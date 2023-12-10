# 推导过程

假设：

- 空间中有两个包围盒 $A$、$B$，需要建立 $A$ 到 $B$ 的投影；
- 包围盒 $A$ 在 $x$、 $y$、 $z$ 轴上的范围分别为 $[A_l, A_r]$、 $[A_b, A_t]$、 $[A_n, A_f]$；
- 包围盒 $B$ 在 $x$、 $y$、 $z$ 轴上的范围分别为 $[B_l, B_r]$、 $[B_b, B_t]$、 $[B_n, B_f]$。

对于 $x$ 轴，有：

```math
A_l \leqslant x \leqslant A_r
```

通过变换使得该不等式两边等于包围盒 $B$ 在 $x$ 轴上的范围：

```math
0
\leqslant
\frac{x - A_l}{A_r - A_l}
\leqslant
1
```

```math
B_l
\leqslant
\frac{(B_r - B_l)(x - A_l)}{A_r - A_l} + B_l
\leqslant
B_r
```

```math
B_l
\leqslant
\frac{B_r - B_l}{A_r - A_l}x + \frac{B_l \cdot A_r - B_r \cdot A_l}{A_r - A_l}
\leqslant
B_r
```

同理，对于 $y$ 轴和 $z$ 轴，有：

```math
B_b
\leqslant
\frac{B_t - B_b}{A_t - A_b}y + \frac{B_b \cdot A_t - B_t \cdot A_b}{A_t - A_b}
\leqslant
B_t
```

```math
B_n
\leqslant
\frac{B_f - B_n}{A_f - A_n}z + \frac{B_n \cdot A_f - B_f \cdot A_n}{A_f - A_n}
\leqslant
B_f
```

变换矩阵为：

```math
\large{
  \begin{bmatrix}
    \frac{B_r - B_l}{A_r - A_l} & 0 & 0 & \frac{B_l \cdot A_r - B_r \cdot A_l}{A_r - A_l} \\
    0 & \frac{B_t - B_b}{A_t - A_b} & 0 & \frac{B_b \cdot A_t - B_t \cdot A_b}{A_t - A_b} \\
    0 & 0 & \frac{B_f - B_n}{A_f - A_n} & \frac{B_n \cdot A_f - B_f \cdot A_n}{A_f - A_n} \\
    0 & 0 & 0 & 1
  \end{bmatrix}
}
```

对于正射投影，可以将其视为场景包围盒到裁剪空间的投影。假设：

- 场景包围盒在 $x$、 $y$、 $z$ 轴上的范围分别为 $[S_l, S_r]$、 $[S_b, S_t]$、 $[S_n, S_f]$；
- 投影前后的 $z$ 轴方向相反。

若裁剪空间各坐标轴的范围为 $[-1, 1]$（WebGL、OpenGL），则变换矩阵为：

```math
\large{
  \begin{bmatrix}
    \frac{2}{S_r - S_l} & 0 & 0 & - \frac{S_l + S_r}{S_r - S_l} \\
    0 & \frac{2}{S_t - S_b} & 0 & - \frac{S_b + S_t}{S_t - S_b} \\
    0 & 0 & - \frac{2}{S_f - S_n} & - \frac{S_n + S_f}{S_f - S_n} \\
    0 & 0 & 0 & 1
  \end{bmatrix}
}
```

若裁剪空间的 $x$ 与 $y$ 轴范围为 $[-1, 1]$，$z$ 轴范围为 $[0, 1]$（WebGPU、Vulkan、DirectX、Metal），则变换矩阵为：

```math
\large{
  \begin{bmatrix}
    \frac{2}{S_r - S_l} & 0 & 0 & - \frac{S_l + S_r}{S_r - S_l} \\
    0 & \frac{2}{S_t - S_b} & 0 & - \frac{S_b + S_t}{S_t - S_b} \\
    0 & 0 & - \frac{1}{S_f - S_n} & - \frac{S_n}{S_f - S_n} \\
    0 & 0 & 0 & 1
  \end{bmatrix}
}
```

> 参考：
>
> 1. [The Orthographic Projection Matrix - Scratchpixel](https://www.scratchapixel.com/lessons/3d-basic-rendering/perspective-and-orthographic-projection-matrix/orthographic-projection-matrix.html)
> 1. [Orthographic Projections - Learn WebGL](https://learnwebgl.brown37.net/08_projections/projections_ortho.html)
