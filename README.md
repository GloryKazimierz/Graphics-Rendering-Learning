# Graphics & Rendering Learning Notes

这是一个以 **AuroraShader / OpenGL / Vulkan 实际项目为主线** 的实时图形学学习仓库。

> 学习目标不是“代码能跑”，而是能解释：**GPU 在做什么、数学为什么这样写、代码对应渲染管线哪一步、写错会出现什么现象。**

## 推荐入口：中文详细课程 V2

新版课程专门解决旧版“过于简略、英文太多、例子和练习不足”的问题：

- [00｜学习方法与路线图](course-v2/00_学习方法与路线图.md)
- [01｜GPU 渲染管线：从顶点到像素](course-v2/01_GPU渲染管线_从顶点到像素.md)
- [02｜GLSL 向量、RGB、UV 与纹理采样](course-v2/02_GLSL向量_RGB_UV_纹理采样.md)
- [03｜Milestone 1：颜色后处理一步步拆解](course-v2/03_Milestone1_颜色后处理一步步拆解.md)
- [Checkpoint A｜GPU 与 GLSL 基础自测](course-v2/checkpoints/Checkpoint_A_GPU与GLSL基础.md)

新版特点：

1. 中文为主，英文术语放在括号中对照。
2. 每个概念先讲“人话直觉”，再讲数学，再讲 GLSL。
3. 大量数字例子和手算过程。
4. 明确指出 AuroraShader 中对应的阶段/文件。
5. 每章都有概念题、手算题、项目题。
6. 题目答案单独放在 `course-v2/answers/`，避免学习时直接看到。
7. 后续将按同一标准继续扩展 Coordinate Space、G-buffer、Lighting、Shadow、PCF、Sky、Fog 等。

## 旧版快速笔记

`lessons/` 目录保留，作为快速复习版；如果是第一次学习，请优先使用 `course-v2/`。

## Project Mapping

- **AuroraShader Milestone 1** — 颜色后处理（Post Processing）
- **AuroraShader Milestone 2** — G-buffer + 延迟方向光（Deferred Directional Lighting）
- **AuroraShader Milestone 3A** — 基础阴影映射（Shadow Mapping）
- **AuroraShader Milestone 3B** — 3×3 PCF 软阴影
- **Milestone 4A** — Custom Sky（开发/学习中）

## Reference Commits

- Milestone 2: `c181095102991f1627d10c12418e5a9a01925fe9`
- Milestone 3A: `bb7a5d63a76f426dd3af75d413d7dea5a90b5d15`
- Milestone 3B: `5aa154dbce823e954f63d25f91f3acbb9566c9c9`

## 其他资料

- [术语表](GLOSSARY.md)
- [旧版综合练习](EXERCISES.md)
