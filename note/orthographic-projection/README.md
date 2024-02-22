---
id: orthographic-projection

title: 正射投影
---

正射投影（_Orthographic projection_）将相机空间中的视域体转换为裁剪空间（_Clip space_）中的标准视域体（_Canonical view volume_）。由于正射投影的视域体是一个长方体，因此投影结果不会出现近大远小的透视效果。

假设：

- 相机观察方向为 $+z$ ，向上方向为 $+y$ ；
- 正射投影的视域体是一个长方体，范围为 $[l, r] \times [b, t] \times [n, f]$ ；
- 标准视域体范围为 $[l', r'] \times [b', t'] \times [n', f']$ ；

正射投影实际上是一类的窗口变换，投影矩阵为：

```math
\begin{bmatrix}
  \frac{r' - l'}{r - l} & 0 & 0 & \frac{l'r - r'l}{r - l} \\
  0 & \frac{t' - b'}{t - b} & 0 & \frac{b't - t'b}{t - b} \\
  0 & 0 & \frac{f' - n'}{f - n} & \frac{n'f - f'n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

在实际应用中，可能还需要考虑变换前后坐标系变换的问题。在一些常用的图形库或标准（WebGL、WebGPU、...）中，假设：

- 相机空间坐标系为右手系；
- 相机观察方向为 $-z$ ，向上方向为 $+y$ ；
- 裁剪空间坐标系为左手系。

这种情况下需要对 $z$ 轴进行翻转，投影矩阵为：

```math
\begin{bmatrix}
  \frac{r' - l'}{r - l} & 0 & 0 & \frac{l'r - r'l}{r - l} \\
  0 & \frac{t' - b'}{t - b} & 0 & \frac{b't - t'b}{t - b} \\
  0 & 0 & -\frac{f' - n'}{f - n} & \frac{n'f - f'n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

在 WebGL 中，标准视域体范围为 $[-1, 1]$ ，投影矩阵为：

```math
\begin{bmatrix}
  \frac{2}{r - l} & 0 & 0 & -\frac{r + l}{r - l} \\
  0 & \frac{2}{t - b} & 0 & -\frac{t + b}{t - b} \\
  0 & 0 & -\frac{2}{f - n} & -\frac{f + n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

在 WebGPU 中，标准视域体范围为 $[-1, 1] \times [-1, 1] \times [0, 1]$ ，投影矩阵为：

```math
\begin{bmatrix}
  \frac{2}{r - l} & 0 & 0 & -\frac{r + l}{r - l} \\
  0 & \frac{2}{t - b} & 0 & -\frac{t + b}{t - b} \\
  0 & 0 & -\frac{1}{f - n} & -\frac{n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

> 参考：
>
> 1. [The Orthographic Projection Matrix - Scratchpixel](https://www.scratchapixel.com/lessons/3d-basic-rendering/perspective-and-orthographic-projection-matrix/orthographic-projection-matrix.html)
> 1. [Orthographic Projections - Learn WebGL](https://learnwebgl.brown37.net/08_projections/projections_ortho.html)
