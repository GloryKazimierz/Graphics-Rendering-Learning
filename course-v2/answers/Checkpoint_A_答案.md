# Checkpoint A 答案｜GPU 与 GLSL 基础

1. Vertex Shader：处理顶点，尤其是位置变换。
2. Rasterization：把三角形覆盖范围转换为 fragments。
3. Fragment Shader：处理每个 fragment 的输出数据。
4. vec3 只是 3 个数，语义由使用方式决定。
5. texture()：从纹理指定坐标读取数据。
6. mix()：在两个值之间按比例线性混合。
7. normalize()：把方向向量长度变成 1，让方向比较不受原始长度影响。
8. 因为它发生在场景主要渲染完成之后，对结果颜色做二次处理。
9. `mix(0,20,0.25)=5`。
10. 1。
11. 0。
12. 10。
13. 0.6。
14. 0.4。
15. `(0.75-0.5)×2+0.5=1.0`。
16. Exposure。
17. Shadow 阶段。
18. 更可能属于 Post Processing。
19. 因为 fragment 数量通常远高于 vertex 数量，一个昂贵操作可能被执行数百万次。
20. 因为 GPU texture 本质是二维数据容器，可以存颜色、法线、深度、光照等各种数据。

完整链：

```text
Vertex Data
↓
Vertex Shader
↓
Primitive Assembly / Rasterization
↓
Fragment Shader
↓
Framebuffer
```

如果你能解释每一步“输入什么、输出什么、为什么需要”，就算真正通过本 checkpoint。
