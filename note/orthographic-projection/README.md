# 推导过程

假设：

- 场景包围盒在 $x$、 $y$、 $z$ 轴上的范围分别为 $[l, r]$、 $[b, t]$、 $[n, f]$；
- 裁剪空间在 $x$、 $y$、 $z$ 轴上的范围分别为 $[l_c, r_c]$、 $[b_c, t_c]$、 $[n_c, f_c]$。

对于 $x$ 轴，有：

```math
l \leqslant x \leqslant r
```

通过变换使得该不等式两边等于裁剪空间 $x$ 轴范围：

```math
0
\leqslant
\frac{x - l}{r - l}
\leqslant
1
```

```math
l_c
\leqslant
\frac{(r_c - l_c)(x - l)}{r - l} + l_c
\leqslant
r_c
```

```math
l_c
\leqslant
\frac{r_c - l_c}{r - l}x + \frac{l_c \cdot r - r_c \cdot l}{r - l}
\leqslant
r_c
```

同理，对于 $y$ 轴和 $z$ 轴，有：

```math
b_c
\leqslant
\frac{t_c - b_c}{t - b}y + \frac{b_c \cdot t - t_c \cdot b}{t - b}
\leqslant
t_c
```

```math
n_c
\leqslant
\frac{f_c - n_c}{f - n}z + \frac{n_c \cdot f - f_c \cdot n}{f - n}
\leqslant
f_c
```

当变换前后坐标轴方向相反时，需要注意变号。以 $z$ 轴为例：

```math
n_c
\leqslant
- \frac{f_c - n_c}{f - n}z + \frac{n_c \cdot f - f_c \cdot n}{f - n}
\leqslant
f_c
```

当裁剪空间范围为 $[-1, 1]$ 时，有：

```math
-1
\leqslant
\frac{2}{r - l}x - \frac{l + r}{r - l}
\leqslant
1
```

```math
-1
\leqslant
\frac{2}{t - b}y - \frac{b + t}{t - b}
\leqslant
1
```

```math
-1
\leqslant
- \frac{2}{f - n}z - \frac{n + f}{f - n}
\leqslant
1
```

在 WebGPU 中，裁剪空间的 $z$ 轴范围为 $[0, 1]$，有：

```math
0
\leqslant
-\frac{1}{f - n}z - \frac{n}{f - n}
\leqslant
1
```

> 参考：
>
> 1. [The Orthographic Projection Matrix - Scratchpixel](https://www.scratchapixel.com/lessons/3d-basic-rendering/perspective-and-orthographic-projection-matrix/orthographic-projection-matrix.html)
> 1. [Orthographic Projections - Learn WebGL](https://learnwebgl.brown37.net/08_projections/projections_ortho.html)
