---
id: windowing-transformation

title: 窗口变换
---

窗口变换（_Windowing transformation_）是将二维空间中的矩形（或三维空间中的长方体）变换为另一个矩形（或长方体）的过程。

以三维空间为例，假设：

- 变换前的范围为 $[x_{min}, x_{max}] \times [y_{min}, y_{max}] \times [z_{min}, z_{max}]$ ；
- 变换后的范围为 $[x'_{min}, x'_{max}] \times [y'_{min}, y'_{max}] \times [z'_{min}, z'_{max}]$ 。

对于 $x$ 轴，有：

```math
x_{min} \leqslant x \leqslant x_{max}
```

变换不等式使得该不等式两边等于变换后的范围：

```math
0
\leqslant
\frac{x - x_{min}}{x_{max} - x_{min}}
\leqslant
1
```

```math
x'_{min}
\leqslant
\frac{(x'_{max} - x'_{min})(x - x_{min})}{x_{max} - x_{min}} + x'_{min}
\leqslant
x'_{max}
```

```math
x'_{min}
\leqslant
\frac{x'_{max} - x'_{min}}{x_{max} - x_{min}}x + \frac{x'_{min}x_{max} - x'_{max}x_{min}}{x_{max} - x_{min}}
\leqslant
x'_{max}
```

同理，对于 y 轴和 z 轴，有：

```math
y'_{min}
\leqslant
\frac{y'_{max} - y'_{min}}{y_{max} - y_{min}}y + \frac{y'_{min}y_{max} - y'_{max}y_{min}}{y_{max} - y_{min}}
\leqslant
y'_{max}
```

```math
z'_{min}
\leqslant
\frac{z'_{max} - z'_{min}}{z_{max} - z_{min}}z + \frac{z'_{min}z_{max} - z'_{max}z_{min}}{z_{max} - z_{min}}
\leqslant
z'_{max}
```

变换矩阵为：

```math
\begin{bmatrix}
\frac{x'_{max} - x'_{min}}{x_{max} - x_{min}} & 0 & 0 & \frac{x'_{min}x_{max} - x'_{max}x_{min}}{x_{max} - x_{min}} \\
0 & \frac{y'_{max} - y'_{min}}{y_{max} - y_{min}} & 0 & \frac{y'_{min}y_{max} - y'_{max}y_{min}}{y_{max} - y_{min}} \\
0 & 0 & \frac{z'_{max} - z'_{min}}{z_{max} - z_{min}} & \frac{z'_{min}z_{max} - z'_{max}z_{min}}{z_{max} - z_{min}} \\
0 & 0 & 0 & 1
\end{bmatrix}
```
