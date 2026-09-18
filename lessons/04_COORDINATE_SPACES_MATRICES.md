# 04 — 坐标空间与矩阵：AuroraShader 最重要的基础

## 1. 为什么图形学里总在说“space”

一个坐标：

```text
(1, 0, 0)
```

单独看没有意义。

必须先问：

> 它在哪个 coordinate space？

常见：

```text
Object Space
World Space
View Space
Clip Space
NDC
Screen Space
```

---

## 2. Object Space

模型自己的局部坐标。

例如一个 cube 的中心：

```text
(0,0,0)
```

无论 cube 被放到世界哪里，它自己的 local center 都可以保持这个值。

---

## 3. World Space

把模型放进游戏世界后的坐标。

```text
Object
   ↓ Model Matrix
World
```

---

## 4. View Space

把世界转换成“以 camera 为中心”的坐标。

```text
World
   ↓ View Matrix
View
```

直觉：

> 在 View Space 中，相机通常可以看成位于原点。

Milestone 2 的 normal 就使用 view-space normal。

---

## 5. Clip Space

Projection matrix 把 view-space position 变成 clip-space：

```text
View
  ↓ Projection
Clip
```

这里还没有完成 perspective divide。

---

## 6. Perspective Divide

```glsl
ndc = clip.xyz / clip.w;
```

得到 NDC。

NDC 常见范围：

```text
x: -1..1
y: -1..1
z: -1..1 或 0..1（依 API/约定）
```

---

## 7. 为什么矩阵顺序重要

概念：

\[
p_{clip}=PVMp_{object}
\]

顺序不能随意交换。

矩阵乘法一般：

\[
AB \neq BA
\]

这与普通数字乘法不同。

---

## 8. Inverse Matrix

如果：

```text
A → B
```

由矩阵 `M` 完成：

```text
B = M A
```

那么：

```text
A = inverse(M) B
```

这就是为什么 Milestone 3A 能：

```text
screen/depth
↓ inverse projection
view position
↓ inverse modelview
player-relative world-oriented position
```

---

## 9. Direction 和 Position 的区别

Position：

```text
在哪里
```

Direction：

```text
朝哪里
```

齐次坐标中常见：

```text
position: w = 1
direction: w = 0
```

因为 translation 应该影响 position，但不应该改变纯方向。

---

## 10. Normal 为什么特殊

普通 position 可以乘 model matrix。

normal 在非均匀缩放下不能简单这样处理。

因此通常需要：

```text
Normal Matrix
```

Aurora M2 使用：

```glsl
gl_NormalMatrix * gl_Normal
```

将 normal 转到 view space。

---

## 11. 对应 AuroraShader

Milestone 2：

- normal → view space

Milestone 3A：

- depth → view position
- view → player-relative world-oriented position
- world/player-relative → shadow light view
- light view → light clip
- light clip → shadow UV/depth

这整个 shadow pipeline 的难点本质上都是 coordinate spaces。

---

## 12. 最容易犯的 bug

### Space mismatch

例如：

```text
N 是 View Space
L 是 World Space
```

直接：

```glsl
dot(N,L)
```

数学上能算，结果却没有物理意义。

### 忘记除 w

```text
clip → NDC
```

必须：

```glsl
xyz /= w;
```

### 相机移动时效果漂移

往往意味着 world/view/player-relative space 混错。

---

## 13. 能否自己解释

1. View Space 的直觉是什么？
2. Clip Space 为什么还不是最终 NDC？
3. inverse matrix 为什么能用来做 position reconstruction？
4. 为什么 `dot()` 两边必须处于同一个 coordinate space？
