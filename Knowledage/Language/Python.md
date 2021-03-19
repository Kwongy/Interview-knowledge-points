### 1. 默认字典

如果查询的键不在字典内就会报错，因此可以可以使用默认字典来解决。

```python
from collections import defaultdict
dic = defaultdict(int)
ls = [1,6,4,3,2,9,1]
for num in ls:
    dic[num] += 1
print(dic[5])
```


### 2. Python标准库模块之heapq

heapq库中的堆默认是最小堆

创建堆  `heap.heapify(list)`
增加元素    `heapq.heappush(heap, item)`
弹出最小值  `heapq.heappop(heap)`
删除并返回最小元素值，添加新的元素值    `heapq.heapreplace(heap, item)`
查询堆中的最大n个元素   `heapq.nlargest(n,heap) `
查询堆中的最小n个元素   `heapq.nsmallest(n,heap) `

```python
import heapq
nums = [1, 2, 4, 5, 3]
heapq.heapify(nums)
heapq.heapreplace(nums, 23)
heapq.heappop(nums)
heapq.heappush(nums, 18)
print(heapq.nlargest(3, nums))
## [23, 18, 5]
```

### 3. 深拷贝和浅拷贝

直接赋值: 对象的引用,也就是给对象起别名
浅拷贝: 拷贝父对象，但是不会拷贝对象的内部的子对象。
深拷贝: 拷贝父对象. 以及其内部的子对象

对于不可变对象,修改赋值后的新变量,不会对原有变量造成任何影响; 对于可变对象,修改赋值后的变量,会对原有的变量造成影响,会导致其 value 值的改变,但是其id 值保持不变。

浅拷贝构建一个复合对象,然后将原有复合对象包含的对象插入到新的复合对象中,浅拷贝后,新复合对象包含的对象(可变或者不可变)的 id 值和原有对象包含的对象的 id 值相同。深拷贝构建一个复合对象,然后递归的将原有复合包含的对象的副本插入到新的复合对象中，深拷贝后,新的复合对象包含的对象,若对象为不可变对象,则 id 值保持不变,若对象为可变对象,则 id 值发生改变；

```python
import copy
a=[1,2,3,4,5,['a','b']]
b=a #赋值，传对象的引用
c=copy.copy(a)  #对象拷贝，浅拷贝
d=copy.deepcopy(a)  #对象拷贝，深拷贝
print(id(b[0]), id(b[-1]))  # 140731981244192 1964460366336
print(id(c[0]), id(c[-1]))  # 140731981244192 1964460366336
print(id(d[0]), id(d[-1]))  # 140731981244192 1964460364544
```


### 4. 自定义排序规则

针对`sorted`函数中的`key`，除了可以使用lambda表达式外，还可使用自定义函数实现自定义排序规则

```python
nums=[43,30,5,9,3]
nums = [str(i) for i in nums]
def cmp(x, y):
    a, b = x + y, y + x
    if a > b: return 1
    elif a < b: return -1
    else: return 0
nums = sorted(nums, key = functools.cmp_to_key(cmp))
print("".join(nums))    # "3034359"
```

另外 `sorted()` 会返回一个排序后的数组， 原数组不变， 而`iter.sort()`会在原数组中排序。

### 5.