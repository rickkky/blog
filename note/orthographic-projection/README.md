# 推导过程

假设：

1. 场景包围盒的范围在 $x$、 $y$、 $z$ 轴上分别为 $[left, right]$、 $[bottom, top]$、 $[near, far]$；
1. 裁剪空间范围为 $[-1, 1]$；
1. 场景包围盒所在坐标系与裁剪空间坐标系各坐标轴方向相同。

对于 $x$ 轴，有：

```math
left \leqslant x \leqslant right
```

通过变换使得该不等式两边等于裁剪空间 $x$ 轴范围，可得：

```math
-1
\leqslant
\frac{2}{right - left}x - \frac{left + right}{right - left}
\leqslant
1
```

同理，对于 $y$ 轴和 $z$ 轴，有：

```math
-1
\leqslant
\frac{2}{top - bottom}y - \frac{bottom + top}{top - bottom}
\leqslant
1
```

```math
-1
\leqslant
\frac{2}{far - near}z - \frac{near + far}{far - near}
\leqslant
1
```

当变换前后坐标轴方向相反时，需要注意变号。以 $z$ 轴为例：

```math
near \leqslant -z \leqslant far
```

```math
-1
\leqslant
-\frac{2}{far - near}z - \frac{near + far}{far - near}
\leqslant
1
```

在 WebGPU 中，裁剪空间的 $z$ 轴范围为 $[0, 1]$：

```math
0
\leqslant
-\frac{1}{far - near}z - \frac{near}{far - near}
\leqslant
1
```

> 参考：
>
> 1. [The Orthographic Projection Matrix - Scratchpixel](https://www.scratchapixel.com/lessons/3d-basic-rendering/perspective-and-orthographic-projection-matrix/orthographic-projection-matrix.html)
> 1. [Orthographic Projections - Learn WebGL](https://learnwebgl.brown37.net/08_projections/projections_ortho.html)
