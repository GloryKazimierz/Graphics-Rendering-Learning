# 01｜GPU 渲染管线：从顶点到像素

## 0. 这一章你最终要回答的问题

Minecraft 里的一个方块为什么最后能变成屏幕上的像素？

答案不是“GPU 画出来的”这么简单，而是一条流水线：

```text
顶点数据（Vertex Data）
↓
顶点着色器（Vertex Shader）
↓
图元组装（Primitive Assembly）
↓
光栅化（Rasterization）
↓
片元着色器（Fragment Shader）
↓
帧缓冲（Framebuffer）
↓
屏幕
```

---

# 1. 什么是顶点（Vertex）

顶点可以理解成：

> 3D 几何体的“关键点”。

一个三角形最少 3 个顶点。

例如：

```text
A = (-1, -1, 0)
B = ( 1, -1, 0)
C = ( 0,  1, 0)
```

画出来：

```text
        C
        ●
       / \
      /   \
     /     \
    ●───────●
    A       B
```

GPU 并不是一开始就知道“这是一个三角形”。

程序先把 3 个顶点数据交给 GPU。

---

# 2. 顶点里不仅有位置

一个顶点可能包含：

```text
Position 位置
Normal 法线
UV 纹理坐标
Color 顶点颜色
Tangent 切线
```

例如：

```text
Vertex A
Position = (-1,-1,0)
UV       = (0,0)
Normal   = (0,0,1)
```

这也是为什么 OpenGL 里你会看到：

```text
VBO
Vertex Attribute
glVertexAttribPointer
```

---

# 3. 什么是顶点着色器（Vertex Shader）

顶点着色器的核心任务：

> “这个顶点最后应该出现在屏幕投影空间的哪里？”

概念 GLSL：

```glsl
gl_Position = projection * view * model * position;
```

这里有 4 个东西：

- `position`：模型内部顶点
- `model`：模型放到世界
- `view`：世界变到相机视角
- `projection`：3D 投影到屏幕方向

暂时不要求你现在完全理解矩阵。

先记住：

> Vertex Shader 主要是“处理顶点”。

---

# 4. 一个非常简单的 Vertex Shader

```glsl
#version 330 core

layout(location = 0) in vec3 aPos;

void main()
{
    gl_Position = vec4(aPos, 1.0);
}
```

假设：

```text
aPos = (0.5, 0.2, 0)
```

它就直接把这个位置交给后面的 pipeline。

---

# 5. 什么是图元（Primitive）

图元（Primitive）可以理解为：

> GPU 真正拿来组成形状的基本单位。

常见：

- Point：点
- Line：线
- Triangle：三角形

实时 3D 图形绝大多数最终都会拆成：

```text
Triangle
```

为什么三角形这么重要？

因为 3 个点一定定义一个平面。

四边形可能不共面，但三角形不会有这个问题。

---

# 6. 什么是光栅化（Rasterization）

现在 GPU 有一个三角形：

```text
      ●
     / \
    /   \
   /     \
  ●───────●
```

但屏幕是离散像素：

```text
□ □ □ □ □ □
□ □ □ □ □ □
□ □ □ □ □ □
```

GPU 要回答：

> “这个三角形覆盖了哪些像素？”

这一步叫：

**光栅化（Rasterization）**

结果类似：

```text
□ □ ■ □ □
□ ■ ■ ■ □
■ ■ ■ ■ ■
```

这些被覆盖的位置就会产生 fragment。

---

# 7. Fragment 和 Pixel 是不是完全一样

不是完全一样。

### Fragment（片元）

是：

> “一个候选像素结果”。

它还可能被：

- Depth Test 深度测试
- Alpha Test
- Blending 混合

影响。

### Pixel（像素）

是最后真正写到 framebuffer / screen 的结果。

为了入门，你可以先近似理解：

```text
Fragment ≈ 正在计算的像素
Pixel ≈ 最终显示的像素
```

---

# 8. 什么是片元着色器（Fragment Shader）

Fragment Shader 决定：

> “这个 fragment 输出什么颜色或数据？”

最简单：

```glsl
#version 330 core

out vec4 FragColor;

void main()
{
    FragColor = vec4(1.0, 0.0, 0.0, 1.0);
}
```

结果：

```text
R=1
G=0
B=0
A=1
```

也就是完全不透明红色。

---

# 9. AuroraShader 为什么有很多 .vsh / .fsh

`.vsh`：

> Vertex Shader，顶点着色器

`.fsh`：

> Fragment Shader，片元着色器

但为什么不是只有一对？

因为 Minecraft 会分不同渲染阶段：

```text
Terrain 地形
Entities 实体
Sky 天空
Shadow 阴影
Deferred 延迟光照
Final 最终后处理
```

所以 AuroraShader 会有不同程序。

---

# 10. Render Pass 是什么

Render Pass（渲染阶段）可以理解为：

> “为了某个目的，把场景处理一遍。”

例如：

### Geometry Pass

目的：

> 保存颜色、Normal、Light Data。

### Shadow Pass

目的：

> 从太阳视角保存深度。

### Deferred Pass

目的：

> 读取 G-buffer，再算 lighting。

### Final Pass

目的：

> 做 Exposure / Saturation / Contrast 等最终颜色处理。

---

# 11. 对应 AuroraShader

你现在应该能把这些文件大致分类：

```text
gbuffers_*.vsh/.fsh
→ Geometry / G-buffer

shadow.vsh/.fsh
→ Shadow Pass

deferred.fsh
→ Deferred Lighting

final.fsh
→ Final Post Processing
```

---

# 12. 一个完整的“方块变像素”故事

假设 Minecraft 有一个石头方块。

### Step 1

CPU / Minecraft 提供 mesh 顶点。

### Step 2

Vertex Shader 把顶点转换到屏幕相关空间。

### Step 3

GPU 把三角形组装出来。

### Step 4

Rasterization 找出它覆盖哪些 fragments。

### Step 5

Fragment Shader 决定这些 fragments 写入：

- Color
- Normal
- Light Data

### Step 6

Deferred Pass 再读这些数据算光照。

### Step 7

Final Pass 调整颜色。

### Step 8

最终显示到屏幕。

---

# 13. 常见误区

## 误区 1

“Fragment Shader 负责移动模型。”

错。

移动顶点主要属于 Vertex Shader / transform。

## 误区 2

“Rasterization 就是 Fragment Shader。”

错。

Rasterization 是产生 fragments 的过程。

Fragment Shader 是处理 fragments。

## 误区 3

“一个游戏只会运行一组 shader。”

错。

现代 renderer 通常有很多 pass 和 shader program。

---

# 14. 小例子

一个 triangle 有：

```text
3 个顶点
```

但可能覆盖：

```text
10000 个 fragments
```

因此：

```text
Vertex Shader 运行约 3 次
Fragment Shader 可能运行上万次
```

这也是为什么 fragment shader 里的昂贵操作会影响性能。

---

# 15. 本章练习

## 概念题

1. Vertex Shader 主要处理什么？
2. Fragment Shader 主要处理什么？
3. Rasterization 做什么？
4. 为什么三角形特别常用？
5. Fragment 和最终 Pixel 为什么不完全一样？

## 判断题

6. “`deferred.fsh` 主要负责把三角形顶点投影到屏幕。” 对还是错？
7. “Shadow Pass 可以理解为为了阴影目的再渲染一次场景。” 对还是错？
8. “Fragment Shader 通常比 Vertex Shader 执行次数更多。” 一般情况下对还是错？

## 项目题

9. `shadow.fsh` 属于哪个 pass？
10. `final.fsh` 最适合放 Exposure 还是 Vertex Transform？

答案放在：`course-v2/answers/01_答案.md`
