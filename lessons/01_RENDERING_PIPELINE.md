# 01 — GPU 到底怎么画出一个 Minecraft 方块

## 1. 这一章要解决的问题

看到：

```text
gbuffers_terrain.vsh
gbuffers_terrain.fsh
deferred.fsh
final.fsh
```

你首先应该知道：**它们不是同一时间做同一件事。**

现代实时渲染是一个 pipeline。

---

## 2. 最基础的图形管线

最简化版本：

```text
Vertex Data
   ↓
Vertex Shader
   ↓
Primitive Assembly
   ↓
Rasterization
   ↓
Fragment Shader
   ↓
Framebuffer
```

### Vertex Data

一个三角形至少需要三个顶点。

例如：

```text
A (-1, -1, 0)
B ( 1, -1, 0)
C ( 0,  1, 0)
```

GPU 首先处理这些点。

### Vertex Shader

Vertex Shader 的主要任务：

> 把一个 3D 顶点变换到 GPU 能继续 rasterize 的位置。

概念：

```glsl
gl_Position = projection * view * model * position;
```

对应数学：

\[
p_{clip}=PVMp_{object}
\]

### Primitive Assembly

GPU 把三个处理过的顶点解释成：

```text
triangle
```

### Rasterization

GPU 判断：

> 这个三角形覆盖了屏幕上的哪些像素？

例如：

```text
      ●
     ███
    █████
   ███████
  ●───────●
```

中间的 `█` 就是待处理 fragments。

### Fragment Shader

Fragment shader 决定：

> 这个 fragment 最终输出什么颜色/数据？

例如：

```glsl
fragColor = vec4(1.0, 0.0, 0.0, 1.0);
```

就是红色。

---

## 3. 为什么 AuroraShader 有很多 shader program

Minecraft 不会用一个 shader 画完所有东西。

可以粗略理解为：

```text
terrain
entities
sky
shadow
deferred
final
```

分别负责不同阶段。

因此：

- `gbuffers_*`：主要负责把几何场景写入 G-buffer。
- `deferred.fsh`：在几何信息已经存在后计算光照。
- `shadow.*`：从光源方向生成 shadow map。
- `final.fsh`：最终后处理/输出。

这就是 **Render Pass** 的概念。

---

## 4. 这个知识对应 AuroraShader 哪里

你应该重点观察：

```text
shaders/gbuffers_*.vsh
shaders/gbuffers_*.fsh
shaders/deferred.vsh
shaders/deferred.fsh
shaders/shadow.vsh
shaders/shadow.fsh
shaders/final.fsh
```

重点不是背代码，而是先判断：

> “这段代码属于 Geometry、Shadow、Lighting 还是 Final？”

---

## 5. 删除某一阶段会发生什么

- 没有 vertex transform：几何体不能正确进入屏幕。
- 没有 fragment shader：没有最终 fragment 输出。
- 没有 G-buffer：deferred pass 没有 normal/light data 可读。
- 没有 shadow pass：没有 shadow depth 可比较。
- 没有 final pass：最终颜色处理缺失。

---

## 6. 你要掌握的知识点

- Vertex
- Triangle
- Vertex Shader
- Fragment Shader
- Rasterization
- Framebuffer
- Render Pass
- Pipeline

---

## 7. 能否自己解释

学完后，尝试不用看资料回答：

1. `.vsh` 和 `.fsh` 最大区别是什么？
2. Rasterization 在做什么？
3. 为什么 AuroraShader 不只有一个 fragment shader？
4. `deferred.fsh` 为什么不是用来“画三角形”的？
