---
id: perspective-projection

title: 透视投影
---

透视投影（_Perspective projection_）将相机空间中的视域体转换为裁剪空间中的标准视域体。由于透视投影的视域体是一个截锥体，因此投影结果会出现近大远小的透视效果。

透视投影可以视为透视变换和正射投影的复合变换：

1. 透视变换：将视截锥体变换为一个长方体；
2. 正射投影：将长方体变换为裁剪空间中的标准视域体；

对于透视变换，假设：

- 相机放置在 $z=0$ 的 $x-y$ 平面上，观察方向为 $-z$ ，向上方向为 $+y$ ；
- 相机到视截锥体近平面的距离为 $n$，远平面的距离为 $f$ ；
- 视截锥体近平面的范围为 $[l, r] \times [b, t]$ ，远截面的范围为 $[l_f, r_f] \times [b_f, t_f]$ ；
- 经过透视变换后的视域体范围为 $[l, r] \times [b, t] \times [-f, -n]$ ；
- 视截锥体内的点 $(x, y, z)$ 经过透视变换后为 $(x', y', z')$ ；

对于视截锥体沿 $z$ 轴方向上的每一个截面，实质上是对该截面进行窗口变换。根据相似三角形的性质，可得：

```math
x' = \frac{n}{-z} x
```

```math
y' = \frac{n}{-z} y
```

根据齐次坐标的特性，可以将 $-z$ 提取到变换后坐标的 $w$ 分量中。在透视变换中， $z$ 分量的变换与 $x$ 和 $y$ 分量无关。假设变换矩阵为：

```math
M_p
=
\begin{bmatrix}
  n & 0 & 0 & 0 \\
  0 & n & 0 & 0 \\
  0 & 0 & k & c \\
  0 & 0 & -1 & 0
\end{bmatrix}
```

即：

```math
z' = \frac{kz + c}{-z}
```

投影变换在近平面和远平面上保持 $z$ 值不变，代入 $-n$ 和 $-f$ 可得：

```math
-kn + c = -n^2
```

```math
-kf + c = -f^2
```

解得：

```math
k = f + n
```

```math
c = fn
```

透视变换矩阵为：

```math
M_p
=
\begin{bmatrix}
  n & 0 & 0 & 0 \\
  0 & n & 0 & 0 \\
  0 & 0 & f + n & fn \\
  0 & 0 & -1 & 0
\end{bmatrix}
```

对透视变换( $M_p$ )的结果进行正射投影( $P_o$ )即可得到透视投影矩阵（ $P_p$ ）：

```math
P_p = P_o M_p
```

代入正射投影矩阵计算可得，透视投影矩阵为：

```math
P_p
=
\begin{bmatrix}
  \frac{(r' - l')n}{r - l} & 0 & \frac{l'r - r'l}{r - l} & 0 \\
  0 & \frac{(t' - b')n}{t - b} & \frac{b't - t'b}{t - b} & 0 \\
  0 & 0 & -\frac{f'f - n'n}{f - n} & -\frac{(f' - n')fn}{f - n} \\
  0 & 0 & -1 & 0
\end{bmatrix}
```

在实际应用中，通常将相机放置在原点。即：

```math
l = -r
```

```math
b = -t
```

代入可得透视投影矩阵为：

```math
P_p
=
\begin{bmatrix}
  \frac{(r' - l')n}{2r} & 0 & \frac{r' + l'}{2} & 0 \\
  0 & \frac{(t' - b')n}{2t} & \frac{t' + b'}{2} & 0 \\
  0 & 0 & -\frac{f'f - n'n}{f - n} & -\frac{(f' - n')fn}{f - n} \\
  0 & 0 & -1 & 0
\end{bmatrix}
```

还可以使用视场角（_Field of view_）和宽高比（_Aspect ratio_）来描述视截锥体。假设：

- 视截锥体在 $y$ 轴方向上的视场角为 $\theta$ ；
- 视截锥体截面宽高比为 $a$ ；

则：

```math
t = n \tan{\frac{\theta}{2}}
```

```math
r = t a
```

代入可得透视投影矩阵为：

```math
P_p
=
\begin{bmatrix}
  \frac{1}{2a \tan{\frac{\theta}{2}}} & 0 & \frac{r' + l'}{2} & 0 \\
  0 & \frac{1}{2 \tan{\frac{\theta}{2}}} & \frac{t' + b'}{2} & 0 \\
  0 & 0 & -\frac{f'f - n'n}{f - n} & -\frac{(f' - n')fn}{f - n} \\
  0 & 0 & -1 & 0
\end{bmatrix}
```

在 WebGL 中，标准视域体范围为 $[-1, 1]$ ，透视投影矩阵为：

```math
P_p
=
\begin{bmatrix}
  \frac{1}{2a \tan{\frac{\theta}{2}}} & 0 & 0 & 0 \\
  0 & \frac{1}{2 \tan{\frac{\theta}{2}}} & 0 & 0 \\
  0 & 0 & -\frac{f + n}{f - n} & -\frac{2fn}{f - n} \\
  0 & 0 & -1 & 0
\end{bmatrix}
```

在 WebGPU 中，标准视域体范围为 $[-1, 1] \times [-1, 1] \times [0, 1]$ ，正射投影矩阵为：

```math
P_p
=
\begin{bmatrix}
  \frac{1}{2a \tan{\frac{\theta}{2}}} & 0 & 0 & 0 \\
  0 & \frac{1}{2 \tan{\frac{\theta}{2}}} & 0 & 0 \\
  0 & 0 & -\frac{f}{f - n} & -\frac{fn}{f - n} \\
  0 & 0 & -1 & 0
\end{bmatrix}
```

> 参考：
>
> - [The Perspective Projection Matrix - Scratchapixel](https://www.scratchapixel.com/lessons/3d-basic-rendering/perspective-and-orthographic-projection-matrix/opengl-perspective-projection-matrix.html)
