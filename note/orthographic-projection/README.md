---
id: orthographic-projection

title: 正射投影
---

正射投影（_Orthographic projection_）实质上是将视域体转换为标准视域体（_Canonical view volume_）的过程，投影结果不会出现近大远小的透视效果。

假设：

- 相机观察方向为 $-z$ ，向上方向为 $+y$ ；
- 视域体范围为 $[l, r] \times [b, t] \times [n, f]$ ；
- 标准视域体范围 $[l', r'] \times [b', t'] \times [n', f']$ ；
- 变换前坐标系为右手系，变换后为左手系，即变换前后的 $z$ 轴方向相反。

正射投影实际上是一类特殊的窗口变换，投影矩阵为：

```math
\begin{bmatrix}
  \frac{r' - l'}{r - l} & 0 & 0 & \frac{l'r - r'l}{r - l} \\
  0 & \frac{t' - b'}{t - b} & 0 & \frac{b't - t'b}{t - b} \\
  0 & 0 & -\frac{f' - n'}{f - n} & \frac{n'f - f'n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

若标准视域体范围为 $[-1, 1]$ ，则变换矩阵为：

```math
\begin{bmatrix}
  \frac{2}{r - l} & 0 & 0 & -\frac{r + l}{r - l} \\
  0 & \frac{2}{t - b} & 0 & -\frac{t + b}{t - b} \\
  0 & 0 & -\frac{2}{f - n} & -\frac{f + n}{f - n} \\
  0 & 0 & 0 & 1
\end{bmatrix}
```

若标准视域体范围为 $[-1, 1] \times [-1, 1] \times [0, 1]$ ，则变换矩阵为：

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
