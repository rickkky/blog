---
id: triangle-barycentric-coordinate-system

title: 三角形重心坐标系
---

在二维平面内，根据三角形的三个顶点 $\textbf{a}$ 、 $\textbf{b}$ 、 $\textbf{c}$ 建立一个非正交坐标系（重心坐标系），坐标系原点为 $\textbf{a}$ ，基向量分别为 $\textbf{b} - \textbf{a}$ 和 $\textbf{c} - \textbf{a}$ 。平面内任意一点 $\textbf{p}$ 可表示为：

```math
\textbf{p} = \textbf{a} + \beta (\textbf{b} - \textbf{a}) + \gamma (\textbf{c} - \textbf{a})
```

即：

```math
\textbf{p} = (1 - \beta - \gamma) \textbf{a} + \beta \textbf{b} + \gamma \textbf{c}
```

通常情况下，会定义一个 $\alpha$ 来简化等式：

```math
\alpha \equiv 1 - \beta - \gamma
```

则点 $\textbf{p}$ 的重心坐标可表示为：

```math
\textbf{p} (\alpha, \beta, \gamma) = \alpha \textbf{a} + \beta \textbf{b} + \gamma \textbf{c}
```

当重心坐标满足以下条件时，点 $\textbf{p}$ 在三角形内：

```math
\begin{cases}
0 \lt \alpha \lt 1 \\
0 \lt \beta \lt 1 \\
0 \lt \gamma \lt 1 \\
\end{cases}
```

当其中一个分量为 $0$ 时， $\textbf{p}$ 在三角形的边界上；当两个分量为 $0$ 时， $\textbf{p}$ 在三角形的顶点上。

## 重心坐标的计算

### 解方程组

可以通过解方程组的方式求解重心坐标：

```math
\begin{cases}
x_p = (1 - \beta - \gamma) x_a + \beta x_b + \gamma x_c \\
y_p = (1 - \beta - \gamma) y_a + \beta y_b + \gamma y_c
\end{cases}
```

### 根据重心坐标的几何特性计算

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

### 根据向量点乘计算

重心坐标等式可以变换为：

```math
(\textbf{p} - \textbf{a}) = \beta (\textbf{b} - \textbf{a}) + \gamma (\textbf{c} - \textbf{a})
```

将该等式简化为：

```math
\vec{v_p} = \beta \vec{v_b} + \gamma \vec{v_c}
```

等式两边同时点乘 $\vec{v_b}$ 或 $\vec{v_c}$ ，可得：

```math
dot_{bp} = \beta dot_{bb} + \gamma dot_{bc}
```

```math
dot_{cp} = \beta dot_{bc} + \gamma dot_{cc}
```

解得：

```math
\beta = \frac{dot_{cc} dot_{bp} - dot_{bc} dot_{cp}}{dot_{bb} dot_{cc} - dot_{bc} dot_{bc}}
```

```math
\gamma = \frac{dot_{bb} dot_{cp} - dot_{bc} dot_{bp}}{dot_{bb} dot_{cc} - dot_{bc} dot_{bc}}
```
