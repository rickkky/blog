矩阵初等变换：

- 交换矩阵的两行（列）；
- 以非 0 常数乘矩阵的某一行（列）；
- 把某一行的常数倍加到另一行（列）上。

矩阵的初等行变换不会改变矩阵所代表的线性方程组的解。

G-J 消元法（Gauss-Jordan elimination）的核心思想是对增广矩阵 $[MI]$ 进行一系列初等行变换得到 $[IM^{-1}]$。

G-J 消元法的步骤：

1. 交换矩阵行使所有主元（pivot）都不为 0；

---

$n$ 阶矩阵 $A$ 的 $(i, j)$ 余子式（minor）通常记为 $M_{ij}$，表示将该矩阵的第 $i$ 行和第 $j$ 列移除以后得到的 $n-1$ 阶矩阵。

代数余子式（cofactor）通常记为 $C_{ij}$，有 $C_{ij}=(-1)^{i+j}M_{ij}$ 。

余子矩阵（cofactor matrix）通常记为 $C$，有 $C=(C_{ij})_{n×n}$ 。

伴随矩阵（adjugate matrix）记为 $adj(A)$ 或 $A^*$，有 $A^*=C^T$ 。

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

$AA^*$ 在 $(i, j)$ 位置上的元素为 $\sum_{k=1}^{n}a_{ik}C_{jk}$
