## Two-Stage

将检测问题划分为两个部分，首先产生候选区域，然后再对候选区域分类，并进一步修正候选区域位置。

<img src="../picture/vision-od-1.png" width=700>

### RCNN

2014年RBG提出的

1. 训练分类网络并 fine-tune
2. 特征提取
    * 提取候选框（选择性搜索）
3. 训练SVM分类器， 每一个类别对应一个SVM
4. 回归器精修候选框位置，利用线性回归模型判定框的准确度。

缺点：
1. 候选框选择算法耗时严重
2. 重叠区域特征重复计算
3. 分步骤进行，过程繁琐

<img src="../picture/vision-od-3.png" width=500>

### SPPNet

同样使用Selective search等方法提取候选区域Region Proposal
对原图只做一次特征提取，计算出RP在feature map上的的映射位置。
提出Spatial Pyramid Pooling(SPP)层，无需warp RP，直接统一池化到相同的大小。

### Fast RCNN

1. 提出RoI pooling层
2. 使用多任务网络同时完成回归和分类

缺点：

使用选择性搜索找出候选框


<img src="../picture/vision-od-2.png" width=500>


### Faster RCNN

提出了Region Proposal Network(RPN)网络：用于前景和背景的区分+初步的候选框定位

RPN的流程：
1. 最后一层特征图先卷积得到新的特征图
2. 新的特征图的每一个点作为中心点，设置9个anchor
3. 计算每一个候选区域的score(是否是前景)
4. 把所有超出图片的候选区域限制在图片内，然后选取部分候选区域
5. 设置threshold=0.7，对选取的候选区域进行NMS，然后选取score最大的2000个。
6. 对这2000个候选区域与ground truth计算IoU, 如果与某个标定区域的IoU大于0.7则记为正样本，如果小于0.3则记为负样本，其余的样本被丢弃
7. 在进行回归时，随机的从6计算出的候选区域内选取256个，其中正负比例为1:1(若正样本不足128个，则用负样本替代)

<img src="../picture/vision-od-4.png" width=400>


#### RoI Pooling的局限性

在Faster R-CNN中，RoI Pooling的作用是根据预测框的坐标位置坐标在特征图中将相应的区域池化为固定大小的特征图，以便进行后面的回归。由于候选框的位置是由回归模型得到，一般来说是一个浮点数，而池化后的特征图要求尺寸固定， 故RoI Pooling这一操作存在**两次量化**的过程：
1. 将候选框边界量化为整数点坐标值
2. 将量化后的边界区域平均分割成$k\times k$个单元(类似SPP中的金字塔池化),再对每一个单元的边界进行量化。

经过上述的两次量化，此时的候选框与最开始回归出来的位置已经有了一定的偏差，这个偏差作者称为“不匹配问题”(misalignment)

#### RoI Align

RoI Align是在Mask R-CNN中提出的一种区域聚合方式，用于解决RoI Pooling操作中两次量化造成的区域不匹配问题。

具体操作是：取消量化操作，使用双线性插值算法获得坐标的浮点数的像素上的图像数值，从而将整个特征聚合过程转化为一个连续的操作，具体流程为：

1. 遍历每一个候选区域，保持浮点数边界不变，不做量化。
2. 将候选区域分割成$k\times k$个单元，每个单元的边界也不做量化
3. 在每个单元中计算固定四个坐标位置(把个单元平均分成四份后分别取它们的中心点)，用双线性插值的方法计算出这四个位置的值，然后进行最大池化。

<img src="../picture/vision-od-7.png" width=500>



## One-Stage 


不需要候选区域，一次检测同时完成分类和回归。

* CNN网络
* 回归网络
    1. 区域回归(置信度，位置，类别)
    2. Anchor机制

### SSD

1. 2016年ECCV
2. 直接回归目标类别和位置
3. 不同尺度的特征图上进行预测
4. 端到端的训练
5. 图像的分辨率较低也能保证检测的精度


SSD中引入了Prior Box，实际上与anchor非常类似以feature map上每个点的中点为中心（offset=0.5），生成一些列同心的prior box（然后中心点的坐标会乘以step，相当于从feature map位置映射回原图位置）

每个特征图会产生6个预测框，每个预测框的长为$\frac{s_k}{\sqrt{a_r}}$，宽为$s_k\sqrt a_r$, 其中$a_r\in\{1, 2, 3, \frac{1}{2}, \frac{1}{3}\}$为比率。$s_k=s_{min}+\frac{s_{max}-s_{min}}{m-1}(k-1),k\in[1,m]$, 最后一个尺寸为$s'_k= \sqrt{s_ks_{k+1}}$


<img src="../picture/vision-od-5.png" width=600>


### YOLO

2015年

<img src="../picture/vision-od-6.png" width=600>

YOLO中的置信度Confidence

$$C= Pr(object)\times IoU^{truth}_{pred}$$

## 优缺点比较

1. One-stage
优点： 速度快，避免背景错误，学到物体的泛化特征
缺点：精确度低，小物体检测不好

2. Two-stage
优点：精度高，Anchor机制，共享计算量
缺点：速度慢，训练时间长，误报高



## 性能指标

AP：PR曲线与坐标轴围成的面积。

IoU: 交并比

FPS：Frames Per Second

