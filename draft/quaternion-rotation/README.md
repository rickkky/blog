---
id: quaternion-rotation

title: 四元数旋转
---

假设：

- $\textbf{u}$ 表示旋转轴上的单位向量；
- $\theta$ 表示旋转角度；
- $\textbf{v}$ 表示待旋转的向量。

构造一个单位四元数表示绕给定轴的旋转：

```math
q
=
\cos(\frac{\theta}{2}) +
\sin(\frac{\theta}{2}) u_x i +
\sin(\frac{\theta}{2}) u_y j +
\sin(\frac{\theta}{2}) u_z k
```

其中 $i, j, k$ 是四元数的虚部单位。

将向量 $\textbf{v}$ 扩展为四元数：

```math
q_v = 0 + v_x i + v_y j + v_z k
```

计算四元数乘法：

```math
q_v^{\prime} = q \cdot v_q \cdot q^*
```

提取 $q_v^{\prime}$ 的虚部组成向量 $\textbf{v}^{\prime}$ ，该向量即为 $\textbf{v}$ 绕 $\textbf{u}$ 轴旋转 $\theta$ 角度后的结果。
