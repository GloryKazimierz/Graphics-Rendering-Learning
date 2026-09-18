# 13 — AuroraShader 复习计划

## 第一阶段：只学 GPU 基础

### Day 1
- `01_RENDERING_PIPELINE.md`
- 能画出：
  ```text
  Vertex → Rasterization → Fragment
  ```

### Day 2
- `02_GLSL_VECTORS_UV_COLOR.md`
- 手算：
  - mix
  - dot
  - normalize（简单向量）

### Day 3
- `03_M1_POST_PROCESSING.md`
- 打开 M1 相关 final/color 代码
- 每个 control 用一句话解释

---

## 第二阶段：进入 3D 数学

### Day 4
- `04_COORDINATE_SPACES_MATRICES.md`
- 只要求弄懂：
  ```text
  Object → World → View → Clip → NDC
  ```

### Day 5
- 再读一次矩阵章节
- 重点：
  - inverse matrix
  - perspective divide
  - direction vs position

---

## 第三阶段：Milestone 2

### Day 6
- `05_GBUFFER_DEFERRED.md`
- 自己画 G-buffer 图

### Day 7
- `06_NORMALS_LAMBERT.md`
- 手算：
  ```text
  N=(0,1,0)
  L=(0,1,0)
  dot=?
  ```

### Day 8
- `07_LIGHTMAP_COMPOSITION.md`
- 理解为什么 torch 与 sunlight 不能粗暴相乘

---

## 第四阶段：Milestone 3

### Day 9
- `08_DEPTH_POSITION_RECONSTRUCTION.md`

### Day 10
- `09_M3A_SHADOW_MAPPING.md`
- 画出：
  ```text
  Camera Pixel
  → Reconstructed Position
  → Light Space
  → Shadow UV
  → Depth Compare
  ```

### Day 11
- `10_M3B_PCF.md`
- 手算一个 3×3 kernel 的 visibility

---

## 第五阶段：工程能力

### Day 12
- `11_DEBUGGING_VALIDATION.md`
- 解释 Debug 0–5 各自排查什么

### Day 13
- `12_M4A_SKY_FOUNDATIONS.md`
- 准备理解 4A

---

## 最终检查

如果你能完整解释下面这条链，就已经真正理解了目前 AuroraShader 的骨架：

```text
Geometry
↓
G-buffer
↓
Normal / Light Data
↓
Deferred Directional Lighting
↓
Position Reconstruction
↓
Shadow Mapping
↓
PCF
↓
Final Color Processing
```

下一阶段再继续：

```text
Sky
Fog
HDR/Bloom
Water
SSAO
SSR
Volumetrics
```

不要急着同时学完。
