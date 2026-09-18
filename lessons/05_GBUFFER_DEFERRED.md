# 05 — Milestone 2：G-buffer 与 Deferred Rendering

## 1. Forward 与 Deferred 的区别

### Forward

```text
Geometry
↓
立即算 lighting
↓
最终颜色
```

### Deferred

```text
Geometry
↓
先把表面信息存起来
↓
G-buffer
↓
之后统一 lighting
```

AuroraShader M2 进入的就是后者。

---

## 2. AuroraShader 当前 G-buffer

已确认设计：

```text
colortex0
→ existing scene color + alpha

colortex1 RGBA16
→ encoded view-space normal RGB
→ validity A

colortex2 RG16
→ block light R
→ sky light G
```

---

## 3. 为什么要存 Normal

如果之后才算光照，你必须知道当前 pixel 的表面朝向。

所以：

```text
Geometry pass
↓
Normal 写入 texture
↓
Deferred pass 再读回来
```

---

## 4. 为什么要存 Lightmap

Minecraft 原本已经有自己的 block light / sky light 信息。

Aurora 不应该粗暴丢掉它们。

因此单独保存：

```text
R = block light
G = sky light
```

之后 lighting pass 再决定如何组合。

---

## 5. MRT 是什么

MRT：

**Multiple Render Targets**

一次 geometry fragment shader 可以同时输出到多个 framebuffer attachment。

概念：

```text
一个 fragment
  ├→ colortex0 color
  ├→ colortex1 normal
  └→ colortex2 light data
```

这就是 deferred renderer 的典型结构。

---

## 6. Normal encoding

Normal 原本：

```text
[-1,1]
```

纹理存储：

```text
[0,1]
```

编码：

```glsl
encoded = N * 0.5 + 0.5;
```

解码：

```glsl
N = encoded * 2.0 - 1.0;
N = normalize(N);
```

---

## 7. Validity Alpha

`colortex1.a` 作为 validity：

> 这个 pixel 是否真的有有效表面 normal？

这样 background/invalid pixel 不会被当成正常几何参与 lighting。

---

## 8. Deferred Pass 在做什么

大致：

```text
read scene color
read normal
read block/sky light
get sun/light direction
compute lighting
output lit color
```

这比 M1 多了一个关键维度：

> 不再只看“颜色是什么”，而开始看“这个表面在 3D 世界里是什么状态”。

---

## 9. 对应文件

已确认相关：

```text
shaders/deferred.fsh
shaders/lib/normal.glsl
shaders/lib/gbuffer_write.glsl
shaders/lib/lighting.glsl
shaders/lib/lighting_settings.glsl
```

以及 terrain/block/entity 的 G-buffer fragment entry points。

---

## 10. 能否自己解释

1. G-buffer 为什么叫 buffer？
2. 为什么 Deferred 要先“存信息”再算光？
3. `colortex1.a` validity 有什么意义？
4. 为什么 normal buffer 不是直接存最终颜色？
