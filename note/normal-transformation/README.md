---
id: normal-transformation

title: 法向量变换
---

假设：

- 切线向量为 $v_0$ ，法向量为 $n_0$ ；
- 切线向量经过变换 $M_v$ 后变为 $v_1$ ，相应的法向量经过变换 $M_n$ 后变为 $n_1$ ；

则有：

```math
v_0^{\operatorname{T}} n_0 = 0
```

```math
v_1^{\operatorname{T}} n_1 = 0
```

```math
v_1 = M_v v_0
```

```math
n_1 = M_n n_0
```

代入 $v_1$ 与 $n_1$ 到等式中可得：

```math
(M_v v_0)^{\operatorname{T}} M_n n_0 = 0
```

利用矩阵乘法转置的性质变换等式：

```math
v_0^{\operatorname{T}} M_v^{\operatorname{T}} M_n n_0 = 0
```

假设：

- $M = M_v^{\operatorname{T}} M_n$ 。

当 $M = I$ 时，等式成立，即：

```math
M_v^{\operatorname{T}} M_n = I
```

解得：

```math
M_n = (M_v^{-1})^{\operatorname{T}}
```

# 补充说明

上面的方法中我们通过假设 $M = I$ 来得到等式的一个可行解，那么可能存在其他通用解吗？

考虑几种特殊情况：

- 若 $M = -I$ ，法向量 $n_0$ 会被 $M$ 变换为 $-n_0$ ，等式成立；
- 若 $M$ 为绕 $v_0$ 旋转的旋转矩阵， $n_0$ 经过旋转后仍然垂直于 $v_0$ ，等式成立；
- 若 $M n_0 = \lambda n_0$ ，当特征值 $\lambda = 1$ 时，矩阵 $M$ 的特征向量 $n_0$ 不会被 $M$ 变换，等式成立；

但是，在进行法向量变换时我们需要满足：

- 法向量相对于切线向量的定向不变；
- 变换后的法向量垂直于变换后切平面（上的任意向量），而不仅是切平面上的某个向量；
