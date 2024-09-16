---
layout: page
permalink: /blogs/variationalinference/index.html
title: Variational Inference
---

## 生成模型从入门到入土#1：变分推断





### 什么是变分推断？
在传统的机器学习中，我们要解决的是参数的点估计问题（优化问题），而在贝叶斯学派的视角中，我们需要估计参数的分布。考虑贝叶斯公式：

$$P(\theta|x) = \dfrac{P(x|\theta)P(\theta)}{P(x)}$$


假设我们现在有了数据样本 $X$，我们希望估计一个新出现的数据 $x$ 出现的概率是多少，那么：

$$P(x|X) = \int   P(x,\theta|X) d \theta = \int  P(x|\theta,X) P(\theta|X) d \theta  = \mathbb{E}_{P(\theta|X)} [P(x|\theta)]$$


因此，我们只要知道参数的后验分布$P(\theta\|X)$，就可以估计新样本出现的概率。一般来说，模型的参数非常复杂，后验分布相当难以求出，此时我们就需要使用近似方法。变分推断就是一种确定性的近似方法。

### 基于平均场理论的梯度上升方法

在以下描述中，我们使用 $X$ 表示观测数据，而 $Z$ 表示隐变量和参数的集合。我们现在将 $P(X)$ 写成两式之和：

$$\begin{align*}\log P(X) &= \log P(X,Z) - \log(Z|X)\\&= \log \dfrac{P(X,Z)}{q(Z)} - \log  \dfrac{P(Z|X)}{q(Z)}\end{align*}$$

两侧同时取期望：


左侧：


$$
LHS = \int \log P(X) q(Z) dZ = \log P(X)
$$

右侧：


$$
\begin{align*}
RHS &= \int_{Z}  q(Z) \log \dfrac{P(X,Z)}{q(Z)} dZ - \int_{Z} q(Z)  \log \dfrac{P(Z|X)}{q(Z)}  dZ
\end{align*}
$$



其中，第一项被称为置信下界 ELBO，记为 $\mathcal{L}(q)$，而右边则是 $\text{KL}(q\|\|p)$，也就是 $q(Z)$ 和 $p(Z\|X)$ 的 KL 散度。我们现在希望使用 $q(Z)$ 来逼近 $p(Z\|X)$，那么我们自然想使得 $\text{KL}(q\|\|p)$ 尽可能小。考虑到 $P(X)$ 是定值，那么我们希望使得：

$$
\mathcal{L}(q) = \int_{Z} q(Z)  \log \dfrac{P(X,Z)}{q(Z)} dZ 
$$

取得最大值。我们假设随机变量 $Z$ 可以划分成 $M$ 个组，这些组两两互相独立，那么我们就得到了：

$$
q(Z) = \prod_{i=1}^{M} q_{i}(Z_{i})
$$

首先假设只有一个分量 $q_{j}$ 能变化，其余的分量暂且固定。将 $q(Z)$ 的表达式代回上式：

$$
\begin{align*}
\mathcal{L}(q) &= \int_{Z} q(Z) \log P(X,Z) dZ - \int_{Z} q(Z)\log  q(Z) dZ\\
&= \int_{Z} \prod_{i=1}^{M} q_{i}(Z_{i}) \log P(X,Z) dZ_{1} \cdots dZ_{M} - \int_{Z} q(Z)\log  q(Z) dZ\\
&= \int_{Z_{j}} q_{j}(Z_{j}) \left(\int_{Z_{i},i \not=j}\prod_{i\not = j}^{M} q_{i}(Z_{i}) \log P(X,Z) dZ_{1} \cdots dZ_{M}\right)  dZ_{j} - \int_{Z} q(Z)\log  q(Z) dZ\\
&= \int_{Z_{j}} q_{j}(Z_{j})\mathbb{E}_{\prod_{i \not = j}^{M}q_{i}(Z_{i})}[\log P(X,Z)]   dZ_{j}  -\int_{Z} q(Z)\log  q(Z) dZ \\
&= \int_{Z_{j}} q_{j}(Z_{j})\mathbb{E}_{\prod_{i \not = j}^{M}q_{i}(Z_{i})}[\log P(X,Z)]   dZ_{j} - \int_{Z}   \prod_{i=1}^{M} q_{i}(Z_{i}) \sum_{i=1}^{M}  \log q_{i}(Z_{i}) dZ
\end{align*}
$$

推导到这一步的时候，我们单独拿出其中一项来看：

$$
\begin{align*}
& \int_{Z} q_{1}q_{2}\cdots q_{M} \log q_{1} dZ \\
&= \int_{Z_{1}} q_{1} \log q_{1} dZ_{1} \int q_{2} dZ_{2}  \cdots  \int q_{M} dZ_{m}\\
&= \int_{Z_{1}}   \log q_{1} dZ_{1}  
\end{align*}
$$

回到最初的推导：

$$
\begin{align*}
\text{原式} &= \int_{Z_{j}} q_{j}(Z_{j})\mathbb{E}_{\prod_{i \not = j}^{M}q_{i}(Z_{i})}[\log P(X,Z)]   dZ_{j} - \sum_{i=1}^{M} \int_{Z_{i}} q_{i}(Z_{i}) \log q_{i}(Z_{i}) dZ_{i}\\
&= \int_{Z_{j}} q_{j}(Z_{j})\mathbb{E}_{\prod_{i \not = j}^{M}q_{i}(Z_{i})}[\log P(X,Z)]   dZ_{j} - \int_{Z_{j}} q_{j}(Z_{j}) \log q_{j}(Z_{j})dZ_{j} + C  \\
&\le \int_{Z_{j}} q_{j}(Z_{j}) \log  \dfrac{\hat P(X,Z_{j})}{q_{j}(Z_{j})} dZ_{j} +C  \\
&= - \text{KL}(q_{j}|| \hat P(X,Z_{j})) + C 
\end{align*}
$$


这给出了只求解 $Z$ 中的一个组 $Z_{i}$，而其他组不变时，ELBO 的表达式。


我们规范一下符号，我们现在使用 $x^{(i)}$ 代表第 $i$ 个样本，而 $z^{(i)}$ 代表第 $i$ 个隐变量，那么假定我们有一个参数为 $\theta$ 的生成模型，它的似然就是：

$$
\log  P_{\theta}(X)= \log  \prod_{i=1}^{N}  P_{\theta} (x^{(i)} )  = \sum_{i=1}^{N}   \log P_{\theta} (x^{(i)} )
$$

为了简化推导，我们只研究每一个样本：$P_\theta(x^{(i)})$，不考虑上文中将 $z$ 拆开的情况，那么此时的 ELBO 写成：

$$
\mathcal{L}(q) = \mathbb{E}_{q(z)} \left[\log \dfrac{P_{\theta}(x^{(i)} ,z)}{q(z)}\right]  = \mathbb{E}_{q(z)} [\log P_\theta(x^{(i)} ,z)] + H [q(z)]
$$

其中，$H(q(z))$ 是分布 $q(z)$ 的信息熵，$z$ 是一个无法观测的隐变量，可能是生成图片的类别之类，而 $\theta$ 是生成模型的参数。看看上文中我们只求 $q_{j}(z_{j})$，而固定其他变量的方法，这实际上是一种坐标上升法。注意到 $\mathcal{L}(q)$ 的最大值是 0， 为了最大化上面的 $\mathcal{L}(q)$，我们需要：

$$
\log q_{j}(z_{j}) = \int_{q_{1}} dq_{1} \int_{q_{2}} dq_{2} \cdots  \int_{q_{j-1}} dq_{j-1}\int_{q_{j+1}} dq_{j+1}\int_{q_{M}}  dq_{M}\ q_{1}q_{2}\cdots q_{M} [\log P_\theta(x^{(i)},z)]
$$

分别不断上升每个坐标，就可以求出 $q(Z)$。这种经典变分推断的假设太强了，因此有很多问题无法解决，而且，上面的期望很可能算不出来！

### 基于梯度的变分推断

这里我们假设 $q(z)$ 这个分布的参数是 $\phi$，那么目标函数是：

$$
\mathcal{L}(\phi) = \mathbb{E}_{q_{\phi} (z)} [\log P_\theta(x,z) - \log q_{\phi}(z)]
$$

现在开始求梯度：

$$
\begin{align*}
\nabla_{\phi} \mathcal{L}(\phi ) &= \nabla_{\phi} \int q_{\phi}(z)  \log P_\theta(x,z) - \log q_{\phi}(z)  dz \\
&= \int \nabla_{\phi} q_{\phi} \cdot (\log P_{\theta}(x,z) - \log q_{\phi}) dz   + \int q_{\phi} \nabla_{\phi}(\log P_\theta(x,z) - \log q_{\phi}) dz\\
\end{align*}
$$

由于 $\nabla_{\phi}q_{\phi}$ 可以很容易地求出，而 $P_{\theta}(x,z)$ 又与 $q_{\phi}$ 没关系，因此我们只需要重点关注下面这一部分：

$$
\begin{align*}
& -\int q_{\phi} \nabla_{\phi}  \log q_{\phi} dz \\
&= - \int q_{\phi}  \dfrac{1}{q_{\phi}} \nabla_{\phi}  q_{\phi}   dz  \\
&= - \nabla_{\phi} \int  q_{\phi}  dz  \\
&= 0 
\end{align*}
$$

因此，最后我们只剩下第一项。我们希望将其写成一个期望的形式，从而便于求解：

$$
\begin{align*}
\nabla_{\phi}  \mathcal{L}(q) &=  \int \nabla_{\phi} q_{\phi} \cdot (\log P_{\theta}(x,z) - \log q_{\phi}) dz\\
&= \int q_{\phi}  \nabla_{\phi} \log q_{\phi} (\log P_\theta(x,z) - \log q_{\phi}) dz 
\end{align*}
$$

这个方法可能不太实用。因为 $\nabla_{\phi} \log  q_{\phi}$ 这一项的方差会非常大，在学习过程中会出现各种数值不稳定的问题。我们现在介绍一种重参数化技巧。设 $z = g_{\phi}(\epsilon)$，其中 $\epsilon$ 服从某个分布 $\epsilon$，而 $g(\cdot)$ 则是确定的！那么此时有：

$$
|q_{\phi}(z) dz | = |p(\epsilon) d \epsilon|
$$

从而我们可以重写梯度：

$$
\begin{align*}
\nabla_{\phi} \mathcal{L}(\phi )  &= \nabla_{\phi} \int(\log P_{\theta}(x,z) - \log   q_{\phi})  q_{\phi} dz\\
&= \nabla_{\phi} \int  (\log P_\theta(x,z) - \log  q_{\phi})  p(\epsilon)d\epsilon \\
&= \mathbb{E}_{p(\epsilon)} (\nabla_{\phi} (\log  P_\theta(x,z)  - \log q_{\phi}))\\
&=\mathbb{E}_{p(\epsilon)}(\nabla_{z}(\cdot )) \nabla_{\phi} g_{\phi}(\epsilon,x)
\end{align*}
$$

这样，所有的不确定性被转移到了$\epsilon$上。


### 实例演示


下面我们求解一个极其简单的问题：考虑一些数据由模型$y = \beta x + \epsilon$生成，其中$\epsilon \sim N(0,\sigma^2)$且$\sigma$已知，如何使用变分推断求解$\beta$的后验分布？这里给出了简单的代码实现，手动计算了ELBO的梯度并进行梯度上升。

[代码演示：一维贝叶斯回归](https://zeroovector.github.io/blogs/attached_code/VariationalInference.ipynb)

