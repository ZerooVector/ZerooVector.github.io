---
layout: page
permalink: /blogs/OT2/index.html
title: OT2
---


## 此即『生成』之铭 -番外3 ：最优传输的经典场论和微分几何形式

之前，我们在一篇博客中谈到：所有的有限维最优控制问题，都可以通过增广构型空间（即：将引入的拉氏乘子视作新的广义坐标）的方式转化为经典力学形式。最优传输作为一个对概率密度场 $\rho(\boldsymbol{x},t)$ 的控制问题，不难想象它必然对应了一个经典场论形式。想要构造它的经典场论形式，只需构造其拉氏密度，使得该拉氏密度可以直接导出连续性方程以及一阶最优条件。根据有限维最优控制问题的拉氏量的构造方法，我们为动态最优传输问题：

$$
\mathscr{J} =  \inf_{\rho , v} \dfrac{1}{2} \int_{0}^{1} \int_{\mathbb{R}^{n}} \dfrac{1}{2} \rho(\boldsymbol{x},t) \| v(\boldsymbol{x},t) \|^{2}   \mathrm{d}  \boldsymbol{x} \mathrm{d} t
$$

$$
\text{s.t.} \  \ \partial_{t}\rho(\boldsymbol{x},t) + \nabla_{\boldsymbol{x}} \cdot (\rho(\boldsymbol{x},t) v(\boldsymbol{x},t)) = 0  
$$

构造如下拉氏密度：

$$
\mathscr{L} = \dfrac{1}{2} \rho v_{i}v^{i} + \lambda (\partial_{0}\rho  +  \partial_{i} \rho  v^{i} + \rho  \partial_{i} v^{i})
$$

$\rho,v$ 以及一个新的场量 $\lambda$ 均在拉氏密度中出现。我们无法只使用 $\rho$ 来构造合理的拉氏量，因为我们可以猜到拉氏量中应当出现最优传输问题原本的优化目标 $\rho v_{i}v^{i}$，而 $v^{i}$ 无法使用 $\rho$ 的时空导数表达。通过拉氏量的构造我们知道：一个 $N$ 维空间中的最优传输问题在转换到经典场论形式时需要 $N+2$ 个场量进行描述。将以上拉氏量代入拉氏方程：

$$
\dfrac{ \partial \mathscr{L}}{\partial \phi^{a} } - \partial_{\mu}\left(\dfrac{\partial \mathscr{L}}{\partial (\partial_{\mu} \phi^{a}) }\right)  = 0 
$$

即可得到：
- 连续性方程： $\partial_{0}\rho   + \partial_{i} \rho  v^{i} + \partial_{i}v^{i}\rho = 0$
- HJB 方程： $\dfrac{1}{2} v^{i}v_{i} - \partial_{0} \lambda  - \partial_{i} \lambda v^{i}  = 0$
- 梯度条件：$v_{i}  = \partial_{i}\rho$

对拉氏密度进行勒让德变换将得到哈氏密度。我们引入的“广义动量”为：

$$
\pi_{\rho}  = \dfrac{\partial \mathscr{L}}{\partial (\partial_{0}\rho)} = \lambda ,\ \pi_{v_{i}} = \dfrac{\partial\scr L}{\partial (\partial_{i}v^{i})} = 0 , \pi_{\lambda}=  0
$$

可以看出，在计算广义动量的过程中需要引入 $N+1$ 个初级约束 $\phi_{v_{i}} = \pi_{v_{i}}, \phi_{\lambda}=  \lambda$ ，因此哈氏密度中需要加入 $N+1$ 个拉氏乘子进行修正。修正后的哈氏密度为：

$$
\begin{align*}
\mathscr{H} &=   \pi_{\rho} (\partial_{0}\rho) - \dfrac{1}{2} \rho  v^{i} v_{i} - \lambda \partial_{i}\rho v^{i} - \lambda \rho  \partial_{i} v^{i}+ \mu_{v^{i}} \pi_{v^{i}} + \mu_{\lambda}\pi_\lambda\\
&= - \dfrac{1}{2} \rho v_{i}v^{i} - \pi_{\rho} (\partial_{i} \rho v^{i} + \rho \partial_{i}v^{i})+ \mu_{v^{i}} \pi_{v^{i}} + \mu_{\lambda}\pi_\lambda
\end{align*}
$$

代入哈氏运动方程：

$$
\dot \phi^{a}  =  \dfrac{ \delta  H}{\delta \pi_{a}} = \dfrac{\partial \mathscr{H}}{\partial \pi_{a}} \quad \dot  \pi_{a}  = - \dfrac{\delta H}{\delta  \phi^{a} }=  - \left(\dfrac{\partial \mathscr{H}}{\partial \phi^{a} } - \partial_{i} \left(\dfrac{\partial \mathscr{H}}{\partial (\partial_{i} \phi^{a})}\right)\right)
$$

可以得到六个方程：

$$
\dot  \rho  = - (\partial_{i} \rho v^{i} + \rho \partial_{i} v^{i}), \ \dot v^{i}  = \mu_{v_{i}} , \  \dot \lambda = \mu_{\lambda}\quad  \langle 1 \sim 3  \rangle 
$$

$$
\dot  \pi_{\rho} = \dfrac{1}{2} v_{i}v^{i} - \partial_{i} \pi_{\rho} v^{i} ,  \  \dot \pi_{v^{i}} = \rho v_{i}  + \rho \partial_{i} \pi_{\rho} , \  \dot  \pi_{\lambda}= 0  \quad  \langle 4 \sim 6  \rangle 
$$

这里的方程 2,3 不提供任何信息；方程 4 是 HJB 方程；方程 5 作为次级约束给出梯度场条件；方程 6 说明了 $\pi_\lambda=0$ 这一约束始终满足。从方程 5 中，我们可以显式解出 $v_{i}  = \partial_{i} \pi_\rho$，因此实际上我们可以定义系统状态的等价类：原本系统的状态是由六个变量（三个场量、三个正则动量）描述的，现在可定义 $(\rho ,\pi_{\rho})$ 相同的点处于相同的等价类中，也就是说我们只使用 $(\rho ,\pi_{\rho})$ 这两个变量来刻画系统的状态，系统的运动方程为：

$$
\dot \rho  = - (\partial_{i}\rho \partial^{i}\pi_{\rho}+ \rho \partial_{i}\partial^{i}\pi_{\rho}), \  \dot \pi_{\rho} = - \dfrac{1}{2} \partial_{i} \pi_{\rho} \partial^{i} \pi_{\rho}
$$

该问题的拉氏和哈氏形式都告诉我们：虽然我们在构造最优传输的场论形式时需要 $N+2$ 个场量，但是实际上只需要两个场量就可以描述这个系统。

我们知道，上面的所有场量都应当视作无穷维构型空间上的坐标，所有广义动量都是构型空间上的余切矢量。一般而言，在经典力学中，我们只讨论构型空间的余切丛上的辛几何，这是通过在余切丛上指定正则辛形式来达到的；换言之，我们一般不单独讨论构型空间的几何结构。但是，在讨论最优传输问题时，我们可以在构型空间上指定一种特殊度规。
众所周知，最优传输问题中的构型空间是 $\{\int  \rho(\boldsymbol{x},t) \mathrm{d}\boldsymbol{x}=1\}$，其切空间是 $\{\int \dot \rho \mathrm{d} x = 0 \}$，通过连续性方程 $\partial_{0}\rho  + \partial_{i}(\rho  v^{i}) = 0$，我们可以将数据空间中的梯度场 $v^{i}= \partial^{i} \phi$ 和构型空间的切矢场 $\dot \rho$ 建立一一映射（这个映射不一定到上），所以不严格地说，$v^{i}$ 可以被认同为构型流形的切矢量。所以在我们这样的理解方式下， $v^{i}$ 含有“双重身份”：它既是构型流形的切矢量，又是数据流形的切矢量。我们可以定义构型流形上 $\rho(\boldsymbol{x})$ 处的度规：

$$
g_{\rho(\boldsymbol{x})}  (v(\boldsymbol{x}), u(\boldsymbol{x})) = \int \rho (\boldsymbol{x}) v(\boldsymbol{x}) \cdot  u(\boldsymbol{x}) \mathrm{d} x
$$

下面我们找出与这个度规适配的协变联络。我们将这个联络记为 $\bar \nabla$，我们定义一个矢量沿着另一个矢量的导数：

$$
\bar \nabla_{T} v  = \dfrac{\partial v}{\partial t} + \nabla_{T} v
$$

这里需要解释一下：$\nabla_{T} v$ 是数据流形上 $v$ 沿着 $T$ 的协变导数，既然我们上面说了 $v$ 有双重身份，那么它可以视作数据流形上某曲线的切矢，$t$ 就是此曲线的参数。非常容易验证这个借助数据流形定义的联络确实是与度规适配的联络，只要验证两个平移的矢量的内积保持恒定即可。设 $\rho(\boldsymbol{x},t)$ 点处的切矢为 $T(\boldsymbol{x},t)$ 两个平移的矢量为 $u (\boldsymbol{x}, t), v(\boldsymbol{x},t)$，计算它们的内积：

$$
\begin{align*}
\dfrac{\mathrm{d}g(u(\boldsymbol{x},t), v(\boldsymbol{x},t))}{\mathrm{d}t} &= \int  \dfrac{\partial ((u \cdot v) \rho)}{\partial t} \mathrm{d} x\\
&= \int ( - (T \cdot \nabla) v + (T  \cdot \nabla) u)\rho - (u \cdot v )  \nabla\cdot (\rho  T) \mathrm{d} x\\
&= - \int  \nabla ((\rho T ) (u \cdot v )) \mathrm{d}x\\
&= 0 
\end{align*}
$$

为什么右边直接写成偏导？我们要注意在研究无穷维构型流形的时候，$\boldsymbol{x}$ 并非坐标，它相当于处在“上/下标”的位置，所以它不应当随着时间变化！通过上面的计算，我们证明了上面的构造确实是协变联络。通过 $v$ 是梯度场的条件：$v  = \nabla \lambda$ 以及 $v$ 自平移的条件，我们立刻可以得到 $v$ 的演化方程：

$$
\dfrac{\partial \lambda}{\partial t} + \dfrac{1}{2} \| \nabla \lambda\|^{2} = 0 
$$

这与前面我们从经典场论表述中得到的结论是一致的。

