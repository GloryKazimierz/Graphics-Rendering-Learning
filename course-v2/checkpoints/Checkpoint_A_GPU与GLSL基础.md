# Checkpoint A｜GPU 与 GLSL 基础自测

> 建议先学完第 01、02、03 章再做。不要一边看答案一边做。

## 第一部分：用自己的话解释

1. Vertex Shader 是干什么的？
2. Rasterization 是干什么的？
3. Fragment Shader 是干什么的？
4. vec3 为什么既可以表示 RGB，也可以表示 Normal？
5. texture() 到底是在“做什么”？
6. mix() 的人话解释是什么？
7. normalize() 为什么对方向计算重要？
8. Post Processing 为什么叫“后处理”？

## 第二部分：手算

9. `mix(0,20,0.25)`
10. `dot((1,0,0),(1,0,0))`
11. `dot((1,0,0),(0,1,0))`
12. `length((6,8))`
13. Exposure=1 时，0.3 变多少？
14. Exposure=-1 时，0.8 变多少？
15. Contrast=2 时，0.75 变多少？

## 第三部分：项目判断

16. `final.fsh` 更适合放 Exposure 还是 Model Matrix？
17. `shadow.vsh` 更接近 Geometry/Shadow/Final 哪个阶段？
18. 如果一个效果只读最终 scene color，不读 normal/depth，它更可能属于哪一类？
19. 为什么 Fragment Shader 的性能常常特别重要？
20. Aurora 里为什么一个 texture 不一定代表“图片颜色”？

## 第四部分：你真的理解了吗

尝试不用资料写出：

```text
Vertex Data
↓
?
↓
?
↓
Fragment Shader
↓
Framebuffer
```

然后解释每一步。

答案见：`course-v2/answers/Checkpoint_A_答案.md`
