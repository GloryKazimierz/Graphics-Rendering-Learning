# 08 — Depth 与 Position Reconstruction

## 1. M3A 遇到的问题

Shadow test 需要知道：

> 当前屏幕 pixel 在光源看来在哪里？

但 deferred pass 并没有额外存一个完整 world-position texture。

Aurora 选择：

> 从 depth + screen coordinate 重建 position。

这叫：

**Position Reconstruction**

---

## 2. Depth Buffer 是什么

渲染场景时，每个 pixel 通常会保存 depth。

它帮助 GPU 判断：

> 哪个 fragment 更靠近 camera？

Aurora M3A 使用 `depthtex1` 作为位置重建输入。

---

## 3. Step 1：Screen UV → NDC

屏幕 UV：

```text
0..1
```

NDC x/y：

```text
-1..1
```

典型：

```glsl
vec2 ndcXY = uv * 2.0 - 1.0;
```

深度也要根据实际 API/Iris 约定转换到正确范围。

---

## 4. Step 2：NDC/Clip → View Position

使用：

```text
gbufferProjectionInverse
```

概念：

```glsl
viewH = gbufferProjectionInverse * clip;
view = viewH.xyz / viewH.w;
```

重点：

```text
/ w
```

不能忘。

---

## 5. Step 3：View → Player-relative World-oriented

使用：

```text
gbufferModelViewInverse
```

得到一个适合送入 shadow matrices 的 player-relative/world-oriented position。

Aurora 当前方案不需要再加绝对 `cameraPosition`。

---

## 6. 为什么不直接存 Position Buffer

额外 position texture 会：

- 占显存
- 增加 bandwidth
- 增加 G-buffer 成本

而 depth 已经存在，所以：

> Reconstruction 可以用计算换存储。

这是一种典型 graphics trade-off。

---

## 7. 这个知识以后还会去哪

位置重建会再次用于：

- SSAO
- SSR
- volumetric lighting
- deferred decals
- screen-space ray marching

所以 M3A 的这部分非常重要。

---

## 8. 最常见 bug

### 忘记 perspective divide

会导致 position 完全不对。

### depth convention 错

0..1 与 -1..1 搞混。

### matrix 顺序错

得到的位置会随着 camera 诡异漂移。

### space mismatch

重建出 View Space，却拿去直接和 World/Light Space 数据比较。

---

## 9. 能否自己解释

1. 为什么 depth + inverse projection 可以恢复 view position？
2. 为什么 Aurora 不一定需要单独 position texture？
3. 为什么 reconstruction 是“计算换 bandwidth”？
