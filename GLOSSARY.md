# AuroraShader 图形学术语表

| 术语 | 中文理解 | Aurora 中的例子 |
|---|---|---|
| Vertex | 顶点 | `.vsh` 输入 |
| Fragment | 栅格化后待处理的像素片段 | `.fsh` |
| Rasterization | 三角形覆盖到屏幕像素的过程 | geometry → fragments |
| UV | 纹理二维坐标 | `texture(..., uv)` |
| Texture Sampling | 从纹理读取数据 | color/normal/depth/shadow |
| Normal | 表面方向 | `colortex1.rgb` |
| Normalize | 把向量长度变 1 | lighting 前处理 |
| Dot Product | 两方向接近程度 | `NdotL` |
| G-buffer | 保存几何表面信息的一组纹理 | colortex0/1/2 |
| MRT | 一次 fragment 输出多个目标 | G-buffer 写入 |
| Deferred Rendering | 先存信息，后算 lighting | `deferred.fsh` |
| View Space | 以 camera 为参考的空间 | M2 normal |
| Clip Space | projection 后、除 w 前 | shadow transform |
| NDC | 除 w 后的标准化坐标 | shadow UV 前 |
| Inverse Matrix | 逆变换 | position reconstruction |
| Depth Buffer | 每 pixel 的深度 | `depthtex1` |
| Position Reconstruction | 从 depth 恢复 3D position | `position.glsl` |
| Shadow Map | 光源视角 depth texture | `shadowtex1` |
| Shadow Bias | 避免自阴影的小深度偏移 | 0.0002 默认 |
| Shadow Acne | bias 太小导致黑纹 | M3A artifact |
| Peter Panning | bias 太大导致阴影脱离 | M3A artifact |
| PCF | 多点 shadow compare 后平均 | M3B 3×3 |
| Texel | texture 的一个元素 | shadow map sample spacing |
| Kernel | 一组邻域采样位置 | 3×3 |
| Regression Testing | 新功能不能破坏旧功能 | debug 0–5 验证 |
| Static Validation | 不真正跑 GPU 的结构检查 | validator |
| Runtime Validation | Iris/GPU 实际编译运行 | Minecraft testing |
| mix | 线性插值 | saturation/sky |
| smoothstep | 平滑阈值过渡 | 未来 day/night |
| pow | 非线性 shaping | horizon/sun glow |
