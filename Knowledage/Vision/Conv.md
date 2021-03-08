## 一、卷积

首先放两张普通卷积运算的图

<img src="../picture/vision-conv-1.jpg" width = "400" height = "200" alt="" align=center />

由上图可知，卷积核的组数取决于要输出feature map的个数，每组卷积的个数取决于输入feature map的channel数。

<img src="../picture/vision-conv-1.png" width = "600" height = "300" alt="" align=center />

由上图可知，普通的卷积操作可以等价转化为矩阵相乘。具体做法是将输入的feature map横着拉直，并竖着拼接作为左乘矩阵，将kernel竖着拉直，每组卷积核横着排，组内卷积核竖着排，一起组成右乘矩阵。

### 卷积层前向传播算法

以单通道卷积为例，输入为shape=3的矩阵$X$，kernel=2, stride=1, 输出为$2\times 2$的矩阵$Y$。即$XconvK=Y$

$$\left(\begin{matrix}
x_{11} & x_{12} & x_{13} \\ x_{21} & x_{22} & x_{23} \\ x_{31} & x_{32} & x_{33} 
\end{matrix}\right) conv \left(\begin{matrix} k_{11} & k_{12}\\ k_{21} & k_{22} \\\end{matrix}\right) = \left(\begin{matrix} y_{11} & y_{12}\\ y_{21} & y_{22} \\\end{matrix}\right)$$

根据卷积的运算规则，则有

$$\left(\begin{matrix} y_{11} \\ y_{12} \\ y_{21} \\ y_{22} \end{matrix}\right) = \left(\begin{matrix} x_{11}k_{11} + x_{12}k_{12} + x_{21}k_{21}+ x_{22}k_{22} \\ x_{12}k_{11} + x_{13}k_{12} + x_{22}k_{21}+x_{23}k_{22} \\ x_{21}k_{11} + x_{22}k_{12} + x_{31}k_{21}+x_{32}k_{22} \\ x_{22}k_{11} + x_{23}k_{12} + x_{32}k_{21}+x_{33}k_{22} \end{matrix}\right) = \left(\begin{matrix} x_{11} & x_{12} & x_{21} & x_{22} \\ x_{12} & x_{13} & x_{22} & x_{23} \\ x_{21} & x_{22} & x_{31} & x_{32} \\ x_{22} & x_{23} & x_{32} & x_{33} \end{matrix}\right)\cdot \left(\begin{matrix} k_{11} \\ k_{12} \\ k_{21} \\ k_{22}  \end{matrix}\right)$$

将上述变形后的矩阵分别记为$\hat X,\hat K, \hat Y$,则有$\hat Xconv\hat K = \hat Y$


### 反向传播

设前向传播公式为$a^l=\sigma(z^l)=\sigma(a^{l-1}\cdot W^l+b)$, $\delta^l$ 为第$l$层的误差。$\odot$指哈尔玛(Hadamard)积，是在矩阵求导中使用的一种运算。具体可类比普通函数微分$df=f'dx$，矩阵函数的微分$d\sigma(x)=\sigma'(x)\odot dx$。损失函数为$J(W,b)=\frac{1}{2}||a^L-y||^2_2$, $*$指的是步长为1且不含补丁的卷积操作。$rot_{180}$代表矩阵旋转180度。

#### 已知上一层的误差$\delta^{l+1}$，求当前层误差$\delta^l$

$$\delta^l=\frac{\partial J(W,b)}{\partial z^l}=(\frac{\partial z^{l+1}}{\partial z^l})^T\frac{\partial J(W,b)}{\partial z^{l+1}}=(\frac{\partial z^{l+1}}{\partial z^l})^T\delta^{l+1}=\delta^{l+1}*rot_{180}(W^{l+1})\odot\sigma'(z^l)$$

#### 已知当前层的误差$\delta^l$，推导$W,b$的梯度

$$\frac{\partial J(W,b)}{\partial W^l}=\frac{\partial J(W,b)}{\partial z^l}\frac{\partial z^l}{\partial W^l}=a^{l-1}*\delta^l$$

$$\frac{\partial J(W,b)}{\partial b^l}=\sum_{u,v}(\delta^l)_{u,v}$$

而对于$b$,则稍微有些特殊，因为$\delta^l$是高维张量，而$b$只是一个向量。通常的做法是将$\delta^l$的各个子矩阵的项分别求和，得到一个误差向量，即为$b$的梯度：


## 二、深度可分离卷积

<img src="../picture/vision-conv-2.jpg" width = "350" height = "150" alt="" align=center />


<img src="../picture/vision-conv-3.jpg" width = "350" height = "150" alt="" align=center />

深度可分离卷积，首先对输入的每一个channel在deep方向上使用一个1维卷积核进行卷积，得到融合了局部信息的特征图，然后在Pointwise上卷积，使用大小为1的卷积核提升特征图的channel。

## 三、分组卷积

<img src="../picture/vision-conv-2.png" width = "200" height = "200" alt="" align=center />

分组卷积是把输入通道$C_{in}$和输出通道$C_{out}$都平均分成了n份（前提是$C_{in}$和$C_{out}$都能被n整除），然后把平均分后的输出通道concat起来，又变成了原来一样的通道数，可用

## 四、空洞卷积


<img src="../picture/vision-conv-3.png" width = "300" height = "100" alt="" align=center />

空洞卷积也叫膨胀卷积，就是在卷积核的中间加入空洞，使得卷积核变大(形式上，实际上没有增加参数)。除了常规的卷积核参数外，膨胀卷积还有独特的参数膨胀率，代表两个卷积参数之间的距离。每一层的感受野从$k*k$变为$[k+(r-1)*(k+1)]*[k+(r-1)*(k+1)]$



参考文献：
[卷积神经网络(CNN)反向传播算法](https://www.cnblogs.com/pinard/p/6494810.html)


