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

### 4.
