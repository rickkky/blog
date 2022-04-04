# 向量

- 物理：空间中的箭头
- 计算机：有序的数字列表
- 数学：抽象的数学符号 $\vec{v}$，只要保证向量加法运算与数乘运算是有意义的即可

## 加法

$\vec{v}+\vec{w}$ 表示从原点先沿着 $\vec{v}$ 移动，然后沿着 $\vec{w}$ 移动得到的向量。

$$
\begin{bmatrix} x_1 \\ y_1 \end{bmatrix}
+
\begin{bmatrix} x_2 \\ y_2 \end{bmatrix}
=
\begin{bmatrix} x_1 + x_2 \\ y_1 + y_2 \end{bmatrix}
$$

## 数乘

$C\vec{v}$ 表示沿 $\vec{v}$ 的方向（若 $C$ 为负数则为反方向）对其缩放 $C$ 倍得到的向量。

$$
C\begin{bmatrix} x \\ y \end{bmatrix}
=
\begin{bmatrix} C x \\ C y \end{bmatrix}
$$

## 线性组合

$a\vec{v}+b\vec{w}$ 称为 $\vec{v}$ 与 $\vec{w}$ 的线性组合。

$a\vec{v}+b\vec{w}$ 可以表示的所有向量的集合称为 $\vec{v}$ 与 $\vec{w}$ 张成的空间。

当一组向量中的某个向量可以表示为其他向量的线性组合，则称这些向量是线性相关的。否则称为线性无关。

向量空间中任意一组线性无关的向量可以作为该空间的一组基向量。

# 矩阵

## 线性变换

线性变换需要满足两个条件：原点保持不变；直线在变换前后仍为直线。

矩阵表示一个线性变换，矩阵向量乘法的结果表示向量经过线性变换后得到的新向量。若将任何向量都视为一组基向量的线性组合，矩阵的每一列可以看作是一个变换后的基向量，矩阵向量乘法的结果仍然表示变换后的基向量的相同的线性组合。

$$
\vec{v} = x\vec{i} + y\vec{j}
~\\
~\\
T
=
\begin{bmatrix} T\vec{i} & T\vec{j} \end{bmatrix}
=
\begin{bmatrix} a & b \\ c & d \end{bmatrix}
~\\
~\\
\begin{bmatrix} a & b \\ c & d \end{bmatrix}
\begin{bmatrix} x \\ y \end{bmatrix}
=
x\begin{bmatrix} a \\ c \end{bmatrix}
+ y\begin{bmatrix} b \\ d \end{bmatrix}
=
\begin{bmatrix} ax + by \\ cx + dy \end{bmatrix}
$$

## 矩阵乘法

矩阵乘法表示两个线性变换的复合变换。

$$
\begin{bmatrix} a & b \\ c & d \end{bmatrix}
\begin{bmatrix} e & f \\ g & h \end{bmatrix}
=
\begin{bmatrix} ae + bg & af + bh \\ ce + dg & cf + dh \end{bmatrix}
$$

性质：

$$
M_{1}M_{2}\not=M_{2}M_{1}
~\\
~\\
(M_{1}M_{2})M_{3}=M_{1}(M_{2}M_{3})
$$

## 行列式

矩阵的行列式表示经过线性变换以后空间定量以及空间定向的变化。

- 行列式的绝对值大小表示空间定量的缩放倍数，空间定量在二维空间中表示面积，在三维空间中表示体积。
- 行列式的正负号表示空间定向的是否发生变化，空间定向在二维空间中表示 $x$ 轴与 $y$ 轴的左右关系，在三维空间中表示坐标系的手性。
- 行列式的值为 0 表示线性变换将空间压缩到了更低的维度。

$$
\det\Big(\begin{bmatrix} a & b \\ c & d \end{bmatrix}\Big)
=
ad - bc
~\\
~\\
\det\Bigg(\begin{bmatrix} a & b & c \\ d & e & f \\ g & h & i \end{bmatrix}\Bigg)
=
a\det\Big(\begin{bmatrix} e & f \\ h & i \end{bmatrix}\Big)
- b\det\Big(\begin{bmatrix} d & f \\ g & i \end{bmatrix}\Big)
+ c\det\Big(\begin{bmatrix} d & e \\ g & h \end{bmatrix}\Big)
$$

性质：

$$
\det(M_{1}M_{2}) = \det(M_1)\det(M_2)
$$

## 向量方程

单位矩阵表示一个恒等变换。

$$
I_n =
\begin{bmatrix}
  1 & 0 & \cdots & 0 \\
  0 & 1 & \cdots & 0 \\
  \vdots & \vdots & \ddots & \vdots \\
  0 & 0 & \cdots & 1
\end{bmatrix}
$$

一个矩阵 $A$ 的逆矩阵 $A^{-1}$ 表示该矩阵所代表的线性变换的逆变换。仅当 $\det{A}\not=0$ 时，$A^{-1}$ 才存在。

$$
A^{-1}A = I
$$

线性方程组可以用系数矩阵与向量组成的向量方程表示：

$$
A\vec{x}=\vec{v}
$$

当系数矩阵的逆矩阵存在时，可以通过逆矩阵来求解向量方程：

$$
A^{-1}A\vec{x}=A^{-1}\vec{v}
~\\
~\\
\vec{x} = A^{-1}\vec{v}
$$

当系数矩阵的逆矩阵不存在时，只有当 $\vec{v}$ 恰好处于 $A$ 将空间压缩成的低维度空间内时，向量方程的解才存在。

矩阵的列空间表示空间内任意向量经过线性变换以后得到的所有结果的集合，即矩阵中的列向量所张成的空间。在向量方程中，只有当 $\vec{v}$ 处于 $A$ 的列空间时，向量方程的解才存在。

矩阵的秩表示矩阵列空间的维数。若秩与矩阵的列数相等时，称该矩阵为满秩。

矩阵的零空间表示经过线性变换以后变为零向量的向量的集合。在向量方程中，当 $\vec{v}$ 为零向量时，零空间表示的就是这个方程所有可能的解。

- 当矩阵的秩为 0 时，它的零空间为原点。
- 对于二维矩阵，当矩阵的秩为 1 时，它的零空间为一条直线。
- 对于三维矩阵，当矩阵的秩为 2 时，它的零空间为一条直线；当矩阵的秩为 1 时，它的零空间为一个平面。
