# 03 — Milestone 1：颜色后处理

## 1. Milestone 1 在整个 renderer 的位置

当时的逻辑基本是：

```text
Minecraft 已经画好的 scene
        ↓
final/post-process
        ↓
Exposure
Saturation
Contrast
Temperature
Tint
Grayscale
        ↓
Screen
```

这一阶段没有真正改变几何、阴影或表面受光关系。

因此它属于：

**Post Processing**

---

## 2. Exposure

常见形式：

```glsl
color *= exp2(exposure);
```

因为摄影中的一档 exposure 常对应亮度翻倍。

### 例子

```text
exposure = 0
2^0 = 1
```

不变。

```text
exposure = 1
2^1 = 2
```

亮度乘 2。

```text
exposure = -1
2^-1 = 0.5
```

亮度减半。

### 对应知识

- 指数函数
- HDR/摄影曝光概念
- scalar × vector

---

## 3. Saturation

先得到 grayscale：

```glsl
float gray = dot(color, luminanceWeights);
```

再：

```glsl
color = mix(vec3(gray), color, saturation);
```

### saturation = 0

完全灰度。

### saturation = 1

原始颜色。

### saturation > 1

比原色更鲜艳。

### 对应知识

- Dot product
- Luminance
- Linear interpolation

---

## 4. Contrast

常见：

```glsl
color = (color - 0.5) * contrast + 0.5;
```

这是围绕 0.5 做缩放。

### 示例

```text
color = 0.7
contrast = 2
```

得到：

```text
(0.7 - 0.5) × 2 + 0.5
= 0.9
```

### 对应知识

- Remapping
- Affine transform

---

## 5. Temperature / Tint

本质是对 RGB 通道做偏移或缩放。

例如：

- 偏暖：R 增、B 减
- 偏冷：B 增、R 减
- Tint：常用于 green/magenta 方向调整

这里重要的不是记某个常量，而是理解：

> 颜色调整本质上仍然是向量运算。

---

## 6. Grayscale

概念：

```glsl
float gray = dot(color, weights);
color = vec3(gray);
```

注意不是简单：

```text
(R+G+B)/3
```

因为人眼对不同波长敏感程度不同。

---

## 7. 为什么默认参数必须是 neutral

AuroraShader 的 M1 默认：

```text
Exposure     0
Saturation   1
Contrast     1
Temperature  0
Tint         0
Grayscale    off
```

这叫：

**Identity / Neutral Configuration**

意味着：

> 开启 shader 但不调参数时，不应该无缘无故破坏原始画面。

---

## 8. M1 给后面的意义

M1 最大作用不是“画面好看”。

它让你熟悉：

- fragment shader
- uniform/options
- color math
- post-processing chain
- debug 颜色问题

后面 M2/M3 才进入真正 3D lighting。

---

## 9. 能否自己解释

1. 为什么 Exposure 用乘法而不是加法更合理？
2. Saturation 为什么适合用 `mix(gray, color, s)`？
3. Contrast 为什么要先减 0.5？
4. 为什么 M1 还不能称作真正的 lighting system？
