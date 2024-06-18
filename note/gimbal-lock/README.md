---
id: gimbal-lock

title: 万向锁
---

定义一个欧拉角旋转：

```math
E(yaw, pitch, roll) = R_z(yaw) R_y(pitch) R_x(roll)
```

假设绕 $x$ 轴旋转的角度为 $\phi$ ，旋转矩阵为：

```math
R_x(\phi)
=
\begin{bmatrix}
1 & 0 & 0 \\
0 & \cos(\phi) & -\sin(\phi) \\
0 & \sin(\phi) & \cos(\phi)
\end{bmatrix}
```

假设绕 $y$ 轴旋转的角度为 $\theta$ ，旋转矩阵为：

```math
R_y(\theta)
=
\begin{bmatrix}
\cos(\theta) & 0 & \sin(\theta) \\
0 & 1 & 0 \\
-\sin(\theta) & 0 & \cos(\theta)
\end{bmatrix}
```

假设绕 $z$ 轴旋转的角度为 $\psi$ ，旋转矩阵为：

```math
R_z(\psi)
=
\begin{bmatrix}
\cos(\psi) & -\sin(\psi) & 0 \\
\sin(\psi) & \cos(\psi) & 0 \\
0 & 0 & 1
\end{bmatrix}
```

计算可得：

```math
R_z(\psi) R_y(\theta) R_x(\phi)
=
\begin{bmatrix}
\cos(\psi) \cos(\theta) & \cos(\psi) \sin(\theta) \sin(\phi) - \sin(\psi) \cos(\phi) & \cos(\psi) \sin(\theta) \cos(\phi) + \sin(\psi) \sin(\phi) \\
\sin(\psi) \cos(\theta) & \sin(\psi) \sin(\theta) \sin(\phi) + \cos(\psi) \cos(\phi) & \sin(\psi) \sin(\theta) \cos(\phi) - \cos(\psi) \sin(\phi) \\
-\sin(\theta) & \cos(\theta) \sin(\phi) & \cos(\theta) \cos(\phi)
\end{bmatrix}
```

当 $\theta = \frac{\pi}{2}$ 时，旋转矩阵为：

```math
\begin{bmatrix}
0 & \cos(\psi) \sin(\phi) - \sin(\psi) \cos(\phi) & \cos(\psi) \cos(\phi) + \sin(\psi) \sin(\phi) \\
0 & \sin(\psi) \sin(\phi) + \cos(\psi) \cos(\phi) & \sin(\psi) \cos(\phi) - \cos(\psi) \sin(\phi) \\
-1 & 0 & 0
\end{bmatrix}
```

对三角函数进行变换可得：

```math
\begin{bmatrix}
0 & \sin(\phi - \psi) & \cos(\phi - \psi) \\
0 & \cos(\phi - \psi) & -\sin(\phi - \psi) \\
-1 & 0 & 0
\end{bmatrix}
```

上面的变换矩阵可以看出，对于 $R_z R_y R_x$ 所表示的旋转变换，当 $\theta = \frac{\pi}{2}$ 时计算出的变换矩阵表示绕单个轴的旋转变换， $\phi$ 和 $\psi$ 最终表现的旋转效果都是绕同一个轴旋转，这种现象称为万向锁。
