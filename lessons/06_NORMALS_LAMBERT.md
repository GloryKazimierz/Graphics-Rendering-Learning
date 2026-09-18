# 06 — Normal、Dot Product 与 Lambert Lighting

## 1. Normal 是什么

Normal：

> 表面的朝向。

例如 cube：

```text
top    → (0, 1, 0)
bottom → (0,-1, 0)
right  → (1, 0, 0)
```

---

## 2. Light Direction

Directional light 可以用一个方向向量表示：

```glsl
L = normalize(lightDirection);
```

Aurora M2 使用 Iris 提供的 `shadowLightPosition` 作为主光方向来源，并在 view space 中使用。

---

## 3. N·L

```glsl
float NdotL = dot(N, L);
```

对于单位向量：

\[
N\cdot L=\cos\theta
\]

### N 与 L 同向

```text
theta = 0
cos(0) = 1
```

最亮。

### 垂直

```text
theta = 90°
cos = 0
```

无直射 diffuse。

### 背向

```text
theta > 90°
dot < 0
```

所以：

```glsl
NdotL = max(dot(N,L), 0.0);
```

---

## 4. Lambert Diffuse

最基本 diffuse 模型：

\[
L_d \propto \max(N\cdot L,0)
\]

它表达：

> 表面越正对光源，单位面积接收到的光越多。

---

## 5. Aurora M2 的实际形式

已确认：

```text
Lambert = 0.65 + 0.55 × NdotL
```

因此：

```text
NdotL = 0
Lambert = 0.65
```

```text
NdotL = 1
Lambert = 1.20
```

这是一个“保留环境亮度 + 增强朝光面”的教学型近似。

不是完整 PBR。

---

## 6. 为什么 debug NdotL 很有用

Debug 3：

```text
black → NdotL low
white → NdotL high
```

如果转动 camera 后，世界表面的受光关系错误地跟着 camera 走，就说明：

- normal space 可能错
- light direction space 可能错
- 二者没有处于同一空间

---

## 7. View-space normal 为什么会随着 camera 转而变色

Debug normal 把：

```text
XYZ → RGB
```

而 normal 在 View Space。

相机旋转后，同一个 world-space 表面的 view-space 表示会改变。

所以 debug RGB 改变是正常的。

关键是：

> lighting 计算中 N 和 L 也都处于同一个 view space，所以相对关系仍可保持正确。

---

## 8. 能否自己解释

1. 为什么 `dot()` 可以表示表面朝不朝光？
2. 为什么 `max(...,0)` 必须存在？
3. 为什么 normal debug 颜色变了，不代表 lighting 一定错？
4. Lambert 为什么不需要知道具体距离？（Directional light）
