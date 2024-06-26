---
id: frustum-from-projection-matrix

title: 根据投影矩阵计算视锥体
---

假设：

- 平面单位法向量为 $\textbf{n}$ ；
- 平面到原点的矢量距离为 $-d$ ；

对于平面上任意一点 $(x, y, z)$ ，有：

```math
\textbf{n} \cdot (x, y, z) = -d
```

则平面方程为：

```math
n_x x + n_y y + n_z z + d = 0
```

当 $d$ 为正数时，表示原点到平面的的方向与法向量方向相反；当 $d$ 为负数时，表示原点到平面的的方向与法向量方向相同。

假设：

- 投影矩阵为 $M$ ，其中的元素以 $m_{ij}$ 表示；
- 投影后的标准视域体范围为 $[-1, 1]$ ；

对于视锥体内的任意一点 $\textbf{p}$ ，其经过投影变换后的坐标为 $(x^\prime, y^\prime, z^\prime)$ ：

```math
x^\prime = \frac{m_{00} x + m_{01} y + m_{02} z + m_{03}}{m_{30} x + m_{31} y + m_{32} z + m_{33}}
```

```math
y^\prime = \frac{m_{10} x + m_{11} y + m_{12} z + m_{13}}{m_{30} x + m_{31} y + m_{32} z + m_{33}}
```

```math
z^\prime = \frac{m_{20} x + m_{21} y + m_{22} z + m_{23}}{m_{30} x + m_{31} y + m_{32} z + m_{33}}
```

考虑视锥体的左平面，若投影前某个点处于视锥体的左平面上，则其投影后的坐标满足 $x^\prime = -1$ ，通过等式变换可得左平面方程：

```math
(m_{00} + m_{30}) x + (m_{01} + m_{31}) y + (m_{02} + m_{32}) z + m_{03} + m_{33} = 0
```

同理可得：

右平面方程：

```math
(m_{00} - m_{30}) x + (m_{01} - m_{31}) y + (m_{02} - m_{32}) z + m_{03} - m_{33} = 0
```

下平面方程：

```math
(m_{10} + m_{31}) x + (m_{11} + m_{31}) y + (m_{12} + m_{32}) z + m_{13} + m_{33} = 0
```

上平面方程：

```math
(m_{10} - m_{31}) x + (m_{11} - m_{31}) y + (m_{12} - m_{32}) z + m_{13} - m_{33} = 0
```

近平面方程：

```math
(m_{20} + m_{32}) x + (m_{21} + m_{32}) y + (m_{22} + m_{32}) z + m_{23} + m_{33} = 0
```

远平面方程：

```math
(m_{20} - m_{32}) x + (m_{21} - m_{32}) y + (m_{22} - m_{32}) z + m_{23} - m_{33} = 0
```

在 WebGPU 中，标准视域体在 $z$ 轴上的范围为 $[0, 1]$ ，近平面方程为：

```math
m_{20} x + m_{21} y + m_{22} z + m_{23} = 0；
```
