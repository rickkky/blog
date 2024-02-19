# 计算过程

假设：

- 相机放置在原点，朝向 $z$ 轴负方向；相机到视锥体近平面的距离为 $d_n$，远平面的距离为 $d_f$；
- 视锥体在 $y$ 轴方向上的视场角为 $\theta$，宽高比为 $a$；
- 裁剪空间在 $x$、 $y$、 $z$ 轴上的范围分别为 $[C_l, C_r]$、 $[C_b, C_t]$、 $[C_n, C_f]$，且在 $x$、 $y$ 轴上关于原点对称。

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
y_c = \frac{C_t - C_b}{2 y_{max}} y
```

```math
x_c = \frac{C_r - C_l}{2 x_{max}} x
```

代入 $y_{max}$ 和 $x_{max}$ 可得：

```math
y_c = \frac{C_t - C_b}{2 \tan{\frac{\theta}{2}}} y \cdot \frac{1}{-z}
```

```math
x_c = \frac{C_r - C_l}{2a  \tan{\frac{\theta}{2}}} x \cdot \frac{1}{-z}
```

根据齐次坐标的特性，假设经过变换后坐标的 $w$ 值为 $-z$。

对于 $z$ 轴上的变换，假设变换关系为：

```math
z_c = \frac{k z + b}{-z}
```

带入裁剪空间的 $z$ 轴范围可得：

```math
C_n = \frac{- k d_n + b}{d_n}
```

```math
C_f = \frac{- k d_f + b}{d_f}
```

解得：

```math
k = - \frac{C_f d_f - C_n d_n}{d_f - d_n}
```

```math
b = - \frac{(C_f - C_n) d_n d_f}{d_f - d_n}
```

变换矩阵为：

```math
\large{
  \begin{bmatrix}
   \frac{C_r - C_l}{2a  \tan{\frac{\theta}{2}}} & 0 & 0 & 0 \\
    0 & \frac{C_t - C_b}{2 \tan{\frac{\theta}{2}}} & 0 & 0 \\
    0 & 0 & - \frac{C_f d_f - C_n d_n}{d_f - d_n} & - \frac{(C_f - C_n) d_n d_f}{d_f - d_n} \\
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
