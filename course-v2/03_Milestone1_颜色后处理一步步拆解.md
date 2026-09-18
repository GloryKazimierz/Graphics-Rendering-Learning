# 03｜Milestone 1：颜色后处理一步步拆解

## 0. Milestone 1 的位置

这一阶段仍然属于：

**后处理（Post Processing）**

意思是：

> Minecraft 已经把场景画出来了，我们再对最终颜色做处理。

流程：

```text
Scene Color
↓
Exposure
↓
Saturation
↓
Contrast
↓
Temperature / Tint
↓
Grayscale（可选）
↓
Final Output
```

---

# 1. 为什么叫“后处理”

“后”指：

> 几何体和原始场景已经先被画出来。

你没有重新计算：

- 三角形位置
- 物体深度
- 阴影遮挡
- 世界几何

你只是改变最终 RGB。

---

# 2. Exposure：曝光

中文直觉：

> 整体变亮或变暗。

常见：

```glsl
color *= exp2(exposure);
```

---

# 3. 为什么使用 exp2()

```glsl
exp2(x)
```

就是：

[
2^x
]

如果：

```text
exposure=0
```

[
2^0=1
]

所以：

```text
color × 1
→ 不变
```

如果：

```text
exposure=1
```

[
2^1=2
]

整体翻倍。

如果：

```text
exposure=-1
```

[
2^{-1}=0.5
]

整体减半。

---

# 4. Exposure 数字例子

原颜色：

```text
color=(0.2,0.4,0.6)
```

Exposure = 1：

```text
×2
```

得到：

```text
(0.4,0.8,1.2)
```

注意 1.2 超过 1。

这也提醒你：

> 渲染里中间计算不一定必须永远限制在 0..1。

以后 HDR 会继续讲。

---

# 5. Saturation：饱和度

中文直觉：

> 颜色有多鲜艳。

做法通常：

### Step 1

先把原颜色变成灰度：

```glsl
float gray = dot(color, luminanceWeights);
```

### Step 2

在：

```text
灰色
和
原颜色
```

之间 mix。

```glsl
color = mix(vec3(gray), color, saturation);
```

---

# 6. 为什么 saturation=0 是灰色

```glsl
mix(gray, color, 0)
```

按照 mix 定义：

```text
100% gray
0% original color
```

所以灰度。

---

# 7. 为什么 saturation=1 是原色

```glsl
mix(gray, color, 1)
```

变成：

```text
0% gray
100% original
```

所以原色。

---

# 8. Saturation 数字例子

假设：

```text
gray=(0.5,0.5,0.5)
color=(1,0,0)
s=0.5
```

结果：

```text
0.5×gray + 0.5×red
```

得到：

```text
(0.75,0.25,0.25)
```

红色还在，但没有原来那么鲜艳。

---

# 9. Contrast：对比度

中文直觉：

> 亮的更亮，暗的更暗。

常见公式：

```glsl
color = (color - 0.5) * contrast + 0.5;
```

---

# 10. 为什么先减 0.5

因为我们希望围绕：

```text
0.5
```

这个中点进行缩放。

例如：

### 亮颜色

```text
0.7
```

减 0.5：

```text
0.2
```

Contrast=2：

```text
0.2×2=0.4
```

加回 0.5：

```text
0.9
```

变亮。

---

# 11. 暗颜色例子

```text
0.3
```

减 0.5：

```text
-0.2
```

×2：

```text
-0.4
```

+0.5：

```text
0.1
```

变暗。

所以对比度增强。

---

# 12. Temperature：色温

简单理解：

> 画面偏暖还是偏冷。

暖：

```text
更多 Red / Yellow
更少 Blue
```

冷：

```text
更多 Blue
更少 Red
```

它不是“真正物理黑体色温模拟”也没关系。

当前项目阶段更重要的是理解：

> 本质仍然是 RGB 通道的调整。

---

# 13. Tint：色调偏移

常见理解：

```text
Green ↔ Magenta
```

Temperature 和 Tint 是两个不同方向。

你可以把它想成：

```text
Temperature：蓝 ↔ 黄/橙
Tint：绿 ↔ 洋红
```

---

# 14. Grayscale：灰度

一个简单平均：

```text
(R+G+B)/3
```

能得到灰度，但不是最符合人眼感受。

更常见：

```glsl
gray = dot(color, weights);
```

因为人眼对绿色比蓝色敏感。

这里的重点不是背具体权重，而是理解：

> Dot Product 可以用来做“加权求和”。

---

# 15. 为什么默认参数要是 Neutral

默认：

```text
Exposure=0
Saturation=1
Contrast=1
Temperature=0
Tint=0
Grayscale=Off
```

叫：

**中性配置（Neutral / Identity）**

意义：

> 开启 shader 后，如果用户什么都不调，不应该莫名其妙把画面破坏掉。

---

# 16. M1 为什么还不算真正 Lighting

因为它不知道：

```text
这个 pixel 的表面朝哪里
太阳在哪里
有没有物体挡住光
```

它只是：

```text
拿到颜色 → 改颜色
```

所以 M2 才是从“滤镜”跨到“renderer”的关键阶段。

---

# 17. 对应 AuroraShader 思维

你看到任何 M1 代码时，可以问：

```text
输入是什么？
→ scene color

输出是什么？
→ modified color

需要 3D position 吗？
→ 不需要

需要 normal 吗？
→ 不需要

需要 depth 吗？
→ 不需要
```

这就是 Post Processing 的核心特征。

---

# 18. 本章练习

## 手算

1. 原亮度 0.4，Exposure=1，结果是多少？
2. 原亮度 0.4，Exposure=-1，结果是多少？
3. `contrast=2` 时，0.7 变多少？
4. `contrast=2` 时，0.3 变多少？

## 概念

5. 为什么 Exposure=0 是 neutral？
6. 为什么 Saturation 可以用 mix(gray,color,s)？
7. 为什么 Contrast 要围绕 0.5 做缩放？
8. Temperature 和 Tint 有什么直觉区别？

## 项目

9. 为什么 M1 不需要 G-buffer Normal？
10. 为什么 M1 不能知道“柱子后面应该变暗”？

答案：`course-v2/answers/03_答案.md`
