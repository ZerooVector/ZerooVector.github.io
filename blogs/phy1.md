---
layout: page
permalink: /blogs/phy1/index.html
title: 为什么量子力学没有被『重写』过？
---


## 为什么量子力学没有被『重写』过？


从强化学习到分数匹配，以随机过程作为"基底"的技术正在重塑我们的生活。我们知道量子力学中的所谓“玻恩概率诠释”：若一个系统的态矢为 $\| \psi \rangle$，则测量其物理量 $A$ 时，结果以 $\langle  a \| \psi \rangle \langle  \psi \|  a\rangle$ 的概率落在 $A$ 的本征值 $a$ 上。显然，对于可观测量 $A$ 有连续本征值的情形（比如坐标或者动量），每一时刻可观测量都在 $(- \infty, + \infty )$ 上有一个概率分布，换言之，可以将粒子的位置 $X(t)$ 建模为随机过程。为什么没有人使用这种方式重写量子力学？


众所周知，在 Heisenberg 绘景下，粒子的转移振幅可以写成 $\langle  x',t' \| x,t \rangle$，我们可以向其中插入一次完备性关系：


$$
\langle  x',t' | x,t \rangle = \int \mathrm{d}x'' \langle x',t'  | x'',t'' \rangle \langle  x'',t'' |x,t  \rangle
$$

这相当于在粒子的轨迹中取定一个“中转站”，将粒子的转移轨迹分成两段，再对所有可能的中转站位置积分。通过插入无数次完备性关系，我们就得到了量子力学的路径积分表述。我们知道，如果一个随机过程 $X(t)$ 是马氏的，我们也会有类似的结论：

$$
\mathbb{P}(x(0) = 0, x(T) = x_{T}) = \int \mathrm{d}x  \mathbb{P}(x(0) =0  ,x(t) = x) \mathbb{P}(x(t)=x,x(T)=x_{T})
$$

问题是我们能不能在量子力学中给出**完全相同**的，而不只是类似的结论？考虑

$$
\begin{align*}
\langle  x',t' | x,t \rangle \langle  x,t | x',t' \rangle &=  \left(\int \mathrm{d}x'' \langle x',t'  | x'',t'' \rangle \langle  x'',t'' |x,t  \rangle\right)\left(\int \mathrm{d}x'' \langle x',t'  | x'',t'' \rangle \langle  x'',t'' |x,t  \rangle \right)^{\star}\\
&= \left(\int \mathrm{d}x_{a}'' \langle x',t'  | x_{a}'',t'' \rangle \langle  x_{a}'',t'' |x,t  \rangle\right)   \left(\int \mathrm{d}x_{b}'' \langle x_{b}'',t''  | x',t' \rangle \langle  x,t |x_{b}'',t''  \rangle\right)\\
&\not =   \int \mathrm{d}x'' (\langle  x',t' |x'',t''  \rangle \langle  x'',t'' | x',t' \rangle) (\langle  x,t |x'',t''  \rangle \langle  x'',t'' |x,t  \rangle) 
\end{align*} 
$$

我们发现不能在积分号里面直接凑出概率幅，这说明 $X(t)$ 没有马尔可夫性！为什么会这样？粒子的波函数 $\langle  x \| \psi \rangle$ 出现在 Schrodinger 方程中，而非概率密度 $\langle  x \| \psi \rangle \langle  \psi \| x \rangle$，因此只使用随机过程 $X(t)$ 是无法描述粒子的全部状态的。

那么，一定有人会问：是不是和经典力学一样，需要使用 $\{X(t),P(t)\}$ 作为系统的全部状态？要回答这个问题，我们先证明一个引理。

**引理**：只知道位置波函数的模方 $\langle  x \| \psi \rangle \langle  \psi \|  x\rangle$ 和动量波函数的模方 $\langle  p \| \psi \rangle \langle  \psi \| p \rangle$，无法唯一地重建位置波函数 $\langle  x \|\psi  \rangle$。

假定位置波函数可以写成：

$$
\langle  x | \psi \rangle = \sqrt{P(x)} \exp(i \theta (x))
$$

则这个问题等价于能否找到 $\theta_{1}(x) \not = \theta_{2}(x) + C_{0}, \forall C_{0}$，使得对于 $\forall p$ 有：

$$
\left(\int_{-\infty}^{+\infty} \sqrt{P(x)} \exp(i \theta_{1}(x))\exp( -i px) \mathrm{d}x\right)^{2} = \left(\int_{-\infty}^{+\infty} \sqrt{P(x)} \exp(i \theta_{2}(x))\exp( -i px) \mathrm{d}x\right)^{2}
$$

**证明**：构造反例：

$$
\psi_{1}(x) = C \exp(- \alpha x^{2}) \exp( i \beta x^{2}) \quad \psi_{2}(x) = C \exp(- \alpha x^{2}) \exp(- i \beta x^{2})
$$

直接使用 Fourier 变换计算动量波函数得：

$$
\begin{align*}
\psi_{1}(p) &= C \int \exp( - (\alpha - i \beta)x^{2})  \exp\left(- \dfrac{ip}{\hbar }x \right)\mathrm{d}x\\
&=  C \exp\left(\dfrac{B^{2}}{4A}\right)  \int  \exp\left(-A \left(x - \dfrac{B}{2A}\right)^{2}\right) \mathrm{d }x  \quad A=\alpha - i \beta , B = - \dfrac{ip}{\hbar}\\
&= C \exp\left(\dfrac{B^{2}}{4A}\right)\sqrt{\dfrac{\pi}{A}} \\
&= C\sqrt{\dfrac{\pi}{\alpha - i \beta}} \exp\left( - \dfrac{p^{2}}{4 \hbar^{2}(\alpha - i \beta)} \right)
\end{align*}
$$

那么它的模方自然就是：

$$
\psi_{1}(p) \psi_{1}^{\star}(p) = C^{2}\dfrac{\pi}{\sqrt{\alpha^{2} + \beta^{2}}} \exp\left( - \dfrac{p^{2} \alpha}{2 \hbar^{2}(\alpha^{2} + \beta^{2})}\right)
$$

显然，在对 $\psi_{2}$ 进行计算时，只需将 $\beta$ 换成 $-\beta$，而这完全不引起坐标和动量波函数模方的变化。因为我们举出了反例，所以引理证成。

下面来到本文的主要定理，我们试着在形式上给出 $X(t)$ 满足的 ODE 或 SDE 。

**定理** ：若单粒子的位置波函数写成 $\psi (x, t) = \sqrt{\rho (x, t)} \exp(i \theta(x,t))$ 的形式，则随机过程 $X(t)$ 满足如下 ODE：

$$
\mathrm{d}X(t) = \dfrac{\hbar }{m } \nabla \theta(X(t),t) \mathrm{d }t
$$

或如下 SDE：

$$
dX(t) = \left(\dfrac{\hbar }{m} \nabla \theta\left(X(t),t\right) + \dfrac{1}{2} \sigma^{2}\nabla \log \rho(X(t),t)\right) \mathrm{d}t+ \sigma \mathrm{dW_{t}} \quad \forall \sigma > 0 
$$


**证明**：考虑单粒子概率密度的演化：

$$
\dfrac{\partial \rho}{\partial t} = \psi^{\star} \dfrac{\partial \psi}{\partial t} + \psi \dfrac{\partial \psi^{\star}}{\partial t}
$$

将单粒子 Schrodinger 方程代入：

$$
\dfrac{\partial \psi}{\partial t} = \dfrac{1}{i\hbar} \left(- \dfrac{\hbar^{2}}{2m} \nabla^{2} \psi  + V \psi \right)
$$

立刻得到结果：

$$
\dfrac{\partial \rho}{\partial t} = - \dfrac{\hbar }{2mi}( \psi^{\star} \nabla^{2}\psi - \psi  \nabla^{2} \psi^{\star}) 
$$

为了做出流守恒方程或 Fokker Planck 方程的形式，我们需要以下矢量恒等式：

$$
\nabla(\psi \nabla\psi^{\star}) = \psi \nabla^{2} \psi^{\star} + (\nabla\psi ) \cdot (\nabla\psi^{\star})
$$

代入后即可出现我们想要的形式：

$$
\dfrac{\partial \rho}{\partial t} + \nabla(\rho j ) = 0 \quad j  = \dfrac{i \hbar }{2m\rho } (\psi^{\star} \nabla\psi  - \psi \nabla\psi ^{\star}) 
$$

将波函数写成 $\psi (x, t) = \sqrt{\rho (x, t)} \exp(i \theta(x,t))$，代入后将得到极其简洁的形式：

$$
j = \dfrac{\hbar }{m} \nabla \theta (x,t)
$$

从而，我们立刻得到描述 $X(t)$ 的 ODE：

$$
\mathrm{d}X(t) = \dfrac{\hbar }{m } \nabla \theta(X(t),t) \mathrm{d }t
$$

停 ！到这里我们先理解一下，前面我们说要将 $X(t)$ 视作随机过程，这里我们怎么只给出了描述 $X(t)$ 的 ODE？显然，每个时间点上的 $X(t)$ 都是一个随机变量，$X(t)$ 确实是随机过程。我们只给出 $X(t)$ 的 ODE，这只是表示 **如果将概率密度视作由一堆粒子叠加而成的，那么这群粒子中每一个粒子的轨迹都是确定性的**，这**不表示位置表象波函数的波包不会扩散**，具体会不会扩散由 $\theta(x,t)$ 给出。
你如果觉得这样写不舒服，那么你当然可以把它改写成 SDE，具体的操作方式和分数匹配中一样，只要新的 SDE 和原 ODE 给出相同的概率密度演化方程即可，所以：

$$
dX(t) = \left(\dfrac{\hbar }{m} \nabla \theta\left(X(t),t\right) + \dfrac{1}{2} \sigma^{2}\nabla \log \rho(X(t),t)\right) \mathrm{d}t+ \sigma \mathrm{dW_{t}}
$$

其中 $\mathrm{d}W_{t}$ 是标准布朗运动。

现在就到了引理起作用的时候了：我们发现 ODE 中出现了 $\theta(x,t)$，换言之，恰好是 $\theta$ 的梯度在“推着”粒子前进。因此，如果要选一组变量表达系统的全部信息，这组变量必须包含 $\theta(t)$（在引理中我们已经证明，只靠 $\{X(t),P(t)\}$ 是恢复不出 $\theta(t)$ 的），而 $\theta(t)$ 又得靠 Schrodinger 方程解出来。因此，我们做的这些事情某种意义上“徒劳无功”，无法对量子力学做任何本质的简化。


