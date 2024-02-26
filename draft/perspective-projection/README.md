---
id: perspective-projection

title: 透视投影
---

透视投影（_Perspective projection_）将相机空间中的视域体转换为裁剪空间中的标准视域体。由于透视投影的视域体是一个截锥体，因此投影结果会出现近大远小的透视效果。

# 计算方法

## 透视变换

可以将透视投影的过程分为两步，先进行透视变换，再进行正射投影。

1. 透视变换：将视截锥体变换为一个长方体；
2. 正射投影：将长方体变换为裁剪空间中的标准视域体。

假设：

- 相机观察方向为 $-z$ ，向上方向为 $+y$ ；
- 相机到视截锥体近平面的距离为 $n$，远平面的距离为 $f$ ；
- 视截锥体近平面的范围为 $[l, r] \times [b, t]$ ，远截面的范围为 $[l_f, r_f] \times [b_f, t_f]$ ；
- 变换后的视域体范围为 $[l, r] \times [b, t] \times [f, n]$ ；
- 视截锥体内的点 $(x, y, z)$ 变换后为 $(x', y', z')$ 。

对于视截锥体沿 $z$ 轴方向上的每一个截面，实质上是对该截面进行窗口变换。根据相似三角形的性质，可得：

```math
x' = \frac{n}{-z} x
```

```math
y' = \frac{n}{-z} y
```

根据齐次坐标的特性，可以将 $z$ 提取到变换后坐标的 $w$ 分量中。在透视变换中， $z$ 分量的变换与 $x$ 和 $y$ 分量无关。假设变换矩阵为：

```math
M_p
=
\begin{bmatrix}
  n & 0 & 0 & 0 \\
  0 & n & 0 & 0 \\
  0 & 0 & a & b \\
  0 & 0 & -1 & 0
\end{bmatrix}
```

即：

```math
z' = \frac{az + b}{-z}
```

投影变换在近平面和远平面上保持 $z$ 值不变，代入 $-n$ 和 $-f$ 可得：

```math
-an + b = -n^2
```

```math
-af + b = -f^2
```

解得：

```math
a = f + n
```

```math
b = fn
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

假设：

- 相机观察方向为 $-z$ ，向上方向为 $+y$ ；
- 视域体范围为 $[l, r] \times [b, t] \times [f, n]$ ；
- 标准视域体范围为 $[l', r'] \times [b', t'] \times [n', f']$ ；

透视投影矩阵为：

```math
P_p
=
\begin{bmatrix}
  \frac{(r' - l')n}{r - l} & 0 & \frac{l'r - r'l}{r - l} & 0 \\
  0 & \frac{(t' - b')n}{t - b} & \frac{b't - t'b}{t - b} & 0 \\
  0 & 0 & -\frac{f'f - n'n}{f - n} & -\frac{(f' - n')fn}{f - n} \\
  0 & 0 & 1 & 0
\end{bmatrix}
```

---

假设：

- 相机放置在原点，朝向 $z$ 轴负方向；相机到视锥体近平面的距离为 $d_n$，远平面的距离为 $d_f$；
- 视锥体在 $y$ 轴方向上的视场角为 $\theta$，宽高比为 $a$；
- 裁剪空间在 $x$、 $y$、 $z$ 轴上的范围分别为 $[l', r']$、 $[b', t']$、 $[n', f']$，且在 $x$、 $y$ 轴上关于原点对称。

当 $z$ 值确定时，视锥体切面上点的 $x$、 $y$ 范围为：

```math
-y_{max} \leqslant y \leqslant y_{max}
```

```math
-x_{max} \leqslant x \leqslant x_{max}
```

```math
y_{max} = -z \tan{\frac{\theta}{2}}
```

```math
x_{max} = y_{max} a
```

将不等式两边变换为裁剪空间范围可得：

```math
y_c = \frac{t' - b'}{2 y_{max}} y
```

```math
x_c = \frac{r' - l'}{2 x_{max}} x
```

代入 $y_{max}$ 和 $x_{max}$ 可得：

```math
y_c = \frac{t' - b'}{2 \tan{\frac{\theta}{2}}} y \cdot \frac{1}{-z}
```

```math
x_c = \frac{r' - l'}{2a  \tan{\frac{\theta}{2}}} x \cdot \frac{1}{-z}
```

根据齐次坐标的特性，假设经过变换后坐标的 $w$ 值为 $-z$。

对于 $z$ 轴上的变换，假设变换关系为：

```math
z_c = \frac{k z + b}{-z}
```

带入裁剪空间的 $z$ 轴范围可得：

```math
n' = \frac{- k d_n + b}{d_n}
```

```math
f' = \frac{- k d_f + b}{d_f}
```

解得：

```math
k = - \frac{f' d_f - n' d_n}{d_f - d_n}
```

```math
b = - \frac{(f' - n') d_n d_f}{d_f - d_n}
```

变换矩阵为：

```math
\large{
  \begin{bmatrix}
   \frac{r' - l'}{2a  \tan{\frac{\theta}{2}}} & 0 & 0 & 0 \\
    0 & \frac{t' - b'}{2 \tan{\frac{\theta}{2}}} & 0 & 0 \\
    0 & 0 & - \frac{f' d_f - n' d_n}{d_f - d_n} & - \frac{(f' - n') d_n d_f}{d_f - d_n} \\
    0 & 0 & -1 & 0
  \end{bmatrix}
}
```

若裁剪空间各坐标轴的范围为 $[-1, 1]$（WebGL、OpenGL），则变换矩阵为：

```math
\large{
  \begin{bmatrix}
   \frac{1}{a  \tan{\frac{\theta}{2}}} & 0 & 0 & 0 \\
    0 & \frac{1}{\tan{\frac{\theta}{2}}} & 0 & 0 \\
    0 & 0 & - \frac{d_f + d_n}{d_f - d_n} & - \frac{2 d_n d_f}{d_f - d_n} \\
    0 & 0 & -1 & 0
  \end{bmatrix}
}
```

若裁剪空间的 $x$ 与 $y$ 轴范围为 $[-1, 1]$， $z$ 轴范围为 $[0, 1]$（WebGPU、Vulkan、DirectX、Metal），则变换矩阵为：

```math
\large{
  \begin{bmatrix}
   \frac{1}{a  \tan{\frac{\theta}{2}}} & 0 & 0 & 0 \\
    0 & \frac{1}{\tan{\frac{\theta}{2}}} & 0 & 0 \\
    0 & 0 & - \frac{d_f}{d_f - d_n} & - \frac{d_n d_f}{d_f - d_n} \\
    0 & 0 & -1 & 0
  \end{bmatrix}
}
```
