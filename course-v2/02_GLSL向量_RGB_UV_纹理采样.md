# 02｜GLSL 向量、RGB、UV 与纹理采样

## 0. 这一章最重要的一句话

GLSL 里你看到的：

```glsl
vec2
vec3
vec4
```

本质就是：

> “装了 2、3、4 个数字的一组数据。”

这些数字可以代表：

- 位置
- 方向
- RGB 颜色
- UV 坐标
- Normal
- 速度

**类型相同，不代表物理意义相同。**

---

# 1. Scalar：标量

Scalar（标量）就是一个数字。

例如：

```glsl
float exposure = 1.0;
```

它只有一个值。

---

# 2. Vector：向量

Vector（向量）是一组数字。

```glsl
vec2 uv;
vec3 color;
vec4 position;
```

---

# 3. vec3 为什么既能是颜色又能是方向

例如：

```glsl
vec3 a = vec3(1.0, 0.0, 0.0);
```

如果当颜色：

```text
R=1 G=0 B=0
→ 红色
```

如果当方向：

```text
X=1 Y=0 Z=0
→ 向 +X 方向
```

GLSL 不知道它“物理上”是什么。

是程序员赋予语义。

---

# 4. RGB 颜色

最常见范围：

```text
0.0 ~ 1.0
```

例如：

```text
黑色 = (0,0,0)
白色 = (1,1,1)
红色 = (1,0,0)
绿色 = (0,1,0)
蓝色 = (0,0,1)
黄色 = (1,1,0)
```

黄色为什么是：

```text
(1,1,0)
```

因为：

```text
Red + Green = Yellow
```

这就是为什么 Aurora Debug Lightmap 中：

```text
R = block light
G = skylight
```

两者都高时会变黄。

---

# 5. vec4 的第四个值是什么

颜色里：

```glsl
vec4(R,G,B,A)
```

A = Alpha（透明度）

常见：

```text
A=1 → 完全不透明
A=0 → 完全透明
```

位置里 vec4 的第四个值通常是：

```text
w
```

后面矩阵章节会讲。

---

# 6. UV 是什么

UV 就是纹理坐标。

通常：

```text
U = 横向
V = 纵向
```

范围：

```text
0..1
```

示意：

```text
V=1   (0,1) -------- (1,1)
       |               |
       |               |
       |               |
V=0   (0,0) -------- (1,0)
        U=0             U=1
```

---

# 7. texture() 到底在做什么

```glsl
vec4 value = texture(myTexture, uv);
```

人话：

> “请去这张纹理的 uv 位置，把那里存的数据给我。”

它读出来的不一定是颜色。

Aurora 里纹理可能存：

```text
Color
Normal
Lightmap
Depth
Shadow Depth
```

---

# 8. 一个纹理采样例子

假设纹理：

```text
左边红色
右边蓝色
```

如果：

```glsl
texture(tex, vec2(0.1,0.5))
```

更靠左，大概率读到红色附近。

如果：

```glsl
texture(tex, vec2(0.9,0.5))
```

更靠右，大概率读到蓝色附近。

---

# 9. mix()：线性插值

```glsl
mix(A, B, t)
```

中文：

> 在 A 和 B 之间按比例混合。

公式：

[
result = A(1-t)+Bt
]

---

# 10. 手算 mix

### 例 1

```text
A=0
B=10
t=0
```

结果：

```text
0
```

### 例 2

```text
t=1
```

结果：

```text
10
```

### 例 3

```text
t=0.25
```

[
0×0.75 + 10×0.25 = 2.5
]

---

# 11. 颜色 mix

```text
A = 红 = (1,0,0)
B = 蓝 = (0,0,1)
t = 0.5
```

结果：

```text
(0.5,0,0.5)
```

就是紫色。

---

# 12. dot()：点积

对于两个向量：

```glsl
dot(A,B)
```

如果：

```text
A=(Ax,Ay,Az)
B=(Bx,By,Bz)
```

那么：

[
A·B = AxBx + AyBy + AzBz
]

---

# 13. dot 手算

```text
A=(1,0,0)
B=(1,0,0)
```

[
1×1+0×0+0×0=1
]

---

```text
A=(1,0,0)
B=(0,1,0)
```

[
1×0+0×1+0×0=0
]

---

# 14. dot 的图形学意义

如果两个都是单位向量：

[
A·B=cos	heta
]

因此：

```text
同方向 → 1
垂直 → 0
反方向 → -1
```

这就是后面：

```text
N · L
```

能表示表面朝不朝太阳的原因。

---

# 15. normalize()

```glsl
normalize(v)
```

中文：

> 把向量长度变成 1，只保留方向。

例如：

```text
v=(0,10,0)
```

长度：

[
10
]

normalize 后：

```text
(0,1,0)
```

---

# 16. 为什么必须 normalize

如果：

```text
N=(0,10,0)
L=(0,1,0)
```

dot：

[
10
]

但我们希望“方向相同”得到：

```text
1
```

所以做方向计算前常常 normalize。

---

# 17. length()

```glsl
length(v)
```

对于：

```text
v=(3,4)
```

长度：

[
sqrt{3^2+4^2}=5
]

这就是勾股定理。

---

# 18. Aurora 最早的 Vignette

假设屏幕中心：

```text
(0.5,0.5)
```

当前 UV：

```text
(0.8,0.5)
```

距离：

```glsl
length(uv - vec2(0.5))
```

越远离中心，就可以越暗。

这里其实就是：

> 2D 向量 + 距离。

---

# 19. 这一章对应 AuroraShader

### M1

- Color = vec3
- Saturation = mix
- Grayscale = dot
- Exposure = scalar × vec3

### M2

- Normal = vec3
- Light Direction = vec3
- NdotL = dot

### M3

- Shadow texture = texture()
- UV offset = vec2

---

# 20. 本章练习

## 手算题

1. `mix(2,10,0.25)` 等于多少？
2. `dot((1,0,0),(0,1,0))` 等于多少？
3. `dot((0,1,0),(0,1,0))` 等于多少？
4. `length((3,4))` 等于多少？

## 概念题

5. 为什么 vec3 可以同时表示 RGB 和 Normal？
6. 为什么方向向量做 dot 前通常要 normalize？
7. texture() 为什么不仅可以读颜色？

## 项目题

8. Aurora Debug Lightmap 中 R 和 G 同时很高，为什么会显示黄色？
9. PCF 为什么需要 vec2 offset？

答案：`course-v2/answers/02_答案.md`
