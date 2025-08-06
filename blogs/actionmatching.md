---
layout: page
permalink: /blogs/actionmatching/index.html
title: Action Matching
---

## 此即『生成』之铭 #5 ：『作用匹配』是在匹配什么？

这次来读一篇文章：Action Matching: Learning Stochastic Dynamics from Samples。
本文章介绍了一种生成模型『Action Matching』，它试图通过拟合一个标量场 $s(x,t)$ 来捕捉粒子群系统的动力学（这是任意一个由动力学过程“诱导”的生成模型所做的事情）。

具体而言，设 $\mathbb{R}^{d}$ 上有大量粒子，每个粒子服从 ODE ：$\dfrac{\mathrm{d}x (t)}{\mathrm{d}t} = v_{t}(x(t))$，则粒子群的分布 $q_{t}$ 必服从连续性方程：

$$
\dfrac{\partial q_{t}}{\partial t} =  - \nabla\cdot (q_{t} v_{t})
$$

只要从数据中学习到 $d$ 维矢量场 $v_{t}$，就可以重建粒子群系统的动力学。特别地，Action Matching 将矢量场 $v_{t}$ 的搜索空间限制在梯度场的集合内，即 $v_{t} = \nabla s_{t}$，$s_{t}$ 是一个标量场，文中将这个标量场称作『Action』。

等等！我们先看看文章里为什么要这么干。显然，连续性方程中的 $v_{t}$ 有一定的规范自由性。如果我们对 $v_{t}$ 做纵横场分解，也即

$$
v_{t} :=  \nabla s_{t} + \epsilon^{abc} \partial_{b} A_{c}
$$

其中 $\epsilon_{abc}$ 是与欧氏度规 $\delta_{ab}$ 适配的体元，$\partial_{b}$ 是 $(\mathbb{R}^{d},\delta_{ab})$ 上的普通导数算符，我们立刻可以发现：

$$
\partial_{a} (\epsilon^{abc} \partial_{b} A_{c} ) = \epsilon^{abc} \partial_{a} \partial_{b} A_{c} = \epsilon^{[ab]c} \partial_{(a} \partial_{b)}A_{c} =0
$$

其中，第一个等号利用了 $\partial_{a}$ 是与 $\delta_{ab}$ 适配的导数算符。从这里我们可以发现，$v_{t}$ 中的非梯度成分是不会对概率密度的演化做贡献的，因此我们完全可以扔掉这一部分，令 $v_{t} = \nabla s_{t}$。
至于将 $s_{t}$ 称作『Action/作用量』的原因与『最优传输』问题有关：就算我们将 $v_{t}$ 指定为梯度场，能将初始分布 $q_{0}$ 运输到终末分布 $q_{1}$ 的梯度场仍有千千万。在一些场景中，我们需要从这些 $\nabla s_{t}$ 中筛选出能最小化以下性能指标的一个（当然，在本文中我们不需要这样做）：

$$
\mathscr{A} = \int_{0}^{1} \int_{\mathbb{R}^{d}}  \dfrac{1}{2} \|v_{t} \|^{2} q_{t} \mathrm{d} x \mathrm{d}t
$$

通过对上面的性能指标进行变分，可以得到本问题的两个一阶必要条件：

$$
v_{t} =  \nabla s_{t}  \quad \dfrac{\partial s_{t}}{\partial t} + \dfrac{1}{2} \|\nabla s_{t} \|^{2} = 0 
$$

是不是觉得第二个条件很熟悉？没错，它就是我们在经典力学中已经导出过的自由粒子的 HJB 方程。在经典力学中容易证明： $s(x,t)$ 的意义是粒子从初始位置出发，在 $t$ 时刻运动到广义坐标 $x$ 处的真实路径对应的作用量。本文中借用了这个名字。


回到正题，本文中假设粒子群分布的真实演化 $q_{t}(x)$ 已知，并且它是由某个梯度场 $\nabla s_{t}^{\star}$ 驱动，我们显然希望尽可能还原这个 $\nabla s^{\star}_{t}$，因此，本文中使用所谓 Action-Gap 来量化还原的效果：

$$
\text{Action-Gap}(s,s^{\star}) = \dfrac{1}{2} \int_{0}^{1} \mathbb{E}_{q_{t}(x)} \| \nabla s_{t}(x)  - \nabla  s^{\star}_{t}(x)\|^{2} \mathrm{d}t
$$

这个东西是算不出来的，因为我们根本不知道 $\nabla s_{t}^{\star}$，因此下面要证明 Action-Gap 可以分解维一个常数 $\mathcal{K}$ 加上一个可以被计算的部分 $\mathcal{L}_{\text{AM}}$，这个证明是容易的：

$$
\begin{align*}
&2\cdot \text{Action-Gap}(s_{t},s_{t}^{\star}) \\
&=  \int_{0}^{1} \int_{\mathcal{X} = \mathbb{R}^{d}} q_{t}(x) \| \nabla s_{t}(x) - \nabla s_{t}^{\star} (x) \|^{2} \mathrm{d}x \mathrm{d}t\\
&= \int_{0}^{1} \int_\mathcal{X} q_{t}(x) \| \nabla s_{t} \|^{2}\mathrm{d}x \mathrm{d}t - \int_{0}^{1} \int_{\mathcal{ X} } q_{t}(x)(\nabla s_{t})^{T} (\nabla s^{\star}_{t})\mathrm{d}x \mathrm{d}t + \int_{0}^{1} \int_\mathcal{X} q_{t}(x) \| \nabla s^{\star}_{t} \|^{2}\mathrm{d}x \mathrm{d}t\\
&= \mathcal{I} + \mathcal{M} +  \mathcal{K}
\end{align*}
$$

其中使用了一次分部积分，并扔掉了边界项。下面单独把 $\mathcal{M}$ 项抽出来瞅一瞅：

$$
\begin{align*}
\mathcal{M}&= \int_{0}^{1} \int_\mathcal{X} \nabla\cdot  (q_{t}(x) s_{t} \nabla s^{\star}_{t})\mathrm{d}x \mathrm{d}t - \int_{0}^{1} \int_\mathcal{X} s_{t}\nabla\cdot (q_{t}(x) \nabla s_{t}^{\star}) \mathrm{d}x \mathrm{d}t\\
&= 0 - \int_{0}^{1} \int_\mathcal{X} s_{t} \dfrac{\partial q_{t}(x)}{\partial t} \mathrm{d}x \mathrm{d}t\\
&= -  \int_\mathcal{X} \int_{0}^{1} \dfrac{\partial q_{t}(x) s_{t}}{\partial t} \mathrm{d}t \mathrm{d}x + \int_\mathcal{X}\int_{0}^{1} \dfrac{\partial s_{t}}{\partial t} q_{t}(x)  \mathrm{d}t \mathrm{d}x\\
&= \mathbb{E}_{q_{0}}[s_{0}] - \mathbb{E}_{q_{1}}[s_{1}] + \int_{0}^{1}\mathbb{E}_{q_{t}(x)} \left[\dfrac{\partial s_{t}}{\partial t}\right]\mathrm{d}t
\end{align*}
$$

其中又使用了一次分部积分。回代并整理得到：

$$
\begin{align*}
\text{Action-Gap}(s_{t},s^{\star}_{t}) &= \int_{0}^{1} \mathbb{E}_{q_{t}(x)}\left[\dfrac{\partial s_{t}}{\partial t} + \dfrac{1}{2} \|\nabla  s_{t} \|^{2}\right] \mathrm{d}t + \mathbb{E}_{q_{0}}[s_{0}] - \mathbb{E}_{q_{1}}[s_{1}] + \mathcal{K}\\
&= \mathcal{L}_{\text{AM}} + \mathcal{K}
\end{align*}
$$

在 $q_{t}(x)$ 已知的情况下，$\mathcal{L}_{\text{AM}}$ 是可以计算的。


本文给出的另一个有趣的结论是在使用 ODE：$\dfrac{\mathrm{d}x (t)}{\mathrm{d}t} = \nabla  s_{t}(x)$ 移动每个粒子从而做生成时，生成分布 $\hat q_{\tau}$ 与真实分布 $q_{\tau}$ 之间的 Wasserstein-2 Distance 可以被 Action-Gap Bound 住。具体而言，设 $\nabla s_{t}(x)$ 对 $x$ 满足 Lipschitz 条件，且 Lipschitz 系数为 $K$，则有结论：

$$
W_{2}^{2}(\hat q_{\tau} , q_{\tau}) \le  \exp((1 + 2K)\tau) \int_{0}^{\tau} \mathbb{E}_{q_{t}(x)} \|\nabla s_{t} - \nabla s^{\star}_{t} \|^{2} \mathrm{d} t   
$$

证明的关键是考虑好如何估计 $W_{2}^{2}$ 的一个上界。设 $\phi (t (x)),\phi^{\star}(t(x))$ 分别为 $s_{t}, s^{\star}_{t}$，则显然有：

$$
W_{2}^{2}(\hat q_{t} ,q_{t}) \le Q_{t} = \int \mathrm{d}x \   q_{0}(x)\| \phi_{t}(x) - \phi_{t}^{\star}(x) \|^{2} 
$$

从而问题转化为考察 $Q_{t}$ 的演化：

$$
\begin{align*}
\dfrac{\partial Q_{t}}{\partial t} &= 2 \int \mathrm{d}x \ q_{0}(x) \langle  \phi_{t}(x) - \phi_{t}^{\star}(x) | \dfrac{\partial \phi_{t}(x)}{\partial t} - \dfrac{\partial \phi^{\star}_{t}(x)}{\partial t}   \rangle \\
&= 2 \int \mathrm{d}x  \ q_{0}(x) \langle  \phi_{t}(x) - \phi_{t}^{\star}(x) | \nabla s_{t}(\phi_{t}(x))  - \nabla s^{\star} (\phi^{\star}_{t}(x))  \rangle \\
&= 2 \int \mathrm{d}x  \ q_{0}(x) \langle  \phi_{t}(x) - \phi_{t}^{\star}(x) | \nabla s_{t}(\phi_{t}(x))  - \nabla s (\phi^{\star}_{t}(x))  \rangle \\
&+ 2 \int \mathrm{d}x  \ q_{0}(x) \langle  \phi_{t}(x) - \phi_{t}^{\star}(x) | \nabla s_{t}(\phi^{\star}_{t}(x))  - \nabla s^{\star} (\phi^{\star}_{t}(x))  \rangle \\
&= \int \mathrm{ d}x \  q_{0}(x)(\mathcal{A} + \mathcal{B})
\end{align*}
$$

这样两项可以分别 Bound 住，并且第二项将与 Action-Gap 产生联系：

$$
\mathcal{A} \le  2K  \| \phi_{t}(x) - \phi_{t}^{\star}(x)\|^{2}
$$

$$
\mathcal{B} \le  \| \phi_{t}(x) - \phi^{\star}_{t}(x) \|^{2} + \| \nabla s_{t}(\phi^{\star}_{t}(x))  - \nabla s^{\star} (\phi^{\star}_{t}(x))\|^{2}
$$

从而：

$$
\dfrac{\partial Q_{t}}{\partial t} \le  (1+2K)Q_{t} + \int \mathrm{d}x \ q_{0}(x) \| \nabla s_{t}(\phi^{\star}_{t}(x))  - \nabla s^{\star} (\phi^{\star}_{t}(x))\|^{2}
$$

于是完成了证明。

除了由连续性方程控制的动力学，本文中还探讨了学习另外两种动力学的情况：将连续性方程改成质量不守恒的情形：$\dfrac{\partial q_{t}}{\partial t} = - \nabla\cdot (q_{t} v) + g q_{t}$ ，或者 Fokker Planck 方程：$\dfrac{\partial q_{t}}{\partial t} = - \nabla\cdot (q_{t} v) + \dfrac{1}{2} \sigma^{2} \nabla^{2}q_{t}$。但是这两个情形下的推导和训练流程与上述最简单情况下的流程并无本质区别，因此在此处不过多介绍。

相关文章链接：[Action Matching](https://arxiv.org/abs/2210.06662)，该文章发表于 ICML (2023)。
