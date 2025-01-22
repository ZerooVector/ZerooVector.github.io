---
layout: page
permalink: /blogs/flowmatching1/index.html
title: FM1
---


## 此即『生成』之铭 #3 ：流匹配的直观理解

Flow Matching 的目的是构建一个概率路径 $p_{t}$，该路径连接了已知的路径起点 $p_{0} = p$ 和路径终点 $q_{0} = q$。具体来讲，我们以 $\psi_{t}(x)$ 代表一个粒子的特征线，那么对于 $X_{0} \sim p_{0}$，有 $X_{t} = \psi_{t}(X_{0}) \sim p_{t}$。 $\psi$ 由以下一阶 ODE 控制：

$$
\dfrac{\mathrm{d}}{\mathrm{d}t} \psi_{t}(x)  = u_{t}(\psi_{t}(x))
$$

由于 $\psi_{t}(x)$ 追踪了一个粒子的位置，那么 $u_{t}(\cdot) = u(t,x)$ 就是一个分布在 $\mathbb{R}^{d}$ 全空间的时变向量场。那么现在我们要做的事情只有两件：1）钦定一个连接分布 $p,q$ 的向量场 $u_{t}$（如果每个粒子沿着向量场行进，那么粒子的整体分布就能完成从 $p$ 到 $q$ 的转移）；2）用神经网络学习它。我们先遵循在 Diffusion 中的传统（或者说实用主义的传统），令 $p$ 是 $\mathcal{N}(0,I)$，假设终末数据集代表了终末分布 $q$，则我们可以先针对终末数据集中的每一个数据 $x_{1}$ 构建条件概率路径 $p_{t}(x\|x_{1})$，那么总体的概率路径是：

$$
p_{t}(x) = \int p_{t}(x|x_{1}) q(x_{1})\mathrm{d}x_{1}
$$

一种简单的条件概率路径的构建方式是：

$$
p_{t}(x|x_{1}) = \mathcal{N}(x|tx_{1},(1-t)^{2} I)
$$

它只需要将初始数据和终末数据进行线性组合就可以实现：

$$
X_{t|x_{1}} = t x_{1} + (1-t )X_{0}, X_{0} \sim p \Rightarrow X_{t|x_{1}} \sim p_{t}(x|x_{1})
$$

由于终末数据分布 $q$ 由经验分布代表，因此：

$$
X_{t}  = t X_{1}  + (1-t) X_{0} , X_{0} \sim p ,X_{1}\sim q  \Rightarrow X_{t} \sim p_{t}(x)
$$

如果直接凭空猜测使得粒子从分布 $p$ 迁移到分布 $q$ 的向量场，这比登天还难。所以一种简单的方式是：我先使得从各点出发的粒子“聚为一点”，也就是说，考察条件概率 $p_{t}(x\|x_{1})$，在 $t=0$ 时从 $p$ 出发，在 $t=1$ 时到达 $x_{1}$ 一点，这个时候向量场是非常好写的：

$$
\dfrac{\mathrm{d}}{\mathrm{d}t} X_{t|1} = x_{1}  - X_{0} = \dfrac{x_{1} - x}{1-t}
$$

我们可以提出以下 Conditional Flow Matching 目标：

$$
\mathcal{L}_{CFM} = \mathbb{E}_{t, X_{0},X_{1}} ||u_{t}^{\theta}(X_{t}) - u_{t}(X_{t}|X_{1})||^{2} , t \sim \mathcal{U}[0,1], X_{0}\sim p , X_{1}\sim q
$$

可以证明，它和标准的 

$$\mathcal{L}_{FM}= \mathbb{E}_{t,X_{t}}||u_{t}^{\theta(X_{t})}- u_{t}(X_{t})||^{2}$$

 的梯度一致，并且 CFM 目标非常好实施。



