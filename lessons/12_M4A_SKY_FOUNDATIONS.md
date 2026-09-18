# 12 — Milestone 4A 预习：Custom Sky 所需要的知识

> 本章是预习。4A 当前仍在开发中，因此不要把这里写成“已确认最终实现”。

## 1. 目标

不再只使用简单背景色，而是让天空颜色取决于：

```text
你看向哪里
太阳在哪里
太阳有多高
是否接近地平线
```

---

## 2. View Direction

对于一个天空 pixel，需要知道：

> camera 从这个 pixel 对应方向看向哪里？

得到：

```glsl
viewDir
```

这是一个单位方向向量。

---

## 3. Camera-stable Sky

错误做法：

```text
用 screenUV.y 决定天空渐变
```

问题：

> 渐变会粘在屏幕上。

正确思想：

```text
screen pixel
↓ reconstruct direction
world/view direction
↓
根据真实方向算 sky
```

这需要逆 camera/projection 变换。

---

## 4. Horizon Factor

如果使用一个“向上”的方向分量：

```text
viewDir.y ≈ 1 → 看天顶
viewDir.y ≈ 0 → 看地平线
```

可以构造：

```glsl
horizon = 1.0 - max(viewDir.y, 0.0);
```

再用：

```glsl
pow(horizon, exponent)
```

控制颜色集中在多靠近 horizon 的区域。

---

## 5. Day Gradient

典型：

```glsl
daySky = mix(zenithBlue, horizonBlue, horizonFactor);
```

这里复用了：

- direction
- mix
- shaping function

---

## 6. Sun Direction

需要：

```glsl
sunDir
```

然后：

```glsl
dot(viewDir, sunDir)
```

判断：

> 当前 pixel 是否靠近太阳方向？

---

## 7. Sun Glow

概念：

```glsl
sunAmount = max(dot(viewDir, sunDir), 0.0);
sunGlow = pow(sunAmount, exponent);
```

指数越大，影响越集中在太阳附近。

注意：

> 这只是 sky color influence，不等于 Bloom。

---

## 8. Sun Elevation

可以从 sun direction 的垂直分量推断：

```text
太阳在天空高处
太阳接近地平线
太阳已经在地平线下
```

这适合控制：

```text
day
sunset
night
```

的 blending。

---

## 9. smoothstep

```glsl
smoothstep(edge0, edge1, x)
```

可以把硬切换：

```text
day → night
```

变成平滑过渡。

这是 procedural rendering 最常见的 shaping tool 之一。

---

## 10. Sunset Directionality

正确 sunset 不应该 360° 全橙。

应大致使用：

```text
sunsetWeight
× horizonFactor
× sunFacingFactor
```

只有：

- 时间接近 sunset
- 位置靠近 horizon
- 方向靠近 sun

时才出现最强暖色。

---

## 11. 本章对应 M2/M3 的旧知识

你会发现天空不是全新世界。

它重新使用：

- vectors
- normalize
- dot product
- coordinate spaces
- inverse matrices
- mix
- pow
- smoothstep

这正是为什么前面的基础必须真正理解。

---

## 12. 能否自己解释

1. 为什么不能简单用屏幕 Y 做天空渐变？
2. `dot(viewDir,sunDir)` 为什么能找到太阳方向？
3. `pow()` 为什么能把 glow 收窄？
4. `smoothstep()` 为什么适合昼夜过渡？
