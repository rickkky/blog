---
id: orthographic-projection

title: 正射投影
---

正射投影（_Orthographic projection_）将相机空间中的视域体转换为裁剪空间（_Clip space_）中的标准视域体（_Canonical view volume_）。由于正射投影的视域体是一个长方体，因此投影结果不会出现近大远小的透视效果。

假设：

- 相机观察方向为 $+z$ ，向上方向为 $+y$ ；
- 视域体范围为 $[l, r] \times [b, t] \times [n, f]$ ；
- 标准视域体范围为 $[l', r'] \times [b', t'] \times [n', f']$ ；

正射投影实际上是一类的窗口变换，正射投影矩阵（ $P_o$ ）为：

```math
P_o =
\begin{bmatrix}
  \frac{r' - l'}{r - l} & 0 & 0 & \frac{l'r - r'l}{r - l} \\
  0 & \frac{t' - b'}{t - b} & 0 & \frac{b't - t'b}{t - b} \\
  0 & 0 & \frac{f' - n'}{f - n} & \frac{n'f - f'n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

在实际应用中，可能还需要考虑变换前后坐标系变换的问题。在一些常用的图形库或标准（WebGL、WebGPU、...）中，假设：

- 相机空间坐标系为右手系，观察方向为 $-z$ ，向上方向为 $+y$ ；
- 视域体范围为 $[l, r] \times [b, t] \times [-f, -n]$ ；
- 裁剪空间坐标系为左手系，观察方向为 $+z$ ，向上方向为 $+y$ ；
- 标准视域体范围为 $[l', r'] \times [b', t'] \times [n', f']$ ；

注意，因为在相机空间中观察方向为 $-z$ ，因此这里我们假设视域体 $z$ 轴的范围为 $[-f, -n]$ 。在进行窗口变换（ $M_w$ ）之前需要先对坐标系的 $z$ 轴进行翻转。翻转后的视域体 $z$ 轴的范围为 $[n, f]$ 。即：

```math
n \leqslant -z \leqslant f
```

这种情况下，正射投影（ $P_o$ ）可以视为翻转 $z$ 轴与窗口变换的复合变换：

1. 先对 $z$ 轴进行翻转，将视域体在 $z$ 轴上的范围变为 $[n, f]$ ；
1. 再进行窗口变换（ $M_w$ ），将窗口范围 $[l, r] \times [b, t] \times [n, f]$ 变换为标准视域体范围；

```math
P_o
=
\begin{bmatrix}
  \frac{r' - l'}{r - l} & 0 & 0 & \frac{l'r - r'l}{r - l} \\
  0 & \frac{t' - b'}{t - b} & 0 & \frac{b't - t'b}{t - b} \\
  0 & 0 & \frac{f' - n'}{f - n} & \frac{n'f - f'n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
  1 & 0 & 0 & 0 \\
  0 & 1 & 0 & 0 \\
  0 & 0 & -1 & 0 \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

计算得正射投影矩阵为：

```math
P_o =
\begin{bmatrix}
  \frac{r' - l'}{r - l} & 0 & 0 & \frac{l'r - r'l}{r - l} \\
  0 & \frac{t' - b'}{t - b} & 0 & \frac{b't - t'b}{t - b} \\
  0 & 0 & -\frac{f' - n'}{f - n} & \frac{n'f - f'n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

在 WebGL 中，标准视域体范围为 $[-1, 1]$ ，正射投影矩阵为：

```math
P_o
=
\begin{bmatrix}
  \frac{2}{r - l} & 0 & 0 & -\frac{r + l}{r - l} \\
  0 & \frac{2}{t - b} & 0 & -\frac{t + b}{t - b} \\
  0 & 0 & -\frac{2}{f - n} & -\frac{f + n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

在 WebGPU 中，标准视域体范围为 $[-1, 1] \times [-1, 1] \times [0, 1]$ ，正射投影矩阵为：

```math
P_o
=
\begin{bmatrix}
  \frac{2}{r - l} & 0 & 0 & -\frac{r + l}{r - l} \\
  0 & \frac{2}{t - b} & 0 & -\frac{t + b}{t - b} \\
  0 & 0 & -\frac{1}{f - n} & -\frac{n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

> 参考：
>
> - [The Orthographic Projection Matrix - Scratchapixel](https://www.scratchapixel.com/lessons/3d-basic-rendering/perspective-and-orthographic-projection-matrix/orthographic-projection-matrix.html)
> - [Orthographic Projections - Learn WebGL](https://learnwebgl.brown37.net/08_projections/projections_ortho.html)
