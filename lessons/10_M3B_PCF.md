# 10 — Milestone 3B：3×3 PCF Soft Shadows

## 1. Hard Shadow 的问题

M3A 一次只做一个 comparison：

```text
visibility = 0 或 1
```

边缘：

```text
████████│░░░░░░
```

非常硬。

---

## 2. PCF 是什么

PCF：

**Percentage-Closer Filtering**

核心不是平均 depth，而是：

> 在附近多个位置分别做 shadow comparison，再平均 comparison 结果。

---

## 3. 3×3 Kernel

Aurora M3B：

```text
x x x
x x x
x x x
```

9 个样本。

每一个位置：

```text
compare(receiverDepth, storedDepthAtOffset)
```

假设结果：

```text
1 1 1
1 1 0
0 0 0
```

总和：

```text
5
```

visibility：

\[
5/9 \approx 0.556
\]

因此边缘出现 partial visibility。

---

## 4. Texel Size

Shadow texture 为：

```text
2048 × 2048
```

一个 texel 的 UV 大小约：

\[
1/2048
\]

Aurora 用：

```glsl
texelSize = 1 / textureSize(shadowtex1, 0)
```

而不是写死某个 UV 常量。

这是正确的 resolution-aware sampling。

---

## 5. Softness

Aurora M3B：

```text
offset =
(x,y) × softness × texelSize
```

支持：

```text
0
0.5
1
1.5
2
```

### softness = 0

9 个 sample 重合。

结果应接近 Hard。

### softness = 1

标准相邻 texel 3×3。

### softness > 1

采样范围变宽。

注意：

> sample 数仍然只有 9，所以这不是“更高质量”，而只是把 9 个采样点拉开。

---

## 6. 为什么不是平均 depth

错误思想：

```text
先把 9 个 depth 平均
↓
只 compare 一次
```

PCF 正确思想：

```text
9 次 depth comparison
↓
平均 9 个 visibility
```

这是 PCF 名字中 Percentage-Closer 的核心。

---

## 7. 性能

Hard：

```text
1 shadow fetch
```

3×3 PCF：

```text
最多 9 shadow fetches
```

这会明显增加 texture sampling 成本。

这里第一次正式碰到：

**Quality vs Performance Trade-off**

---

## 8. Debug 5 的变化

M3A：

```text
black / white
```

M3B：

```text
black → gray → white
```

gray 表示：

```text
部分 sample lit
部分 sample shadowed
```

---

## 9. 这不是什么

当前 PCF 不是：

- PCSS
- contact-hardening shadow
- cascaded shadow maps
- temporal shadow filtering
- stochastic soft shadow

它只是固定半径的 3×3 filtering。

---

## 10. 能否自己解释

1. 3×3 PCF 为什么是 9 次 compare？
2. 为什么 texel size 要从 textureSize 得到？
3. softness=2 为什么不等于“18 samples”？
4. 为什么 9 次采样会影响 GPU 性能？
