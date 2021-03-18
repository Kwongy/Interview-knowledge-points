### KNN

KNN是一种非参的，惰性的算法模型

直接根据提供的训练数据对测试数据进行测试，每测试一个数据时遍历整个训练集，查找与该测试样本距离最近的K的训练样本，然后统计这K个训练样本的类别，选取类别最多的类别作为测试样本的结果。


```py
import numpy as np
from collections import defaultdict

def createDataSet():
    group = np.array([[1.0, 0.9], [1.0, 1.0], [0.1, 0.2], [0.0, 0.1]])
    labels = ['A', 'A', 'B', 'B']
    return group, labels


def calDistant(vec1, vec2):
    return np.sqrt(np.sum((vec1 - vec2) ** 2))


def knnClassify(testData, trainData, trainLabel, k):
    n, m = testData.shape
    t, m = trainData.shape
    label = [0 for i in range(n)]
    for i in range(n):
        dis = []
        for j in range(t):          # 计算每个训练样本到测试样本的距离
            distant = calDistant(testData[i], trainData[j])
            dis.append((distant, j))
        dis = sorted(dis, key=lambda x: x[0])       # 排序
        dic = defaultdict(int)  
        for j in range(k):                          # 选取前k个
            dic[trainLabel[dis[j][1]]] += 1
        for key, value in dic.items():
            if value == max(dic.values()):
                label[i] = key
                break
    return label


g, l = createDataSet()
testdata = np.array([[1.2, 1.], [0.1, 0.3]])
print(knnClassify(testdata, g, l, 3))
```

#### 优缺点

优点
* 简单易用，相比其他算法，KNN算是比较简洁明了的算法。即使没有很高的数学基础也能搞清楚它的原理。
* 无需训练
* 预测效果好。
* 对异常值不敏感

缺点
* 对内存要求较高，因为该算法存储了所有训练数据
* 预测阶段可能很慢
* 对不相关的功能和数据规模敏感