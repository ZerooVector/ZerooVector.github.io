---
layout: page
permalink: /blogs/oc_mechanics/index.html
title: Optimal Control & Classical Mechanics
---

## 数学建模遗留问题：使用拉氏乘子加约束相当于扩展了构型空间


我们会在各种各样的地方遇到最优控制问题，相信有人和我一样是在 2022 年美赛 A 题的解析中（别样的自行车大赛. Jpg）看到了这个名词。首先举一个经典的例子来看看这种问题是什么样的：
设有一辆小车在光滑水平轨道上运动，初始 （$0$ 时刻）位于 $x(0)=0$ 处，现在要求在 $t=1$ 时将小车移动到 $x(1)=1$ 处。根据牛顿定律，小车的动力学方程显然为：

$$
\dot x(t) = v(t) \quad  \dot v(t) = u(t)
$$

设小车单位时间内消耗的燃料正比于小车的加速度平方（燃料质量远小于小车本身），则燃料的总消耗为：

$$
\mathscr{C} = \int_{0}^{1} C(x,u) \mathrm{d}t =  \int_{0}^{1} \dfrac{1}{2} u^{2}(t) \mathrm{d}t
$$

如何在使用尽可能小燃料的前提下完成移动小车的任务？

### 来自控制系的方法

如果去查控制系的相关参考书，他们会提供一套工程上的处理方法。构造函数：

$$
H = C(x,u) + \sum_{i=1}^{N}\lambda_{i}(t) f_{i}(x,u)
$$

其中，$f_{i}$ 是系统的第 $i$ 个状态变量满足的一阶动力学方程右手边的部分，$\lambda_{i}$ 被称为协态变量。我们会说这样的方式是"使用拉格朗日乘子引入了约束"。按照以下方法可以得到状态变量和协态变量的演化方程：

$$
\dot x_{i}= \dfrac{\partial H}{\partial \lambda_{i}}= f_{i}(x,u) \quad \dot \lambda_{i} = -\dfrac{\partial H}{\partial x_{i}} = -\dfrac{\partial C(x,u)}{\partial x_{i}} - \sum_{i=1}^{N}\lambda_{i}(t) \dfrac{\partial f_{i} (x,u)}{\partial x_{i}}
$$

最优控制律则是使得 $H$ 取到最小值的控制律，即：

$$
\dfrac{\partial H}{\partial u}=0 \Rightarrow \dfrac{\partial C(x,u)}{\partial u} + \sum_{i=1}\lambda_{i}(t) \dfrac{\partial f_{i}(x,u)}{\partial u} = 0 
$$

这套方法被称为庞特里亚金极值原理。如果利用它求解前面的例子，我们将得到：

$$
H = \dfrac{1}{2} u^{2} + \lambda_{1}v + \lambda_{2} u
$$

容易验证状态变量 $\{x,v\}$ 的演化方程与前面问题中指出的动力学方程相同，协态变量的演化方程：

$$
\dot \lambda_{1} = 0 \Rightarrow \lambda_{1} = C_{1}  \quad  \dot \lambda_{2} = - \lambda_{1} \Rightarrow \lambda_{2} = -C_{1}t + C_{2}
$$

最优控制律：

$$
\dfrac{\partial H}{\partial u} = 0  \Rightarrow  u = - \lambda_{2} = C_{1}t- C_{2}
$$

结合边界条件就可以具体确定这里的 $C_{1},C_{2}$ 应该取多少。



### 疑问与新的构造
但是，但是！看完了以上过程，你难道没有一点疑问吗？我们先不谈如何从优化的角度证明庞特里亚金极值原理（实际上我们这篇文章也不会谈），除此之外，可以发现状态变量、协态变量的演化方程与经典力学中的哈密顿正则方程相似，为什么会这样？此外，最优控制和经典力学一样都是泛函求极值的问题，但是略有差别（在经典力学中，我们先有作用量，而后通过变分得到运动方程；在最优控制中，我们做的事情更像是“完形填空”——我们既有作用量又有运动方程，只需求出 $u(t)$ 的控制律使得作用量最小化）。最优控制的问题能否被统一到经典力学框架下？本文试图回答这些问题，也就是给庞特里亚金极值原理找到一个物理意义作为解释。

我们考虑一般的系统，设其有 $N$ 个状态变量 $x_{i}, i=1,\cdots N$ 和一个控制变量 $u$，状态变量满足 $N$ 个运动方程 $\dot x_{i}  = f(x,u)$。重申一下，我们的目标是将最优控制问题的求解套路统一到经典力学框架下，这意味着我们需要构造一个拉氏量，**从这个拉氏量出发可以同时给出状态变量的演化方程、协态变量的演化方程和最优控制律**。为了实现这个目的，我们需要将协态变量放到拉氏量中.，一种构造方式如下：考虑一个自由度为 $2N+1$ 的系统，将其前 $N$ 个广义坐标 $q_{1},\cdots q_N$ 对应到状态变量 $x_{1},\cdots ,x_{N}$；将其第 $N+1$ 个广义坐标 $q_{N+1}$ 对应到控制变量 $u$；将控制理论中引入的 $N$ 个协态变量 $\lambda_{1},\cdots ,\lambda_{N}$ 对应到第 $N+1$ 到第 $2N+1$ 个广义坐标 $q_{N+1}, \cdots ,q_{2N+1}$，并构造如下拉氏量：

$$
\mathscr{L}(q,\dot q) = L(q) + \sum_{i=1}^{N} q_{N+i+1} (\dot q_{i} -f_{i}(q))
$$

为了给出与庞特里亚金极值原理中类似的方程，显然应该做出这个拉氏量对应的哈氏方程。求正则动量：

$$
p_{i} = \dfrac{\partial L}{\partial \dot q_{i}} = q_{N+1+i} \quad  p_{N+1+i} = \dfrac{\partial L}{\partial \dot q_{N+1+i}} = 0 \quad  p_{N+1}= 0
$$

我们发现所有广义坐标对时间的导数都无法通过上面的式子显式表示成 $p,q$ 的函数，但是这并不影响我们进行勒让德变换，考虑哈密顿量：

$$
\begin{align*}
\mathscr{H} &= \sum_{i=1}^{N}p_{i}\dot q_{i}  - \mathscr{L}\\
&= \sum_{i=1}^{N } p_{i}\dot q_{i} - \sum_{i=1}^{N} q_{N+i+1} (\dot q_{i} - f_{i}(q)) - L(q)\\
&= \sum_{i=1}^{N } p_{i}\dot q_{i} - \sum_{i=1}^{N} p_{i} (\dot q_{i} - f_{i}(q)) - L(q)\\
&= - L(q) + \sum_{i=1}^{N} p_{i}f_{i}(q) 
\end{align*}
$$

下面导出哈密顿正则方程。前面做勒让德变换时我们导出了 $p_{N+1}=0, p_{N+1+i} = 0$，这说明我们构造的拉氏量有退化性。在 Dirac 的约束理论中，由于拉氏量的退化性在勒让德变换时产生的约束被称为初级约束，每出现一个初级约束都需要在哈氏量中补充一个拉氏乘子（注意这里的拉氏乘子是由退化的拉氏量带来的，是在使用哈氏理论处理问题时自然出现的，而非像庞特里亚金极值原理中人为引入的）。因此我们在哈氏量中补充 $N+1$ 个修正项，使得增广的哈氏量变成：

$$
\mathscr{H}^{\dagger} = \mathscr{H} + \phi_{N}(p,q) \lambda_{N}(t) +  \sum_{i=1}^{N} \phi_{N+1+i}(p,q) \lambda_{N+1+i}(t)
$$

其中，$\phi_{N}(p, q) = p_{N} , \phi_{N+1+i}(p, q) = p_{N+1+i}$。
显然现在关于正则坐标、正则动量的方程分别分为三组：关于下标 $1,\cdots,N$ 的；关于下标 $N+1,\cdots,2N+1$ 的；关于下标 $N+1$ 的。我们分别写出这些方程，下文中，我们均取 $i=1,\cdots N$：

$$
\dot q_{i} =  \dfrac{\partial \mathscr{H}^{\dagger} }{\partial p_{i}} =  f_{i}(q) \quad \langle 1\rangle 
$$

这对应于各个状态变量的演化方程。

$$
\dot q_{N+1+i} =  \dfrac{\partial \mathscr{H}^{\dagger} }{\partial p_{N+1+i}} = \lambda_{N+1+i}(t) \quad \langle2 \rangle 
$$

这组方程不提供任何信息

$$
\dot p_{i} = - \dfrac{\partial \mathscr{H}^{\dagger}}{\partial q_{i}} =   -\sum_{j=1}^{N} p_{j} \dfrac{\partial f_{j}(q)}{\partial q_{i}} + \dfrac{\partial L(q)}{\partial q_{i}} \quad \langle3 \rangle
$$

这是协态变量的演化方程。

$$
\dot p_{N+1+i} = - \dfrac{\partial \mathscr{H}^{\dagger}}{\partial q_{N+1+i}} =  0 \quad   \langle 4\rangle 
$$
这个方程保证了 $\phi_{N+1+i} = p_{N+1+i}=0$ 的约束一定满足。

下面考虑控制变量。注意：

$$
\dot  q_{N+1} =  \dfrac{\partial \mathscr{H}^{\dagger}}{\partial p_{N+1}} =  \lambda_{N+1} \quad \langle 5 \rangle 
$$

$$
\dot p_{N+1} = - \dfrac{\partial \mathscr{H}^{\dagger}}{\partial q_{N+1}} =- \sum_{i=1}^{N}p_{j} \dfrac{\partial f_{j}(q)}{\partial q_{N+1}} + \dfrac{\partial L}{\partial q_{N+1}} = 0 \quad  \langle 6 \rangle 
$$

$\langle 6\rangle$ 是初级约束与动力学结合后得到的次级约束，从中可以解出 $q_{N+1}$，由此确定最优控制律，同时可求出 $\langle 5 \rangle$ 中的 $\lambda$（当然，单独使用 $\langle 5 \rangle$ 不起任何作用）。如果 $\langle 6 \rangle$ 无法解出 $q_{N+1}$，还应该继续向下讨论：记 $\psi (p, q) = \dot p_{N+1}$，它应当继续满足自洽性条件：

$$
\dot \psi  = \{\psi,H\} +  \{\psi  ,\phi_{N+1}\} \lambda_{N+1} + \sum_{i=1}^{N} \{\psi  ,\phi_{N+1+i}\} \lambda_{N+1+i} = 0 
$$

这可以得到新的方程。

容易注意到只要将我们的拉氏量中的 $L(q)$ 换成最优控制中的 $-C(x,u)$，我们立刻得到与庞特里亚金定理几乎相同的表述（仅在状态变量的运动方程上差一组常数）。

