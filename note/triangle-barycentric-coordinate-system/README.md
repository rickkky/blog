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

# 重心坐标的计算

## 解方程组

可以通过解方程组的方式求解重心坐标：

```math
\begin{cases}
x_p = (1 - \beta - \gamma) x_a + \beta x_b + \gamma x_c \\
y_p = (1 - \beta - \gamma) y_a + \beta y_b + \gamma y_c
\end{cases}
```

## 根据重心坐标的几何特性计算

也可以通过重心坐标的几何特性进行求解。面积比可以转化为点到直线距离的比值。以 $\beta$ 为例：

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

## 根据向量点积计算

重心坐标等式可以变换为：

```math
(\textbf{p} - \textbf{a}) = \beta (\textbf{b} - \textbf{a}) + \gamma (\textbf{c} - \textbf{a})
```

将该等式简化为：

```math
\vec{v_{ap}} = \beta \vec{v_{ab}} + \gamma \vec{v_{ac}}
```

等式两边同时点乘 $\vec{v_{ab}}$ 或 $\vec{v_{ac}}$ ，可得：

```math
v_{ab} \cdot v_{ap} = \beta v_{ab} \cdot v_{ab} + \gamma v_{ab} \cdot v_{ac}
```

```math
v_{ac} \cdot v_{ap} = \beta v_{ab} \cdot v_{ac} + \gamma v_{ac} \cdot v_{ac}
```

解得：

```math
\beta = \frac{(v_{ac} \cdot v_{ac}) (v_{ab} \cdot v_{ap}) - (v_{ab} \cdot v_{ac}) (v_{ac} \cdot v_{ap})}{(v_{ab} \cdot v_{ab}) (v_{ac} \cdot v_{ac}) - v_{ab} \cdot v_{ac} (v_{ab} \cdot v_{ac})}
```

```math
\gamma = \frac{(v_{ab} \cdot v_{ab}) (v_{ac} \cdot v_{ap}) - (v_{ab} \cdot v_{ac}) (v_{ab} \cdot v_{ap})}{(v_{ab} \cdot v_{ab}) (v_{ac} \cdot v_{ac}) - (v_{ab} \cdot v_{ac}) (v_{ab} \cdot v_{ac})}
```

## 根据向量叉积计算

重心坐标等式可以变换为：

```math
\beta \vec{v_{ab}} + \gamma \vec{v_{ac}} - \vec{v_{ap}} = \vec{0}
```

即：

```math
\beta x_{ab} + \gamma x_{ac} - x_{ap} = 0
```

```math
\beta y_{ab} + \gamma y_{ac} - y_{ap} = 0
```

转换为矩阵乘法形式：

```math
[\beta, \gamma, 1]
\begin{bmatrix}
  x_{ab} \\
  x_{ac} \\
  x_{pa}
\end{bmatrix}
= 0
```

```math
[\beta, \gamma, 1]
\begin{bmatrix}
  y_{ab} \\
  y_{ac} \\
  y_{pa}
\end{bmatrix}
= 0
```

这表示向量 $(\beta, \gamma, 1)$ 垂直于向量 $(x_{ba}, x_{ca}, x_{ap})$ 和 $(y_{ba}, y_{ca}, y_{ap})$ 。可以通过向量叉积计算同时垂直于两个向量的向量，可得：

```math
\begin{bmatrix}
  \beta \\ \gamma \\ 1
\end{bmatrix}
=
k
\begin{bmatrix}
  x_{ab} \\ x_{ac} \\ x_{pa}
\end{bmatrix}
\times
\begin{bmatrix}
  y_{ab} \\ y_{ac} \\ y_{pa}
\end{bmatrix}
```

其中 $k$ 为向量叉积结果的第三个分量，将其视为齐次坐标最终进行归一化即可得到重心坐标。
