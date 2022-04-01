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
\hat{n}_s = \frac{\vec{n}_s}{\Vert\vec{n}_s\Vert}
$$

## WGS84 坐标系

- 直角坐标系，单位为米（$m$）
- 地球为扁球体，原点为地球质心
- $x$ 轴指向地理坐标 $(0\degree,\space 0\degree)$
- $y$ 轴指向地理坐标 $(90\degree,\space 0\degree)$
- $z$ 轴指向北极点
- 赤道为圆形，半径 $6378137m$
- 球心到极点的距离为 $6356752.3142m$

## 地理坐标

地理坐标 $(\lambda,\space \phi,\space h)$ 用经度、纬度、高程来表示一个点的位置。

对于一个 $(a,\space b,\space c)$ 表示的椭球，使用它的球面法向量来定义地理坐标的经纬度。法向量在 $xy$ 平面的投影与 $x$ 轴的夹角表示经度；法向量与 $xy$ 平面的夹角表示纬度。对于 WGS84 椭球，由于 $a=b$，所以这里使用球面法向量来定义经度与通过子午面夹角来定义经度实际上是等价的。

球面单位法向量可以用经纬度表示：

$$
\hat{n}_s = (\cos{\lambda}\cos{\phi},\space \sin{\lambda}\cos{\phi},\space \sin{\phi})
$$

## 直角坐标

对于空间中的任意一点 $P\space(x,\space y,\space z)$，球面上某一点 $(x_s,\space y_s,\space z_s)$ 与它的连线垂直于球面，称该方向的向量为球面法向量 $\vec{n}_s$；$OP$ 连线与球面的交点表示为为 $(x_c,\space y_c,\space z_c)$，称该方向的向量为球心法向量 $\hat{n}_c$。

# 坐标转换

## 地理坐标转直角坐标

设：

$$
\hat{n}_s
= (x_n,\space y_n,\space z_n)
= \gamma\vec{n}_s
$$

可得：

$$
(x_s,\space y_s,\space z_s)
= (\frac{a^2x_n}{\gamma},\space \frac{b^2y_n}{\gamma},\space \frac{c^2z_n}{\gamma})
$$

代入椭球方程可得：

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

## 直角坐标转地理坐标

### 根据球面坐标计算经纬度

$$
\lambda = \arctan(\frac{y_n}{x_n})
~\\
~\\
\phi = \arcsin{\frac{z_n}{\Vert\vec{n}_s\Vert}}
$$

### 沿球心法向量方向缩放到球面

设：

$$
(x_c,\space y_c,\space z_c) = \beta(x,\space y,\space z)
$$

代入球面方程可得：

$$
\beta = \frac{1}{\sqrt{\frac{x^2}{a^2} + \frac{y^2}{b^2} + \frac{z^2}{c^2}}}
$$

### 根据球面法向量用迭代法计算球面坐标

设：

$$
\vec{r} = (x,\space y,\space z)
~\\
~\\
\vec{r}_s = (x_s,\space y_s,\space z_s)
~\\
~\\
\vec{h} = \alpha\vec{n}_s
~\\
~\\
$$

有：

$$
\vec{r} = \vec{r}_s + \vec{h}
$$

可以解得：

$$
x_s = \frac{x}{1 + \frac{\alpha}{a^2}}
~\\
~\\
y_s = \frac{y}{1 + \frac{\alpha}{b^2}}
~\\
~\\
z_s = \frac{z}{1 + \frac{\alpha}{c^2}}
$$

代入球面方程得：

$$
\frac{x^2}{a^2(1 + \frac{\alpha}{a^2})^2}
+ \frac{y^2}{b^2(1 + \frac{\alpha}{b^2})^2}
+ \frac{z^2}{c^2(1 + \frac{\alpha}{c^2})^2}
- 1
= 0
$$

在这个方程中 $\alpha$ 是唯一的未知数。

假设 $(x_c,\space y_c,\space, z_c)$ 与 $(x_s,\space y_s,\space z_s)$ 重合，则可以计算 $\alpha$ 的初始值：

$$
\alpha_0 = (1 - \beta)\frac{\Vert\vec{r}\Vert}{\Vert\vec{n}_s\Vert}
$$

设：

$$
S
=
\frac{x^2}{a^2(1 + \frac{\alpha}{a^2})^2}
+ \frac{y^2}{b^2(1 + \frac{\alpha}{b^2})^2}
+ \frac{z^2}{c^2(1 + \frac{\alpha}{c^2})^2}
- 1
$$

则有：

$$
S'
=
-2
\Big[
\frac{x^2}{a^4(1 + \frac{\alpha}{a^2})^3}
+ \frac{y^2}{b^4(1 + \frac{\alpha}{b^2})^3}
+ \frac{z^2}{c^4(1 + \frac{\alpha}{c^2})^3}
\Big]
$$

可得：

$$
\Delta\alpha = \frac{S(\alpha_n)}{S'(\alpha_n)}
~\\
~\\
\alpha_{n+1} = \alpha_n - \Delta\alpha
$$

给定一个足够接近于 0 的阈值 $\epsilon$，当 $|S| < \epsilon$ 时，迭代结束。

对于地球扁球体，这种方法收敛速度快，并且不需要任何三角函数的计算。这种方法并不适用于非常靠近球心的点，因为可能有多个解（Don't know why），但是在实际应用中这种情况很少见。
