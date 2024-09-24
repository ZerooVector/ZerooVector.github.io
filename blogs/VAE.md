#MachineLearning 

### 回顾和整理：在 VI 中，我们是如何给出 ELBO 的？
回顾一下上一节变分推断中我们干什么：**我们希望走贝叶斯学派的路，根据数据点推断模型中的一些参数分布**。因此，我们的推导过程是：

$$
P(x,\theta) = P(\theta|x) P(x) \Rightarrow  \log P(x) = \log P(x,\theta) - \log P(\theta|x)
$$

这里的 $P(x)$ 是数据点的分布，我们虽然不知道它是什么，但是它是真实存在的，并且它与参数 $\theta$ 无关。为了近似 $P(\theta\|x)$，我们引入一个变分分布 $q(\theta\|x)$，从而有：

$$
\log P(x) =  \log  \dfrac{P(x, \theta)}{q(\theta|x)} - \log  \dfrac{P(\theta|x)}{q(\theta|x)}
$$

两侧对 $q(\theta\|x)$ 取期望：

$$
\log P(x) = \int q(\theta|x) d \theta \log  \dfrac{P(x,\theta)}{q(\theta|x)}  -  \int  q(\theta|x) d \theta \log  \dfrac{P(\theta|x)}{q(\theta|x)}
$$

注意到第二项恰好是 $D_{KL}(q(\theta\|x) , P(\theta\|x))$，我们需要使得第二项尽可能小，就要使得第一项尽可能大。于是，我们的做法就是最大化所谓 ELBO：

$$
\mathcal{L} = \mathbb{E}_{\theta \sim  q(\theta|x)}\left( \log \dfrac{P(x,\theta)}{q(\theta|x)}\right) 
$$

通过将 $\theta$ 的分布参数化为 $q_{\phi}(\theta\|x)$（注意：虽然我们将 $\theta$ 称为参数，但其实我们要求的是它的分布，它实际上是一个随机变量，而 $\phi$ 才是真正的没有随机性的、可被梯度上升训练的参数），我们可以求出 $\mathcal{L}$ 的梯度的形式：

$$
\nabla_{\phi} \mathcal{L} = \mathbb{E}_{\theta \sim q_{\phi}(\theta|x)}  \left( \log \dfrac{P(x,\theta)}{q_{\phi}(\theta|x)} \nabla_{\phi} \log  q_{\phi}(\theta|x) \right) 
$$

### VAE ：理论推导

VAE 的推导和 VI 是相似的。作为我们看到的第一款生成模型，VAE 的本职工作是**建立一个模型，调整参数，以捕捉数据的真实分布**。不妨设我们建立的模型是 $q_{\theta}(x)$，其中参数 $\theta$ 可调，而真实的数据分布是我们不知道的 $p(x)$，我们要做极大似然估计，即对于真实的数据点 $x_{1},\cdots ,x_{n}$，调整参数 $\theta$，使得 $\prod_{i=1}^{n}  q_\theta(x_{i})$ 最大。形式化地，最大似然估计可以写为：

$$
\theta  = \arg\max_{\theta}\mathbb{E}_{x \sim  p(x)} (\log q_\theta(x))
$$

我们正是要求解这个问题。由于各种历史原因（例如，为了让模型变得采样更简单、更具可解释性、更可控等等），我们将生成模型建模为一个两阶段过程：首先我们根据隐变量的分布 $q(z)$ 生成一个隐变量 $z$，然后根据条件分布 $q(x\|z)$ 再来生成数据 $x$。这样，我们可以将 $q_{\theta}(x)$ 写成 ：

$$
q_{\theta}(x)= \int  q(x,z) dz = \int q(z) q(x|z) dz  
$$

这里我们暂时隐去了参数 $\theta$。我们先推导，最后再看要参数化谁。自然地，$p$ 也可以被写作：

$$
p(x) = \int p(x,z) dz   = \int p(z|x) p(x) dx 
$$

这里我们要对 $q_\theta(x)$ 做极大似然估计，直观上看，目标函数最大的点在 $q(x)$ 与 $p(x)$ 完全相同的时刻，也就是在 $q(x,z)$ 与 $p(x,z)$ 完全相同的时刻。考虑到直接求解极大似然估计相当艰难，我们可以换个方式求解：最小化 $q(x,z)$ 和 $p(x,z)$ 之间的距离。从而我们将目标转向一个可计算的东西：最小化

$$
\begin{align*}
D_{KL}(p(x,z)||q(x,z)) &=  \int \int dx dz \ p(x,z)  \log  \dfrac{p(x,z)}{q(x,z)}\\
&= \int dx \ p(x) \int dz \ p(z|x)  \log  \dfrac{p(x,z)}{q(x,z)}\\
&= \mathbb{E}_{x \sim p(x)} \left( \int  dz  \ p(z|x)  \log  \dfrac{p(x,z)}{q(x,z)}\right)
\end{align*}
$$

这里的分子和分母都能拆，我们不妨先来拆分子。这样，我们单独拆出来一项：

$$
\mathbb{E}_{x \sim  p(x)} \left( \int dz  \ p(x|z)  \log p(x)\right)  = \mathbb{E}_{x \sim p(x) }( \log p(x))
$$

这一项是常数，在优化过程中不必理会，之后我们也略去不写。我们看其他的项：

$$
\begin{align*}
D_{KL}(p||q) &= \mathbb{E}_{x \sim p(x)} \left( \int dz \ p(z|x) \log \dfrac{p(z|x)}{q(x,z)}\right)  
\end{align*}
$$

我们已经可以很明显地观察到这个表达式与上文中 VI 的 ELBO 的相反数类似。我们继续将它推下去：

$$
\begin{align*}
\mathcal{L} &= \mathbb{E}_{x \sim p(x)} \left(- \mathbb{E}_{z \sim p(z|x)}( \log q(x| z)) + \mathbb{E}_{z \sim p(z|x)}\left(\log \dfrac{p(z|x)}{q(z)}\right)\right)\\
&= \mathbb{E}_{x \sim p(x)} \left(- \mathbb{E}_{z \sim p(z|x)}( \log q(x| z)) + D_{KL}(p(z|x)||q(z))\right)
\end{align*}
$$

这正是原文中推导得到的 VAE 损失。

注意：我们给出的以上推导和原文中的推导不尽相同。原文中的推导和我们在上一期中推导 VI 的过程很像：假设我们要做最大似然估计来最大化 $P_{\theta}(x)$，那么我们横插一个 $q_{\phi}(z\|x)$ 来逼近 $p_\theta(z\|x)$。根据上一期中的推导，有：

$$
\log P_{\theta}(x)= D_{KL} (q_{\phi}(z,x)  ||p_\theta(z,x)) + \mathcal{L}(\theta,\phi ,x)
$$

其中：

$$
\mathcal{L} = \mathbb{E}_{z \sim q_{\phi}(z|x) } ( - \log q_{\phi}(z|x)+ \log P_\theta(x,z))
$$
由于第一项 KL 散度必然大于 0，因此 $\mathcal{L}$ 是似然的下界，我们只需最大化 $\mathcal{L}$ 就能提高似然。这两种推导使用的记号不同，在下文中，我们仍然以第一种推导为准。

### VAE：参数化和前向过程
现在我们已经明晰了 VAE 在做什么：我们希望求出联合分布 $p(x,z)$，但是为了方便以及种种原因，我们找了 $q(x,z)$ 来近似。我们将 $p(x,z)$ 分拆成 $p (x, z)  = p (z\|x) p (x)$，而将 $q(x,z)$ 分拆成 $q (x, z) = q(x\|z)q(z)$。二者显然一个是编码过程。另一个则是生成过程。在损失中，$p(z\|x),p(x)$ 和 $q(x\|z)$ 三项均出现了，因此我们都要处理。作为 $q$ 的先验分布，我们可以直接将 $q(z)$ 选为标准正态分布 $\mathcal{N}(0,I)$。简便起见，我们可以将 $p(z\|x)$ 也选为正态分布 $\mathcal{N}(\boldsymbol{\mu}, \Sigma)$，其中 $\Sigma$ 是对角阵。这样，损失中的 KL 散度项是可以直接计算的：

$$
D_{KL} (p(z|x)||q(z)) = \dfrac{1}{2}  \sum_{i}  (\mu_{i}^{2} + \sigma_{i}^{2} - \ln  \sigma_{i}^{2} -1)  
$$

对于 $q(x\|z)$，我们有多种处理方法，常用的方法包括：

- 伯努利分布，此时对应的损失恰好是交叉熵损失：

$$
\ln q(x|z) = \sum_{i} (- x_{i} \ln \rho_{i}  - (1  - x_{i}) \ln (1 - \rho_{i})) 
$$

- 正态分布：此时的损失是：

$$
\ln q(x|z) = \sum_{i} \dfrac{1}{2} \left(\dfrac{ x_{i} - \mu_{i} }{\sigma_{i}}\right)^{2} + \dfrac{1}{2} \sum_{i} \ln \sigma_{i}^{2} 
$$

如果正态分布的方差 $\sigma_{i}$ 全部取为确定的常数，那么我们就得到了 MSE 损失。这就是为什么在 VAE 中最经常被使用的损失反而是 MSE 损失。

### 活用 VAE：将 VAE 直接用于聚类任务

一般来说，如果要将 VAE 用作分类任务，我们可以先使用 VAE 得到数据的隐变量 $z$，此后再对隐变量进行聚类。但是，这里我们介绍一种直接使用 VAE，一次性完成聚类的方法。设隐变量 $\mathcal{Z} = (z,y)$，其中 $z$ 是数据的编码向量，而 $y$ 是数据的类别。那么我们要最小化的是：

$$
D_{KL}(p(x,z,y)||q(x,z,y)) = \sum_{y} \iint  p(y|z) p(x|z) p(x) \ln \dfrac{ p(y|z) p(z|x) p(x)}{q(x|z) q(z|y)q(y)} dzdx 
$$

分子上仍然是编码过程：给出一个数据 $x$，可以给出它的类别 $y$，而分母上是生成过程：给出一个类别 $y$，生成数据 $x$。
不妨将 $q(y)$ 设为均匀分布，$q(z\|y)$ 如之前的 $q(x\|z)$ 一样设为正态分布，$q(x\|z)$ 也是正态分布；$p(z\|x)$ 与之前一样设置为正态分布，而 $p(y\|z)$ 则是分类器，可以随意使用一个神经网络拟合。通过对各个分布的参数和分类器 $p(y\|z)$ 的训练，我们就可以完成聚类过程。经过推导，损失函数可以写为：

$$
\mathcal{L} = \mathbb{E}_{x \sim p(x)} ( - \log q(x|z) + \sum_{y} p(y|z) \log \dfrac{p(z|x)}{q(z|y)} + D_{KL} (p(y|z) || q(y) ))
$$

### 小实验：使用VAE生成MNIST数据集

这里使用VAE生成手写数字。其中，$q(x\|z)$被选为伯努利分布：

[代码演示：手写数字生成](https://zeroovector.github.io/blogs/attached_code/VAE_MNIST.ipynb)
