# 坐标系

## 椭球

椭球表面方程：

$$
\frac{x_s^2}{a^2} + \frac{y_s^2}{b^2} + \frac{z_s^2}{c^2} = 1
$$

球面法向量：

$$
\vec{n}_s = (\frac{x_s}{a^2},\space \frac{y_s}{b^2},\space \frac{z_s}{c^2}) \\
$$

球面单位法向量：

$$
\hat{n}_s = \frac{\vec{n}_s}{\Vert \vec{n}_s \Vert}
$$

## WGS84 坐标系

- 直角坐标系，单位为米（$m$）
- 地球为扁球体，原点为地球质心
- $x$ 轴指向地理坐标 $(0\degree,\space 0\degree)$
- $y$ 轴指向地理坐标 $(90\degree,\space 0\degree)$
- $z$ 轴指向北极点
- 赤道为圆形，半径 $6378137m$
- 球心到极点的距离为 $6356752.3142m$

## 坐标表示

地理坐标：$(\lambda,\space \phi,\space h)$ 表示 $(longitude,\space latitude,\space height)$

直角坐标：$(x_s,\space y_s,\space z_s)$ 表示球面点位的坐标，是点 $(x,\space y,\space z)$ 在球面的投影

~~经纬度与直角坐标的关系可以表示为：~~

$$
\tan{\lambda} = \frac{y_s}{x_s}
$$

$$
\sin{\phi} = \frac{z_n}{\Vert \vec{n}_s \Vert}
$$

~~球面单位法向量可以表示为：~~

$$
\hat{n}_s = (\cos{\lambda}\cos{\phi},\space \sin{\lambda}\cos{\phi},\space \sin{\phi})
$$

# 坐标转换

## 地理坐标转直角坐标

设：

$$
\hat{n}_s
= x_n\hat{i} + y_n\hat{j} + z_n\hat{k}
= \gamma\vec{n}_s
$$

可得：

$$
(x_s,\space y_s,\space z_s)
= (\frac{a^2x_n}{\gamma},\space \frac{b^2y_n}{\gamma},\space \frac{c^2z_n}{\gamma})
$$

带入椭球方程可得：

$$
\gamma = \sqrt{a^2x_n^2 + b^2y_n^2 + c^2z_n^2}
$$

设：

$$
\vec{h} = h\hat{n}_s
$$

可得：

$$
(x,\space y,\space z)
=
(x_s,\space y_s,\space z_s) + \vec{h}
$$
