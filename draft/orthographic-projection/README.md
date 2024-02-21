---
id: orthographic-projection

title: 正射投影
---

正射投影（_Orthographic projection_）实质上是将视域体转换为标准视域体（裁剪空间）的过程，投影结果不会出现透视效果。

假设：

- 相机观察方向为 $-z$ ，向上方向为 $y$ ；
- 视域体范围为 $[l, r] \times [b, t] \times [f, n]$ ；
- 标准视域体范围 $[l', r'] \times [b', t'] \times [f', n']$ 。

正射投影实际上是一类窗口变换，因此投影矩阵为：

```math
\begin{bmatrix}
  \frac{r' - l'}{r - l} & 0 & 0 & \frac{l'r - r'l}{r - l} \\
  0 & \frac{t' - b'}{t - b} & 0 & \frac{b't - t'b}{t - b} \\
  0 & 0 & \frac{n' - f'}{n - f} & \frac{f'n - n'f}{n - f} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

若标准视域体范围为 $[-1, 1]$ ，则变换矩阵为：

```math
\begin{bmatrix}
  \frac{2}{r - l} & 0 & 0 & - \frac{r + l}{r - l} \\
  0 & \frac{2}{t - b} & 0 & - \frac{t + b}{t - b} \\
  0 & 0 & - \frac{2}{n - f} & - \frac{n + f}{n - f} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

若标准视域体范围为 $[-1, 1] \times [-1, 1] \times [0, 1]$ ，则变换矩阵为：

```math
\begin{bmatrix}
  \frac{2}{r - l} & 0 & 0 & - \frac{r + l}{r - l} \\
  0 & \frac{2}{t - b} & 0 & - \frac{t + b}{t - b} \\
  0 & 0 & \frac{1}{n - f} & - \frac{n}{n - f} \\
  0 & 0 & 0 & 1
\end{bmatrix}
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
