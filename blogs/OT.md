---
layout: page
permalink: /blogs/OT1/index.html
title: OT1
---

## 此即『生成』之铭 #4 ：最优运输的直观理解


在图像生成模型中（无论是 VAE 还是 Diffusion），我们实质上是找一个将先验分布 $p_{0}$ 推前到目标分布 $p_{1}$ 的映射。在 VAE 中，这个推前映射由 Encoder 实现，形式化地：

$$
\# p_{0} = \mathrm{Encoder}(p_{0}) = p_{1}
$$

在 Diffusion 中，这个推前映射由逆向 SDE 实现：

$$
\mathrm{d}X_{t} = (f - g^{2} \nabla \ln p) \mathrm{d}t + g \mathrm{d}W_{t}
$$

而在 Flow Matching 中，这个推前映射通过一个 ODE 实现：

$$
\mathrm{d}X_{t} = f(X_{t},t) \mathrm{d}t
$$

但是，以上列出的三种方法中，VAE 根本没有动力学模型，Diffusion 和 Flow Matching 的动力学是人为设计的（在 Diffusion 中，我们需要设计 $f$ 和 Noising Schedule $g$；在 Flow Matching 中需要设计 $f$），也就是说它们没有**学习真正的动力学方程**。那么有没有模型能够学习从先验分布推前到目标分布的这一真实动力学过程呢？有的兄弟，有的。下面介绍的最优传输就能做到这一点。

考虑给定的初始分布和终末分布 $p_{0},p_{1}$，从初始分布中采样一些独立粒子（这些粒子位于 $\mathbb{R}^{N}$ 中，下同），每一个粒子的动力学方程是以下 ODE：

$$
 \mathrm{d} X_{t}  = u(X_{t},t) \mathrm{d}t
$$

那么，这些粒子的分布演化满足连续性方程：

$$
\dfrac{\partial p(x,t)}{\partial t} + \partial_{i}(p(x,t) u^{i}(x,t)) =0
$$

『动力学』信息被编码在作用量中，『最优传输』要求运输过程中最小化以下作用量：

$$
S = \int_{0}^{1} \int_{\mathbb{R}^{n}} \dfrac{1}{2} u^{i}u_{i}\ p\  \mathrm{d}x \mathrm{d}t \quad \mathrm{s.t.} \dfrac{\partial p(x,t)}{\partial t} + \partial_{i}(p(x,t) u^{i}(x,t)) =0
$$


我们通过把这个作用量变形来直观理解一下最优传输。考虑一个粒子的初始位置位于 $x_{0}$，它的位置演化（特征线）由 $x_{0}\mapsto z(x_{0},t)$ 给出，$z(x_{0},t)$ 满足上文中提到的 ODE。那么，初始位于体积微元 $\mathrm{d}x_{0} = (x_{0},x_{0} + \mathrm{d}x_{0})$ 的粒子在 $t$ 时间后全部演化到 $\mathrm{d}z = (z (x_{0}, t), z (x_{0} +  \mathrm{d}x_{0}, t)) = (z,z + \mathrm{d}z)$ 中。这两个微元的体积比恰为坐标变换 $x_{0} \mapsto z$ 的 Jacobi 行列式。根据体积微元内的粒子数守恒，我们写出：

$$
p(x_{0},0) \mathrm{d} x_{0} = p(z(x_{0},t),t) \mathrm{d}x_{0} \det (J(z))
$$

这个方程给出了粒子特征线上的概率密度演化。我们把作用量做一个变量替换：

$$
S = \int_{0}^{1} \int _{\mathbb{R}^{n}} \dfrac{1}{2} u^{i}(z(x_{0},t))u_{i}(z(x_{0},t)) p(z(x_{0},t),t) \mathrm{d}z  \mathrm{d}t = \int_{0}^{1} \int _{\mathbb{R}^{n}} \dfrac{1}{2} u^{i}(\cdot)u_{i}(\cdot) p_{0}(\cdot) \mathrm{d}x_{0} \mathrm{d}t  
$$

换一下右边的积分顺序：

$$
S = \int _{\mathbb{R}^{n}} \int_{0}^{1} \dfrac{1}{2} u^{i}(\cdot)u_{i}(\cdot) p_{0}(\cdot)  \mathrm{d}t  \mathrm{d}x_{0} = \mathbb{E}_{x_{0}\sim p_{0}} \left[ \int_{0}^{1} \dfrac{1}{2} u^{i}(\cdot)u_{i}(\cdot)  \mathrm{d}t\right]
$$

内层积分计算的是**运输一个自由粒子的作用量**，而外层积分负责的是**从初始分布采样这些自由粒子，并将所有粒子的作用量加和**。因此，最优传输的物理意义表述为：**从初始分布采样自由粒子，将它们运输到某一指定的终末分布，使得这个过程中所有粒子的作用量总和最小**。


上面的物理意义给我们提供两种启发：（1）在求解方式上，我们可以参数化 $u(x,t)$（使用基函数或者神经网络），从已知的初始分布 $p_{0}$ 采样粒子并要求粒子沿着 $u(x,t)$ 给出的动力学演化，这样我们可以沿着演化路径计算作用量，并使用作用量对 $u(x,t)$ 进行梯度下降。（2）由于自由粒子在 1 单位时间内从 $x$ 点运动到 $y$ 点的作用量是：

$$
\hat S = \dfrac{1}{2} (y - x)^{2}
$$

因此若最优传输中的初始分布和终末分布都是多个 $\delta(\cdot)$ 函数的叠加，则最优传输问题重新表述为：现有两组点，每组点共 $N$ 个，以 $i,j = 1,2,\cdots N$ 标号。寻找 $i \mapsto j$ 的一一映射 $\sigma(i)$，使得

$$
S  =\dfrac{1}{2} (\vec{x}_{i}  - \vec{y}_{\sigma(i)})^{2}
$$

最小化。这个问题可以使用匈牙利算法求解。

此外，可以使用变分法处理最优传输问题以得到其他结论。使用拉格朗日乘子将约束引进作用量，此时 $p$ 和 $u$ 可独立进行变分：

$$
S = \int_{0}^{1} \int_{\mathbb{R}^{n}} \left[\dfrac{1}{2} u^{i}u_{i}p - \lambda\left(\dfrac{\partial p}{\partial t} + \partial_{i}(p u^{i}) =0\right)\right]  \mathrm{d}x \mathrm{d}t
$$

对 $u$ 的变分给出：

$$
u_{i} = - \partial_{i}\lambda
$$

这说明速度场 $u_{i}$ 是无旋的（更严格地，$N$ 维情况下应该说速度 1-形式 $u_{a}$ 是恰当的）。对 $\rho$ 的变分给出：

$$
\dfrac{1}{2} u^{i}u_{i} - \partial_{t} \lambda  - \partial_{i} \lambda v^{i} = 0
$$

代入 $u_{i}$ ，得到：

$$
\partial_{t} \lambda  + \dfrac{1}{2}  \partial_{i}\lambda \partial^{i} \lambda = 0
$$

这恰好是自由粒子的 Hamilton-Jacobi 方程。