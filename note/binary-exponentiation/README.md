---
id: binary-exponentiation

title: 快速幂
---

# 算法思想

快速幂（Binary Exponentiation，也称为二进制取幂、平方法）是一种在 $O(\log n)$ 时间内计算 $a^n$ 的方法。

快速幂的基本思想是，在计算 $a^n$ 时，通过二分法的思想将取幂的过程分解为多个子问题，从而减少计算量。

首先，我们可以将 $n$ 表示为二进制形式，例如：

```math
a^{13} = a^{(1101)_2} = a^{2^3} \times a^{2^2} \times a^{2^0}
```

因为 $n$ 的二进制表示由 $\lfloor \log_2 n \rfloor + 1$ 二进制位组成，因此只需要进行 $O(\log n)$ 次乘法运算即可完成计算。

如果我们将 $n$ 表示为二进制形式 $(n_t n_{t-1} \cdots n_1 n_0)_2$，其中 $n_i \in \{0, 1\}$，则有：

```math
n = n_t 2^t + n_{t-1} 2^{t-1} + \cdots + n_1 2^1 + n_0 2^0
```

即：

```math
a^n
=
a^{n_t 2^t + n_{t-1} 2^{t-1} + \cdots + n_1 2^1 + n_0 2^0}
=
a^{n_t 2^t} \times a^{n_{t-1} 2^{t-1}} \times \cdots \times a^{n_1 2^1} \times a^{n_0 2^0}
```

可以看出，原问题被转化为了相同子问题的乘积。

# 实现

迭代版本：

```rust
fn pow(mut a: i64, mut n: i64) -> i64 {
    let mut res = 1;
    while n > 0 {
        if n & 1 == 1 {
            res *= a;
        }
        a *= a;
        n >>= 1;
    }
    res
}
```

递归版本：

```rust
fn pow(mut a: i64, mut n: i64) -> i64 {
    if n == 0 {
        return 1;
    }
    let mut res = pow(a, n >> 1);
    res *= res;
    if n & 1 == 1 {
        res *= a;
    }
    res
}
```

> 参考
>
> - [快速幂 - OI Wiki](https://oi-wiki.org/math/binary-exponentiation/)
