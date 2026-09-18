# 11 — Debug View、验证与 Graphics Programmer 的工作方式

## 1. 为什么不能只看“画面好不好看”

Renderer bug 常见来源：

- normal 错
- light data 错
- coordinate space 错
- shadow depth 错
- final grading 错

如果只看最终图像，很难知道哪一步出了问题。

所以需要：

**Debug Visualization**

---

## 2. Aurora 当前 Debug Views

已确认：

```text
0 — Normal Rendering
1 — View-space Normals
2 — Lightmap
3 — NdotL
4 — Raw Shadow Depth
5 — Shadow Visibility
```

---

## 3. Debug 1：Normals

把：

```text
normal.xyz
```

编码到：

```text
RGB
```

目的：

> 检查 normal 是否存在、方向是否合理。

---

## 4. Debug 2：Lightmap

```text
R = block light
G = sky light
```

目的：

> 检查 Minecraft lighting data 是否正确进入 G-buffer。

---

## 5. Debug 3：NdotL

灰度：

```text
0 → black
1 → white
```

目的：

> 直接检查 directional diffuse math。

---

## 6. Debug 4：Shadow Depth

目的：

> 确认 shadow pass 真的画出了 depth。

如果这里本身就是空的，问题不在 deferred shadow compare，而在 caster/shadow pass。

---

## 7. Debug 5：Visibility

目的：

> 检查 depth comparison / PCF 输出。

M3A：

```text
0/1
```

M3B：

```text
0..1
```

---

## 8. Regression Testing

每加一个功能，都必须确认旧功能没坏。

例如 M3B 不只测试 PCF，还要确认：

- Hard mode 仍等于 M3A
- Debug 1–4 仍正常
- Shadows Off 仍能回到 M2
- M1 color controls 仍正常

这叫：

**Regression Testing**

---

## 9. Static Validation vs Runtime Validation

### Static Validation

例如：

- include 存在
- shader pair 完整
- settings 有效
- 文本结构正确
- 某些数值测试通过

### Runtime Validation

真正让 Iris/GPU：

```text
compile
link
execute
render
```

两者不能混为一谈。

“validator passed” 不等于 “shader runtime correct”。

---

## 10. 编译错误的价值

M2 曾出现：

```text
undefined variable RGBA16
undefined variable RG16
```

这种错误让你学到：

> Shader pack metadata / engine directives 与真正 GLSL 代码有边界。

Debugging 不是项目失败，而是图形开发的一部分。

---

## 11. 推荐 Debug 顺序

以后新效果坏了：

```text
1. 是否 compile？
2. 输入 buffer 是否正确？
3. coordinate space 是否一致？
4. 中间数学值是否正确？
5. 最终 composition 是否正确？
6. 才看“美术参数是否好看”
```

---

## 12. 能否自己解释

1. 为什么 debug buffer 比直接看最终画面更可靠？
2. Static validation 为什么不能代替 Minecraft runtime test？
3. Regression testing 在 renderer 里为什么特别重要？
