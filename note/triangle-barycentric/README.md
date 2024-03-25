---
id: triangle-barycentric

title: 重心坐标
---

假设：

- 三角形 $ABC$ 的三个顶点坐标分别为 $A(x_0, y_0)$, $B(x_1, y_1)$, $C(x_2, y_2)$ ；
- 三角形平面内任意一点 $P(x, y)$ ；
- $P$ 关于三角形 $ABC$ 的重心坐标为 $(\alpha, \beta, \gamma)$ ；

重心坐标满足以下关系：

```math
\alpha + \beta + \gamma = 1
```

则重心坐标可表示为 $(1- \beta - \gamma, \beta, \gamma)$ 。

代入坐标值，可得等式关系：

```math
\begin{cases}
x = (1 - \beta - \gamma) x_0 + \beta x_1 + \gamma x_2 \\
y = (1 - \beta - \gamma) y_0 + \beta y_1 + \gamma y_2
\end{cases}
```

解得：

```math
\begin{cases}
\beta = \frac{(x - x_0)(y_2 - y_0) - (y - y_0)(x_2 - x_0)}{(x_1 - x_0)(y_2 - y_0) - (y_1 - y_0)(x_2 - x_0)} \\
\gamma = \frac{(x - x_0)(y_1 - y_0) - (y - y_0)(x_1 - x_0)}{(x_2 - x_0)(y_1 - y_0) - (y_2 - y_0)(x_1 - x_0)}
\end{cases}
```

即：

```math
\begin{bmatrix}
\beta \\ \gamma \\ 1
\end{bmatrix}
=
\begin{bmatrix}
x_1 - x_0 \\ x_2 - x_0 \\ x - x_0
\end{bmatrix}
\times
\begin{bmatrix}
y_1 - y_0 \\ y_2 - y_0 \\ y - y_0
\end{bmatrix}
```
