---
layout: page
permalink: /blogs/flowmatchinge1/index.html
title: FME1
---

## 此即『生成』之铭-番外1 ：Your Flow Matching Model Secretly Learns the Score Function


知所周众，Score Matching 的反向方程是这样的：

$$
\dfrac{\mathrm{d}x_{i}}{\mathrm{d}t} = f_{i}(x,t)  \mathrm{d}t -  \dfrac{1}{2} g^{2}(x,t)\partial_{i} \log p(x,t) \mathrm{d }t 
$$

这个方程里面是显含 $\log p(x,t)$ 的，这极大地便利了我们进行条件生成任务，比如最简单的方式是所谓分类器制导生成：

$$
\dfrac{\mathrm{d}x_{i,l}}{\mathrm{d}t} = f_{i}(x,t)  \mathrm{d}t -  \dfrac{1}{2} g^{2}(x,t)\partial_{i} \log p(x,t) \mathrm{d }t  - \kappa \partial_{i} \log p(l|x) \mathrm{d}t
$$

其中，$\partial_{i} \log p(l\|x)$ 是一个在噪声数据上训练的分类器。但是，Flow Matching 的向量场 $v(x,t)$ 看似是“设计”出来的。比如，当我们希望将数据从初始分布 $p$ 运往终末分布 $q$ 时，我们先给出将 $p$ 运送到终末分布中的一个数据点 $x_{1} \sim q$ 的过程：

$$
p(t,x|x_{1}) = \mathcal{N}(x|\alpha(t) x_{1} , \sigma^{2}(t) I)
$$

其中 $\alpha (0)=1,\alpha (1)=0,\sigma^{2} (0)=1,\sigma^{2} (1)=0$ 。积分掉 $x_{1}$ 就得到将 $p$ 转移到 $q$ 的过程：

$$
p(t,x) = \int p(t,x|x_{1})q(x_{1})  \mathrm{d}x_{1}
$$

Score Function 可以展开成：

$$
\begin{align*}
\partial_{i} \log  p(t,x) \\
=& \dfrac{\partial_{i} p(t,x)}{p(t,x)} \\
=& \dfrac{1}{p(t,x)} \partial_{i}\left(\int p(t,x|x_{1}) q(x_{1}) \mathrm{d}x_{1}\right) \\
=& \dfrac{1}{p(t,x)} \int \partial_{i}p(t,x|x_{1}) q(x_{1})\mathrm{d}x_{1}\\
&= \dfrac{1}{p(t,x)} \int \partial_{i} \log  p(t,x|x_{1})  p(t,x|x_{1}) q(x_{1}) \mathrm{d}x_{1} \quad  (\star)
\end{align*}
$$

边缘速度场 $u(t,x)$ 能否写成类似的形式？由于条件速度场和边缘速度场分别满足连续性方程：

$$
\partial_{t}p(t,x|x_{1}) + \partial_{i} [p(t,x|x_{1}) u(t,x|x_{1})]^{i} = 0
$$

$$
\partial_{t} p(t,x) + \partial_{i}[p(t,x) u(t,x)]^{i} = 0 
$$

对比两式，要使得它们等效，需要：

$$
 u_{i}(t,x) = \dfrac{1}{p(t,x)}\int  u_{i}(t,x|x_{1})p(t,x|x_{1})  q(x_{1})\mathrm{d}x_{1}   \quad (\star \star )
$$

这里可以看到 $(\star )$ 和 $(\star \star)$ 的形式相似。由于条件速度场是：

$$
u_{i}(t,x|x_{1}) = \dfrac{\dot \sigma(t)}{\sigma(t)} (x_{i} - \alpha(t) x_{1,i}) + \dot \alpha(t)  x_{1,i} 
$$

经过简单的初等运算可以验证：

$$
u_{i}(t,x|x_{1}) = \dfrac{\dot \alpha(t)}{\alpha(t)} x_{i} + \left(\dot \sigma(t) \sigma(t) - \sigma^{2}(t)  \dfrac{\dot  \alpha(t)}{ \alpha(t)}\right) \partial_{i} \log  p(t,x|x_{1}) 
$$

将上式代入 $(\star\star)$ 即得：

$$
u_{i}(t,x) = \left(\dot \sigma(t) \sigma(t) - \sigma^{2}(t)  \dfrac{\dot  \alpha(t)}{ \alpha(t)}\right) \partial_{i} \log p(x,t) + \dfrac{\dot \alpha(t)}{\alpha(t)} x_{i}
$$

这个方程给出了 Flow Matching 学习到的速度场和 Score Function 之间的关系。有了这个关系，我们可以在 Flow 流经的路径上训练一个分类器模型 $p(l\|x)$，并直接使用它引导生成：

$$
u_{i}(t,x|l) = u_{i}(t,x) + \kappa \left(\dot \sigma(t) \sigma(t) - \sigma^{2}(t)  \dfrac{\dot  \alpha(t)}{ \alpha(t)}\right) \partial_{i} \log (l|x)
$$

特别地，对于最简单的线性插值路径有 $\sigma (t) =1-t,\alpha (t) = t$，计算可得：

$$
u_{i}(t,x|l) = u_{i}(t,x) + \kappa \left( \dfrac{t-1}{t}\right) \partial_{i} \log (l|x)
$$
