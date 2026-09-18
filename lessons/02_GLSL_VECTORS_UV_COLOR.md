# 02 — GLSL 基础：vec、UV、RGB、texture、mix、dot

## 1. vec2 / vec3 / vec4 是什么

GLSL 中：

```glsl
vec2
vec3
vec4
```

就是 2、3、4 维向量。

例如：

```glsl
vec3 color = vec3(0.2, 0.7, 0.3);
```

如果它表示颜色：

```text
R = 0.2
G = 0.7
B = 0.3
```

如果它表示位置：

```text
X = 0.2
Y = 0.7
Z = 0.3
```

**vec3 本身没有“颜色”或“位置”的含义。含义取决于你怎么使用它。**

---

## 2. UV 坐标

纹理通常使用：

```text
(0,0) → (1,1)
```

二维坐标。

概念：

```text
(0,1) -------- (1,1)
  |              |
  |              |
  |              |
(0,0) -------- (1,0)
```

读取纹理：

```glsl
vec4 texel = texture(sceneTexture, uv);
```

这叫：

**Texture Sampling**

---

## 3. texture() 到底做什么

它回答：

> 在这张纹理的 UV 位置上，保存了什么值？

它不一定只读“颜色”。

AuroraShader 中 texture 还可能存：

- normal
- block light
- sky light
- depth
- shadow depth

所以 texture 可以理解成：

> GPU 上的一张二维数据表。

---

## 4. mix()

GLSL：

```glsl
mix(A, B, t)
```

数学：

\[
(1-t)A+tB
\]

例子：

```text
A = 0
B = 10
t = 0.25

结果 = 2.5
```

颜色也一样：

```glsl
vec3 result = mix(grayColor, originalColor, saturation);
```

---

## 5. dot()

点积：

```glsl
dot(A, B)
```

对于单位向量：

\[
A\cdot B=\cos\theta
\]

因此：

- 同方向 → 1
- 垂直 → 0
- 反方向 → -1

AuroraShader 中它会用于：

- grayscale luminance 加权
- N·L directional lighting
- 未来 viewDir·sunDir 天空方向控制

---

## 6. normalize()

```glsl
normalize(v)
```

把向量长度变成 1，只留下方向。

例如：

```text
(0, 10, 0)
```

normalize 后：

```text
(0, 1, 0)
```

方向没有变。

---

## 7. length()

```glsl
length(v)
```

返回向量长度：

\[
|v|=\sqrt{x^2+y^2+z^2}
\]

你最早的 vignette 就可用：

```glsl
float d = length(uv - vec2(0.5));
```

求 pixel 到屏幕中心的距离。

---

## 8. clamp / max / min

非常常见：

```glsl
max(x, 0.0)
```

可以把负值切掉。

N·L 就常写：

```glsl
float NdotL = max(dot(N, L), 0.0);
```

---

## 9. 这一章对应项目

最早实验：

- grayscale
- vignette
- cool tint

Milestone 1：

- exposure
- saturation
- contrast
- temperature
- tint

Milestone 2：

- `dot(N,L)`
- `normalize(N)`

Milestone 3：

- texture sampling shadow map

---

## 10. 能否自己解释

1. 为什么 `vec3` 既可以是 RGB，也可以是方向？
2. `texture()` 为什么不仅能读颜色？
3. `mix(A,B,0)` 和 `mix(A,B,1)` 分别是什么？
4. 为什么做 lighting 前常常 normalize？
