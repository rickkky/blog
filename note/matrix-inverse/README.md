---
id: matrix-inverse

title: 逆矩阵
---

对于 $n$ 阶矩阵 $A$，有以下定义：

余子式（minor）记为 $M_{ij}$，表示将该矩阵的第 $i$ 行和第 $j$ 列移除以后得到的 $n-1$ 阶矩阵的行列式。

代数余子式（cofactor）记为 $C_{ij}$，有 $C_{ij}=(-1)^{i+j}M_{ij}$ 。

余子矩阵（cofactor matrix）记为 $C$，有 $C=(C_{ij})_{n×n}$ 。

伴随矩阵（adjugate matrix）记为 $A^{*}$ ，有 $A^{*}=C^T$ 。

计算 $AA^*$：

```math
AA^*
=
\begin{bmatrix}
a_{11} \cdots a_{1n} \\
\vdots \ddots \vdots \\
a_{n1} \cdots a_{nn}
\end{bmatrix}
\begin{bmatrix}
C_{11} \cdots C_{n1} \\
\vdots \ddots \vdots \\
C_{1n} \cdots C_{nn}
\end{bmatrix}
```

$A A^*$ 在 $(i, j)$ 位置上的元素为：

```math
(A A^*)_{ij}
=
\sum_{k=1}^{n}a_{ik}C_{jk}
=
\begin{cases}
\enspace |A|, & i = j \\
\enspace 0, & i \neq j
\end{cases}
```

当 $i \neq j$ 时， $(AA^*)_{ij}$ 实际上相当于把 $A$ 的第 $j$ 行元素换成第 $i$ 行元素后求行列式。由于有两行相同，因此行列式为 $0$。

```math
AA^*
=
\begin{bmatrix}
|A| & 0 & \cdots & 0 \\
0 & |A| & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & |A|
\end{bmatrix}
=
|A|I
```

因此可得：

```math
A^{-1} = \frac{A A^*}{|A|}
```
