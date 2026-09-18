# AuroraShader 小练习

这些题不要求一次做完。目标是让你从“看懂解释”变成“自己能推导”。

---

## A. GLSL / Vector

### 1

```text
A = 2
B = 10
t = 0.25
```

计算：

```glsl
mix(A,B,t)
```

### 2

```text
N = (0,1,0)
L = (0,1,0)
```

计算 `dot(N,L)`。

### 3

```text
N = (0,1,0)
L = (1,0,0)
```

计算 `dot(N,L)`。

### 4

为什么 `(0,10,0)` 和 `(0,1,0)` 表示同一个方向，但直接用于 dot 可能产生不同结果？

---

## B. Milestone 1

### 5

若：

```text
color = 0.7
contrast = 1.5
```

使用：

```text
(color - 0.5) × contrast + 0.5
```

算结果。

### 6

为什么 saturation=0 应该得到 grayscale，而 saturation=1 应恢复 original color？

---

## C. G-buffer

### 7

给一个 pixel：

```text
color = green
normal = up
blocklight = 0
skylight = 1
```

分别应该存到 Aurora 哪个 colortex？

### 8

为什么 normal 适合存在单独 texture，而不是直接混到 scene color 中？

---

## D. Coordinate Spaces

### 9

解释：

```text
Object → World → View → Clip → NDC
```

每一步的含义。

### 10

为什么：

```text
View-space N
```

不能直接和：

```text
World-space L
```

做 dot？

---

## E. Shadow Mapping

### 11

如果：

```text
storedDepth = 0.40
receiverDepth = 0.55
```

忽略 bias，receiver 是否在 shadow？

### 12

如果：

```text
storedDepth = 0.60
receiverDepth = 0.55
```

是否在 shadow？

### 13

解释为什么“太阳可以看成另一台 camera”。

---

## F. PCF

### 14

9 次 compare：

```text
1 1 1
1 1 0
0 0 0
```

visibility 是多少？

### 15

为什么 softness 从 1 改成 2 不会增加 sample 数？

---

## G. Debugging

### 16

如果 Debug 4 shadow depth 完全为空，但 Debug 1 normal 正常，你首先应该查：

```text
shadow pass
还是
normal pass
```

为什么？

### 17

如果 Debug 5 visibility 正确，但最终画面 shadow 不明显，问题更可能在哪个阶段？

---

# 答案建议

先不要立刻看答案。自己写一遍。

参考：

1. 4
2. 1
3. 0
4. 未 normalize 时长度参与 dot 结果
5. 0.8
6. 因为 `mix(gray, color, t)` 在 t=0/1 时分别返回两端
7. color→colortex0，normal→colortex1，light→colortex2
8. deferred lighting 需要独立读取几何信息
9. 见坐标空间章节
10. 两个向量不在同一基底，dot 没有正确物理意义
11. shadow
12. lit
13. 它也用 view/projection 把世界投影并保存 depth
14. 5/9 ≈ 0.556
15. softness 只放大 offset，kernel 仍是 3×3
16. shadow pass
17. lighting composition/final integration
