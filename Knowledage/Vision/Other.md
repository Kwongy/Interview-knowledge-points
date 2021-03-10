在此记录零碎的知识点

### 1. 目标检测为什么使用SmoothL1损失？

$L_2=||f(x)-Y||^2$，其导数为$2(f(x)-Y)f'(x)$，当预测值与目标真实值相差过大时，会造成梯度爆炸。

$L_1=|f(x)-Y|$, 其导数为$|f'(x)|, x\neq0$,当$x=0$时不存在导数。因此$L_1$不连续。

因此采用SmoothL1。$$\text{Smooth L}_1(x)= \begin{cases}0.5x^2  \quad   \text{if} |x|<1\\|x|-0.5 \quad   \text{otherwise}\end{cases}$$

### 2.标签平滑为什么有效？

1. 标签平滑使得网络不会绝对相信标签，有利于缓解标签错误的问题。
2. 可以减小网络过拟合的问题。以逻辑回归为例，$\hat y_i$永远不可能变为0或1，标签平滑使得网络不会让参数$w$学的过大。
$$\hat y_i=\frac{1}{1+\exp{(-w^Tx_i)}}$$

### 3. 介绍一下Focal loss

Focal Loss: $\text{FL}(p_t)=-\alpha_t(1-p_t)^\gamma\log(p_t)$,其中$\alpha_t=\begin{cases}\alpha  \quad\quad \quad  \text{if}\quad y=1\\1-\alpha \quad   \text{otherwise}\end{cases}$

一般情况下$\alpha=0.25,\gamma=2$

Focal loss是一种解决难样本挖掘的损失函数，easy examples虽然它们单个example的loss可以收敛到很小，但是由于它们的数量过于庞大，把一些hard example的loss覆盖掉。导致求和之后他们依然会支配整个批次样本的收敛方向。具体做法是在CE损失中乘一个$(1-p_i)^\gamma$，举个例子，当一个$\gamma=2$时，一个易样本($p_i=0.9$)，它的损失会降低100倍，而一个难样本($p_i=0.5$)，它的损失只降低了4倍。 这样可以加大难样本的权重，使得求和后的梯度方向偏向难样本。

### 4. 单个卷积层的计算复杂度(时间复杂度和空间复杂度)

设该层的kernel大小为$k$,输入和输出通道数分别为$c_{in},c_{out}$，该层输出特征图的大小为$m$，则

该层的时间复杂度为$O(m^2\cdot k^2\cdot c_{in}\cdot c_{out})$,空间复杂度为$O(k^2\cdot c_{in}\cdot c_{out})$

**注：当我们需要裁剪模型时，由于卷积核的尺寸通常已经很小，而网络的深度又与模型的能力紧密相关，不宜过多削减，因此模型裁剪通常最先下手的地方就是通道数。**

### 5. Xaiver初始化

在前馈神经网络中，如果权重初始化为方差为1，那么随着网络深度增加，数据的分布方差会减小，为了解决这个问题可使用Xaiver初始化。
设全连接神经网络某一层的一个神经元$y=W\vec x+\vec b=\vec w_1x_1+\cdots+\vec w_nx_n$
那么，它的方差$var(y)=var(W\vec x+\vec b)=var(\vec w_1x_1+\cdots+\vec w_nx_n)=var(\vec w_1x_1)+\cdots+var(\vec w_nx_n)$,又由于独立同分布$var(y)=N\cdot var(\vec wx)$
$var(\vec wx)=E(\vec w^2x^2)-E^2(\vec wx)=var(\vec w)var(x)$(由于x和w的期望均为0).
要使$var(y)=var(x)$，则有$N\cdot var(\vec w)=1$，因此$var(\vec w)=\frac{1}{N}$,又由于前向传播中的$N_{in}$和反向传播中的$N_{out}$不一定相同，因此折中将$var(\vec w) = \frac{2}{N_{in} + N_{out}}$.

### 6. KaiMing初始化

由于Xaiver初始化中，假设x的分布的均值为0，但实际中使用ReLU函数会使得x的分布的均值不为0。因此，需要使用积分求得$E(x^2)$。针对不同的激活函数，论文中推导出了不同的标准差$std$。


### 7. 学习率衰减的必要性

学习率过大，在算法优化的前期会加速学习，使得模型更容易接近局部或全局最优解。但是在后期会有较大波动，甚至出现损失函数的值围绕最小值徘徊，波动很大，始终难以达到最优。