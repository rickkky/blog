---
id: matrix-representation-with-code

title: 矩阵的代码表示
---

以行主序（row-major order）表示的三维矩阵为例：

```math
\begin{bmatrix}
  x_1 & x_2 & x_3 \\
  y_1 & y_2 & y_3 \\
  z_1 & z_2 & z_3
\end{bmatrix}
```

在代码中，通常以列主序（colum-major order）的一维数组形式表示：

```
[
  x1, y1, z1,
  x2, y2, z2,
  x3, y3, z3,
]
```
