---
id: triangle-barycentric

title: 重心坐标
---

在二维平面内，根据三角形的三个顶点 $\bold a$ 、 $\bold b$ 、 $\bold c$ 建立一个非正交坐标系（重心坐标系）。坐标系原点为 $\bold a$ ，基向量分别为 $\bold b - \bold a$ 和 $\bold c - \bold a$ 。平面内任意一点 $\bold p$ 可表示为：

```math
\bold p = \bold a + \beta (\bold b - \bold a) + \gamma (\bold c - \bold a)
```

即：

```math
\bold p = (1 - \beta - \gamma) \bold a + \beta \bold b + \gamma \bold c
```

通常情况下，会定义一个 $\alpha$ 来简化等式：

```math
\alpha \equiv 1 - \beta - \gamma
```

则点 $\bold p$ 的重心坐标可表示为：

```math
\bold p (\alpha, \beta, \gamma) = \alpha \bold a + \beta \bold b + \gamma \bold c
```

当重心坐标满足以下条件时，点 $\bold p$ 在三角形内：

```math
\begin{cases}
0 \lt \alpha \lt 1 \\
0 \lt \beta \lt 1 \\
0 \lt \gamma \lt 1 \\
\end{cases}
```

当其中一个分量为 $0$ 时， $\bold p$ 在三角形的边界上；当两个分量为 $0$ 时， $\bold p$ 在三角形的顶点上。

可以通过解方程组的方式求解重心坐标：

```math
\begin{cases}
x_p = (1 - \beta - \gamma) x_a + \beta x_b + \gamma x_c \\
y_p = (1 - \beta - \gamma) y_a + \beta y_b + \gamma y_c
\end{cases}
```

也可以通过重心坐标的几何特性进行求解。以 $\beta$ 为例：

```math
\beta = \frac{f_{ac}(x_p, y_p)}{f_{ac}(x_b, y_b)}
```

计算可得：

```math
\beta
=
\frac{(y_c - y_a) x_p + (x_a - x_c) y_p + x_c y_a - x_a y_c}{(y_c - y_a) x_b + (x_a - x_c) y_b + x_c y_a - x_a y_c}
```

同理：

```math
\gamma = \frac{f_{ab}(x_p, y_p)}{f_{ab}(x_c, y_c)}
```

有：

```math
\gamma
=
\frac{(y_b - y_a) x_p + (x_a - x_b) y_p + x_b y_a - x_a y_b}{(y_b - y_a) x_c + (x_a - x_b) y_c + x_b y_a - x_a y_b}
```
