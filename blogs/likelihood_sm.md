---
layout: page
permalink: /blogs/likelihood_sm/index.html
title: Likelihood Training of Score Matching
---

## 此即『生成』之铭 -6 ：分数匹配就是极大似然


许多人对 Yang Song 的 Score Matching 非常熟悉，所以我们这次来读一篇与 Score Matching 紧密联系的文章。 Score Matching 有如下损失：

$$
\mathcal{J}_{SM}(\theta , \lambda) = \dfrac{1}{2} \int_{0}^{T} \mathbb{E}_{p_{t}(x)} (\lambda(t) \| \nabla_{x} \log p_{t}(x)  - s_{\theta}(x,t) \|^{2}) \mathrm{d}t
$$

实际训练中，我们通常使用如下等价的损失：

$$
\mathcal{J}_{DSM} = \dfrac{1}{2} \int_{0}^{T} \mathbb{E}_{p(x)p_{0t}(x'|x)} (\lambda(t) \| \nabla_{x'} \log  p_{0t}(x'|x)  - s_{\theta}(x',t)\|^{2}) \mathrm{d}t
$$

这个训练目标与极大似然估计是有联系的。我们首先证明定理 1：生成数据的“质量”，即真实数据与生成数据之间的 KL 散度可以被 Score Matching 目标 Bound 住。记运行反向 SDE 以生成数据时，我们的先验分布是 $\pi$，由反向 SDE 生成的分布为 $p^{SDE}$。记真实分布是 $p$，真实分布通过正向 SDE 加噪后的分布记为 $p_{T}$，有：

$$
D_{KL}(p \| p^{SDE}) \le  \mathcal{J}_{SM} + D_{KL}(p_{T} \| \pi)
$$

这个结论的物理意义很直观：不难看出，如果 $p_{T}$ 和 $\pi$ 完全一致，且 $s_{\theta}$ 完美拟合了 $\nabla_{x} \log p(x,t)$，那么运行反向 SDE 必然完美地生成 $p$。因此，误差可以被拆成两部分：$p_{T}$ 和先验分布 $\pi$ 之间的差距，以及由于 $s_{\theta}$ 拟合不精确带来的差距。


证明这个定理需要用到一个结论，即 Data Processing Inequality，它是说如果我们有两个分布 $P_{X},Q_{X}$ 和一个随机映射（通过一个 Kernel $K(y\|x)$ 描述），则 $X$ 通过随机映射后得到两个新的分布：

$$
P_{Y}(y) = \int_{X} K(y|x) \mathrm{d}P_{X}(x) \quad Q_{Y}(y) = \int_{X} K(y|x) \mathrm{d}Q_{X}(x)
$$

则有：

$$
D_{KL}(P_{Y} \| Q_{Y}) \le  D_{KL} (P_{X} \| Q_{X})
$$

不难看出 $p$ 和 $p^{SDE}$ 分别是对以下两个 SDE 在 $\tau=T$ 处取边缘得到的：

$$
\mathrm{d}x = -(f - g^{2} \nabla_{x} \log  p_{T-\tau}(x)) \mathrm{d}\tau + g \mathrm{d}W \quad  \mathrm{d}\hat x = - (f-g^{2} s_{\theta}(x,T-\tau)) \mathrm{d}\tau  +g \mathrm{d}W \quad  
$$

在全文的推导过程中，我们都将第一个方程的边缘分布记为 $p_{t}$，第二个方程的边缘分布记作 $q_{t}$。第一个方程是能够精确地将 $p_{T}$ 变换到初始分布 $p$ 的方程，而第二个则是真正用于生成的方程。这里的方程中做了换元 $\tau = T-t$ ，以使得采样过程是 $\tau=0$ 到 $\tau=T$。
因此取 Kernel：$K (\{z (\tau)\}, y) = \delta(z(\tau=0),y)$，有：

$$
\int K(\{x(\tau)\},x) \mathrm{d} \mu = p (x) \quad \int K(\{\hat x(\tau),x\}) \mathrm{d}\nu = p^{SDE}(x)
$$

其中 $\mu,\nu$ 分别为两个 SDE 的路径测度。由于 $x (0) \sim p_{T} ,\hat x (0) \sim \pi$，有：

$$
\begin{align*}
D_{KL}(p \| p^{SDE}) &\le D_{KL}(\mu \| \nu )\\
&= D_{KL}(p_{T} \| \pi) + \mathbb{E}_{z \sim p}(D_{KL}(\mu(\cdot|x(0)=z) \| \nu(\cdot  | \hat x(0) =z)))
\end{align*}
$$

所以这又回到了老生常谈的问题：计算两个路径测度之间的 KL 散度。为简化起见，设 $\mathrm{d}x = a \mathrm{d}\tau + g \mathrm{d}W, \mathrm{d} \hat x = b \mathrm{d}\tau + g \mathrm{d}W$，容易将 $\mathrm{d} \mu$ 写成路径积分形式：

$$
\mathrm{d} \mu[x]  = \exp\left( - \int_{0}^{T}\dfrac{1}{2g} (\dot x - a)^{2} \mathrm{d}\tau\right) p(x(0)= x_{0})
$$

$\mathrm{d}\nu$ 同理。利用 $\mathrm{d}x = a \mathrm{d} \tau +g \mathrm{d} W$ 将其中的 $\mathrm{d} x$ 换成 $\mathrm{d} W$，并假定两个 SDE 在 $\tau = 0(t=T)$ 时刻有相同的边缘分布，立刻得到：

$$
\dfrac{\mathrm{d}\mu[x]}{\mathrm{d} \nu[x]}= \exp\left(- \int \dfrac{1}{2g^{2}} (- (a-b )^{2}\mathrm{d}\tau - 2 (a-b) g \mathrm{d}W)\right)
$$

回到原式，利用 KL 散度的定义立刻有：

$$
\begin{align*}
&D_{KL}(\mu(\cdot|x(0)=z) \| \nu (\cdot | \hat x(0)=z))\\
&= \mathbb{E}_{\mu}(\int_{0}^{T}g(T-\tau)(\nabla_{x} \log p_{T-\tau}(x) - s(x,T-\tau))\mathrm{d}W \\
& \ \ \ \ +\dfrac{1}{2} \int_{0}^{T} g^{2}(T-\tau) \|\nabla_{x} \log p_{T-\tau}(x) - s(x,T-\tau)\|^{2} \mathrm{d}\tau)\\
&= \dfrac{1}{2} \int_{0}^{T} \mathbb{E}_{p_{T-\tau}(x)} (g^{2}(T-\tau) \|\nabla_{x} \log p_{T-\tau}(x) - s (x,T-\tau)\|^{2}) \mathrm{d}\tau 
\end{align*}
$$

这个结果中已经不含有 $z$。把 $\tau$ 换回 $t$ 得到：

$$
\begin{align*}
& \ \ \ \ \dfrac{1}{2} \int_{0}^{T} \mathbb{E}_{p_{T-\tau}(x)} (g^{2}(T-\tau) \|\nabla_{x} \log p_{T-\tau}(x) - s (x,T-\tau)\|^{2}) \mathrm{d}\tau\\
&=  \dfrac{1}{2} \int_{0}^{T} \mathbb{E}_{p_{t}(x)} (g^{2}(t) \|\nabla_{x} \log p_{t}(x) - s(x,t) \|^{2}) \mathrm{d}t
\end{align*}
$$

从而定理得证。

还可以从另一个视角研究定理 1 中的这个 KL 散度，即显式地沿着轨迹追踪这个 KL 散度的演化。仍然考虑 SDE：

$$
\mathrm{d} \hat x = ( f- g^{2} \nabla_{x} s(x,t)) \mathrm{d}t + g \mathrm{d}W 
$$

将它诱导的边缘分布记为 $q_{t}$，直接考虑 KL 散度，有：

$$
\begin{align*}
D_{KL}(p(x)\| q(x)) &= D_{KL} (p_{0}(x)\| q_{0}(x)) - D_{KL}(p_{T}(x )\| q_{T}(x))+ D_{KL}(p_{T}(x )\| q_{T}(x))\\
&= \int_{T}^{0} \dfrac{\partial D_{KL} (p_{t}(x) \| q_{t}(x))}{\partial t} \mathrm{d}t + D_{KL}(p_{T}(x)\| q_{T}(x))
\end{align*}
$$

所以下面的目标是求 KL 散度对时间的导数。为简便起见采用记号 $\dfrac{\partial p_{t}(x)}{\partial t} = \nabla_{x} \cdot (h_{p}(x, t))$ 以及 $\dfrac{\partial q_{t}(x)}{\partial t} = \nabla_{x} \cdot (h_{q}(x, t))$。求偏导：

$$
\begin{align*}
\dfrac{\partial D_{KL}}{\partial t} &=  \dfrac{\partial }{\partial t} \int p_{t}(x) \log  \dfrac{p_{t}(x)}{q_{t}(x) } \mathrm{d}x\\
&= \int \dfrac{\partial p_{t}(x)}{\partial t} \log \dfrac{ p_{t}(x)}{q_{t}(x)} \mathrm{d} x+ \int \dfrac{\partial p_{t}}{\partial t} \mathrm{d}x - \int \dfrac{p_{t}}{q_{t}} \dfrac{\partial q_{t}}{\partial t} \mathrm{d}x\\
&= \int \nabla_{x} \cdot (h_{p} p_{t}) \log  \dfrac{p_{t}}{q_{t}} \mathrm{d}x - \int \dfrac{p_{t}}{q_{t}} \nabla_{x} \cdot (h_{q} q) \mathrm{d}x\\
&= -\int h_{p} p_{t} \cdot \nabla_{x} \left(\log \dfrac{p_{t}}{q_{t}}\right) \mathrm{d}x+ \int h_{q} q  \cdot  \nabla_{x} \left(\dfrac{ p_{t}}{q_{t}}\right)  \mathrm{d}x\\
&= - \int p_{t} (h_{p} - h_{q})\cdot  ( \nabla_{x} \log  p_{t} - \nabla_{x} \log q_{t}) \mathrm{d} x
\end{align*}
$$

整理一下，得到：

$$
D_{KL}(p\|p^{SDE}) = \dfrac{1}{2} \int \mathbb{E}_{x\sim p_{t}} (g^{2}(t)(\nabla_{x} \log p_{t} - s(x,t))\cdot (\nabla_{x} \log p_{t} - \nabla_{x} \log q_{t}) ) \mathrm{d}t + D_{KL}(p_{T}\|\pi)
$$

通过这样的研究方式，我们可以看到定理 1 中取等号的条件是 $s (x, t) = - \nabla_{x} \log q(x,t)$。此外，将其与定理 1 结合，我们还可以得到一个不等式：

$$
\|\nabla_{x} \log p_{t} - s(x,t) \|^{2} \ge (\nabla_{x} \log p_{t} - s(x,t))\cdot (\nabla_{x} \log p_{t} - \nabla_{x} \log q_{t}) 
$$

上面我们谈了如何 Bound 住 KL 散度，现在我们谈谈如何对 Score Matching 直接做极大似然训练。首先不难注意到 KL 散度和似然的关系：

$$
- \mathbb{E}_{p(x)} (\log p^{SDE}) = D_{KL} (p \| p^{SDE}) + \mathcal{H}(p)
$$

其中 $\mathcal{H}(p)$ 是熵，所以我们先考虑熵随着 $x(t)$ 的轨迹的演化。类似于前面研究 KL 散度，立刻有：

$$
\mathcal{H}(p(x)) - \mathcal{H}(p_{T}(x)) = \int_{T}^{0} \dfrac{\partial }{\partial t} \mathcal{H}(p_{t}(x)) \mathrm{d}t
$$

从而研究熵的演化：

$$
\begin{align*}
\dfrac{\partial }{\partial t} \mathcal{H}(p_{t}) &= - \dfrac{\partial }{\partial t} \int p_{t} \log p_{t} \mathrm{d}x\\
&= - \int  \left(\dfrac{\partial p_{t}}{\partial t} \log p_{t} + \dfrac{\partial p_{t}}{\partial t} \right) \mathrm{d}x\\
&= - \int \nabla_{x} (h_{p} p_{t}) \cdot \log p_{t} \mathrm{d}x\\
&= \int p_{t} h_{p} \cdot \nabla_{x} \log p_{t} \mathrm{d} x\\
&= \dfrac{1}{2} \mathbb{E}_{x\sim p_{t}}(g^{2} \| \nabla_{x} \log p_{t}\|^{2} - 2f \cdot  \nabla_{x} \log p_{t})\\
&= \dfrac{1}{2} \mathbb{E}_{x\sim p_{t}}(g^{2} \| \nabla_{x} \log p_{t}\|^{2}) +\int p_{t} \nabla \cdot f \mathrm{d}x
\end{align*}
$$

这是一个与被拟合的部分 $s_{\theta}(x,t)$ 无关的值。注意：我们在定理 1 中得到了：

$$
D_{KL}(p \| p^{SDE}) = \mathcal{J}_{SM} + D_{KL} (p_{T} \| \pi)
$$

可以改写为：

$$
\mathcal{H}(p) + D_{KL}(p \| p^{SDE}) = \mathcal{J}_{SM} + D_{KL} (p_{T} \| \pi) + H(p_{T}) + \int_{T}^{0} \dfrac{\partial }{\partial t} \mathcal{H}(p_{t}) \mathrm{d}t
$$

也就是说：

$$
- \mathbb{E}_{p(x)} (\log p^{SDE}) = \mathcal{J}_{SM} - \mathbb{E}_{p_{T}(x)} (\log \pi) + C
$$

这里的 C 是一个只依赖于 $f,g,p_{t}$（也就是数据集和我们选择的加噪过程）的常数。因此使用 Score Matching 目标进行训练不仅是在压低生成的分布与真实分布之间的 KL 散度，还是在优化一个 ELBO。将这个常数插入到之前的推导中，我们还将得到另一个用于优化 ELBO 的目标。把上面的式子展开：

$$
\begin{align*}
-\mathbb{E}_{p(x)} &= -\mathbb{E}_{p_T(x)}(\log \pi(x))\\
&+ \dfrac{1}{2} \int_{0}^{T}\mathbb{E}_{p_{t}(x)}(g^{2}(\|\nabla_{x}\log p_{t} - s\|^{2} - \|\nabla_{x} \log p_{t}\|^{2})) \mathrm{d}t \\
&- \int_{0}^{T} \mathbb{E}_{p_{t}(x)} (\nabla\cdot f) \mathrm{d}t
\end{align*}
$$

其中的第二项可以稍作化简：

$$
\begin{align*}
& \ \ \ \ \dfrac{1}{2} \int_{0}^{T}\mathbb{E}_{p_{t}(x)}(g^{2}(\|\nabla_{x}\log p_{t} - s\|^{2} - \|\nabla_{x} \log p_{t}\|^{2})) \mathrm{d}t\\
&= \dfrac{1}{2} \int_{0}^{T} \mathbb{E}_{p_{t}}(g^{2}(\|s\|^{2}-2g^{2}s  \cdot \nabla_{x} \log p_{t})) \mathrm{d}t\\
&= \dfrac{1}{2} \int_{0}^{T} \mathbb{E}_{p_{t}}(g^{2}\|s\|^{2}) \mathrm{d}t- \int_{0}^{T} g^{2}  \int s \cdot \nabla_{x} p_{t}\mathrm{d}
x \mathrm{d}t\\
&= \dfrac{1}{2} \int_{0}^{T} \mathbb{E}_{p_{t}}(g^{2}\|s\|^{2}) \mathrm{d}t+ \int_{0}^{T} g^{2}  \int p \nabla_{x} \cdot s\ \mathrm{d}
x \mathrm{d}t\\
&= \dfrac{1}{2} \mathbb{E}_{p_{t}(x)}(g^{2} \|s\|^{2} + 2g^{2} \nabla\cdot s) \mathrm{d}t
\end{align*}
$$

这个目标中 $f,g,s$ 都出现，因此它对这三项都可以求出梯度。通过这个目标我们可以同时对这三项进行训练以进行极大似然估计。

相关文章链接：[Maximum Likelihood Training of Score-Based Diffusion Models](https://arxiv.org/abs/2101.09258)，该文章发表于 NIPS 2021.
