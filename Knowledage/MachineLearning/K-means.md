### K-means

K-means 是我们最常用的基于欧式距离的聚类算法，其认为两个目标的距离越近，相似度越大。

K-means的思想如下

1. 选择初始化的 k 个样本作为初始聚类中心$a= a_1, a_2, a_3, a_4$；
2. 针对数据集中每个样本 $x_i$ 计算它到 $k$ 个聚类中心的距离并将其分到距离最小的聚类中心所对应的类中；
3. 针对每个类别 $a_j$ ，重新计算它的聚类中心 $a_j = \frac{1}{|c_i|}\sum_{x\in c_i}x$（即属于该类的所有样本的质心）；
4. 重复上面 2 3 两步操作，直到达到某个中止条件（迭代次数、最小误差变化等）。


#### 伪代码

```
获取数据 n 个 m 维的数据
随机生成 K 个 m 维的点
while(t)
    for(int i=0; i < n;i++)
        for(int j=0; j < k;j++)
            计算点 i 到类 j 的距离
    for(int i=0;i < k;i++)
        1. 找出所有属于自己这一类的所有数据点
        2. 把自己的坐标修改为这些数据点的中心点坐标
end
```

时间复杂度：$O(tknm)$ ，其中，t 为迭代次数，k 为簇的数目，n 为样本点数，m 为样本点维度。

空间复杂度：$O(m(n + k))$ ，其中，k 为簇的数目，m 为样本点维度，n 为样本点数。

损失函数： 最小误差平方和(基于距离) $J(c, \mu) = \sum_{i=1}^k||x^{(i)}-\mu_{c^{(i)}}||^2$
，其中$\mu_{c^{(i)}}$表示第i类的均值。


#### Python使用Numpy实现

```python
import numpy as np
import random


def calDistant(vec1, vec2):  # 计算任意两个向量的欧式距离
    return np.sqrt(np.sum((vec1 - vec2) ** 2))


def randCreate(data, k):  # 随机生成k个聚类中心
    n = data.shape[1]
    centroids = np.zeros((k, n))
    for i in range(n):
        maxn = np.max(data[:, i])
        minn = np.min(data[:, i])
        for j in range(k):
            centroids[j, i] = minn + (maxn - minn) * random.random()
    return centroids


def kmeans(data, k):
    centroids = randCreate(data, k)
    n, m = data.shape
    clusterAllocation = np.zeros((n, 2))  # 第一列存放该数据所属的中心点，第二列是该数据到中心点的距离
    change = True
    while change:
        change = False
        for i in range(n):  # 计算各个样本与聚类中心的距离，重新分配样本所属类别
            dis = float("inf")
            aim = -1
            for j in range(k):
                distij = calDistant(data[i, :], centroids[j, :])
                if distij < dis:
                    dis = distij
                    aim = j
            if clusterAllocation[i, 0] != aim:
                change = True
            clusterAllocation[i, :] = aim, dis
        for i in range(k):   #  重新计算k个质心的位置
            cent = data[np.where(clusterAllocation[:, 0] == i)]
            centroids[i] = np.mean(cent, axis=0)

    return centroids, clusterAllocation


x = np.array([[1, 2], [1.5, 1.8], [5, 8], [8, 8], [1, 0.6], [9, 11]])
print(x)
centroids, clusterAllocation = kmeans(x, 2)
print(centroids)
print(clusterAllocation)
```


#### 优缺点分析

优点：

* 容易理解，聚类效果不错，虽然是局部最优， 但往往局部最优就够了；
* 处理大数据集的时候，该算法可以保证较好的伸缩性；
* 当簇近似高斯分布的时候，效果非常不错；
* 算法复杂度低。

缺点

* K 值需要人为设定，不同 K 值得到的结果不一样；
* 对初始的簇中心敏感，不同选取方式会得到不同结果；
* 对异常值敏感；
* 样本只能归为一类，不适合多分类任务；
* 不适合太离散的分类、样本类别不平衡的分类、非凸形状的分类。


#### k的取值方式

K 值的选取对 K-means 影响很大，这也是 K-means 最大的缺点，常见的选取 K 值的方法有：手肘法、ISODATA、Gap statistic 方法。



##### 手肘法

根据聚类性质容易知道，当聚类的类别增加时，分类的总误差会减小。因此，可以测试不同的k的取值，并统计此时整个样本的距离误差大小。选择误差和处于拐点时的k。


<img src="../picture/ml-kmeans-1.jpg" width = "400" height = "300" alt="" align=center />


##### ISODATA

ISODATA 的全称是迭代自组织数据分析法。它解决了 K 的值需要预先人为的确定这一缺点。而当遇到高维度、海量的数据集时，人们往往很难准确地估计出 K 的大小。ISODATA 就是针对这个问题进行了改进，它的思想也很直观：当属于某个类别的样本数过少时把这个类别去除，当属于某个类别的样本数过多、分散程度较大时把这个类别分为两个子类别。