# 07 — Minecraft Lightmap 与 Lighting Composition

## 1. 为什么不能只算太阳光

Minecraft 本身已经有：

- Skylight
- Block light（torch 等）

如果 Aurora 只做：

```text
color *= NdotL
```

洞穴和火把场景会被破坏。

---

## 2. Aurora 的 light data

M2：

```text
colortex2.r = block light
colortex2.g = sky light
```

Debug 2：

```text
R → block light
G → sky light
```

因此：

- 户外白天常偏绿
- torch 附近偏红
- 二者同时高 → 黄

---

## 3. Lighting Weight

Aurora M2 已确认大意：

```text
weight =
lightingStrength
× skyLevel
× (1 - blockLevel)
```

直觉：

### skylight 高

说明这个位置更适合受到 Aurora directional sunlight 影响。

### block light 高

说明这里主要由 torch 等 Minecraft 局部光源照明。

因此减少额外 directional 调制，避免重复/错误压暗。

---

## 4. 为什么这叫 Composition

真实/复杂 renderer 往往有：

```text
Ambient
Direct
Indirect
Emissive
Local Lights
Sky
...
```

不能把所有东西混成一个值后再乱乘。

Aurora 从 M2 开始已经在做最基础的：

**Lighting Decomposition / Composition**

---

## 5. M3 为什么 shadow 只作用 Direct

M3 采用：

```text
ambient + direct × visibility
```

而不是：

```text
(ambient + direct) × visibility
```

因为阴影代表：

> 主方向光被遮挡。

不代表 torch、ambient 全部消失。

---

## 6. 能否自己解释

1. 为什么 torch 附近不应该直接乘太阳的 NdotL？
2. 为什么 shadow 不应该把 ambient 一起乘黑？
3. Debug lightmap 为什么用 RGB 显示数据很方便？
