# 椭球体

球心为原点的球体表面方程：

$$
x^2 + y^2 + z^2 = r^2
$$

球心为原点的椭球体表面方程：

$$
\frac{x^2}{a^2} + \frac{y^2}{b^2} + \frac{z^2}{c^2} = 1
$$

球面单位法向量：

$$
m = (\frac{x}{a^2}, \frac{y}{b^2}, \frac{z}{c^2}) \\
$$

$$
\hat{n} = \frac{m}{\Vert m \Vert}
$$

# WGS84 坐标系

- 直角坐标系，单位为米（$m$）
- 地球为扁球体，原点为地球质心
- $x$ 轴指向地理坐标 $(0\degree, 0\degree)$
- $y$ 轴指向地理坐标 $(90\degree, 0\degree)$
- $z$ 轴指向北极点
- 赤道为圆形，半径 $6378137m$
- 球心到极点的距离为 $6356752.3142m$

# 坐标转换

- 地理坐标：$(\lambda, \phi, h)$ 表示 $(longitude, latitude, height)$
- 笛卡尔坐标：$(x, y, z)$

## 地理坐标转换为直角坐标

首先根据 $(\lambda, \phi)$ 计算球面坐标 $(x_s, y_s, z_s)$：

$$
\hat{n}
= \cos{\lambda}\cos{\phi}\hat{i} + \sin{\lambda}\cos{\phi}\hat{j} + \sin{\phi}\hat{k}
= \hat{n}_x\hat{i} + \hat{n}_y\hat{j} + \hat{n}_z\hat{k}
$$

$$
\vec{n} = \frac{x_s}{a^2}\hat{i} + \frac{y_s}{b^2}\hat{j} + \frac{z_s}{c^2}\hat{k}
$$

设：

$$
\hat{n} = \gamma\vec{n}
$$

从上面的方程可得：

$$
(x_s, y_s, z_s)
= (\frac{a^2\hat{n}_x}{\gamma}, \frac{b^2\hat{n}_y}{\gamma}, \frac{c^2\hat{n}_z}{\gamma})
$$

带入椭球方程可得：

$$
\gamma = \sqrt{a^2\hat{n}_x^2 + b^2\hat{n}_y^2 + c^2\hat{n}_z^2}
$$

设：

$$
\vec{h} = h\hat{n}_s
$$

可得：

$$
\begin{bmatrix}
x \\
y \\
z
\end{bmatrix}
=
\begin{bmatrix}
x_s \\
y_s \\
z_s
\end{bmatrix}
+
\vec{h}
$$
