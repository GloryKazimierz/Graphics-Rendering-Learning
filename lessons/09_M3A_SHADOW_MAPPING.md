# 09 — Milestone 3A：Basic Shadow Mapping

## 1. N·L 为什么不够

N·L 只能回答：

> 表面朝不朝太阳？

不能回答：

> 太阳和表面之间有没有东西挡住？

所以需要 visibility。

---

## 2. Shadow Map 的核心思想

从光源方向再渲染一次世界。

```text
World
↓
Light View
↓
Depth Only
↓
Shadow Map
```

Aurora 新增：

```text
shaders/shadow.vsh
shaders/shadow.fsh
```

Shadow map：

```text
shadowtex1
```

分辨率：

```text
2048 × 2048
```

固定投影半径：

```text
64 blocks
```

---

## 3. Caster 与 Receiver

### Caster

能把阴影投出去的物体。

例如：

```text
柱子
墙
地形
```

### Receiver

接收阴影的表面。

例如：

```text
地面
墙面
```

M3A 初版优先支持 solid/terrain 等，不追求所有透明类别完美。

---

## 4. Receiver 进入 Light Space

M3A 流程：

```text
depth + screen
↓
camera NDC
↓ gbufferProjectionInverse
View Position
↓ gbufferModelViewInverse
Player-relative world-oriented position
↓ shadowModelView
Light View
↓ shadowProjection
Light Clip
↓ divide w
Light NDC
↓ remap
Shadow UV + Receiver Depth
```

这就是 shadow mapping 的坐标核心。

---

## 5. Depth Comparison

读取：

```text
storedDepth = texture(shadowtex1, shadowUV)
```

比较：

```text
receiverDepth - bias <= storedDepth
```

成立：

```text
visible to light
→ lit
```

不成立：

```text
something closer blocked it
→ shadow
```

---

## 6. Shadow Bias

默认已确认：

```text
0.0002
```

为什么需要？

因为有限精度下，表面可能错误地认为自己挡住自己。

### 太小

出现：

**Shadow Acne**

黑纹/条纹。

### 太大

出现：

**Peter Panning**

阴影和物体底部脱离。

---

## 7. Lighting Composition

正确思路：

```text
ambient + direct × visibility
```

不是：

```text
everything × visibility
```

所以 shadow 内仍然能看到 ambient / block light。

---

## 8. Debug 4

Raw shadow depth。

它展示：

> 光源“看到”的深度数据。

不是 camera 正常画面。

---

## 9. Debug 5

Shadow visibility：

```text
white = lit
black = shadow
```

M3A 时是近似二值结果。

---

## 10. M3A 教你的核心知识

- Extra render pass
- Depth texture
- Light-space transform
- Position reconstruction
- Depth comparison
- Numerical precision
- Shadow artifacts
- Direct vs ambient lighting

---

## 11. 能否自己解释

1. 为什么 shadow map 可以理解成“太阳的 depth camera”？
2. receiverDepth 大于 storedDepth 为什么说明被挡住？
3. Shadow Acne 与 Peter Panning 分别是什么？
4. 为什么 shadow 只乘 direct light？
