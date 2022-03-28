# 向量

- 物理：空间中的箭头
- 计算机：有序的数字列表
- 数学：抽象的数学符号 $\vec{v}$，只要保证向量加法运算与数乘运算是有意义的即可

## 加法

$\vec{v}+\vec{w}$ 表示从原点先沿着 $\vec{v}$ 移动，然后沿着 $\vec{w}$ 移动得到的向量。

$$
\begin{bmatrix}
  x_1 \\
  y_1
\end{bmatrix}
+
\begin{bmatrix}
  x_2 \\
  y_2
\end{bmatrix}
=
\begin{bmatrix}
  x_1 + x_2 \\
  y_1 + y_2
\end{bmatrix}
$$

## 数乘

$C\vec{v}$ 表示沿 $\vec{v}$ 的方向（若 $C$ 为负数则为反方向）对其缩放 $C$ 倍得到的向量。

$$
C
\begin{bmatrix}
  x \\
  y
\end{bmatrix}
=
\begin{bmatrix}
  C x \\
  C y
\end{bmatrix}
$$

## 线性组合

$a\vec{v}+b\vec{w}$ 称为 $\vec{v}$ 与 $\vec{w}$ 的线性组合。

$a\vec{v}+b\vec{w}$ 可以表示的所有向量的集合称为 $\vec{v}$ 与 $\vec{w}$ 张成的空间。

当一组向量中的某个向量可以表示为其他向量的线性组合，则称这些向量是线性相关的。否则称为线性无关。

向量空间中任意一组线性无关的向量可以作为该空间的一组基向量。

## 线性变换

线性变换需要满足两个条件：原点保持不变；直线在变换前后仍为直线。

矩阵表示一个线性变换，矩阵向量乘法的结果表示向量经过线性变换后得到的新向量。若将任何向量都视为一组基向量的线性组合，矩阵的每一列可以看作是一个变换后的基向量，矩阵向量乘法的结果仍然表示变换后的基向量的相同的线性组合。

$$
\vec{v} = x\vec{i} + y\vec{j}
$$

$$
T =
\begin{bmatrix}
  T\vec{i} & T\vec{j}
\end{bmatrix}
$$

$$
T\vec{v} = xT\vec{i} + yT\vec{j}
$$

$$
\begin{bmatrix}
  a & b \\
  c & d
\end{bmatrix}
\begin{bmatrix}
  x \\
  y
\end{bmatrix}
=
x
\begin{bmatrix}
  a \\
  c
\end{bmatrix}
+
y
\begin{bmatrix}
  b \\
  d
\end{bmatrix}
=
\begin{bmatrix}
  ax + by \\
  cx + dy
\end{bmatrix}
$$
