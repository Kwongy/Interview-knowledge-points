### 快速排序

快速排序的主要思想是通过划分将待排序的序列分成前后两部分，其中前一部分的数据都比后一部分的数据要小，即每一轮至少使得一个数找到自己在排序后数组的正确位置，使得该位置前的数均比它小，该位置后的的数均比它大(内部不一定有序)，然后再递归调用函数对两部分的序列分别进行快速排序，以此使整个序列达到有序。


具体做法就是
1. 从数列中挑出一个元素，称为"基准"（pivot），
2. 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区结束之后，该基准就处于数列的中间位置。这个称为分区（partition）操作。
3. 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。在每次的迭代（iteration）中，它至少会把一个元素归位到它排序后的正确位置上。


递归版本代码

```python
def quickSort(nums, l0, r0):
    if l0 >= r0:  # 递归的退出条件
        return
    l = l0
    r = r0
    temp = nums[l]
    while l < r:
        while l < r and nums[r] >= temp:
            r -= 1
        nums[l] = nums[r]
        while l < r and nums[l] <= temp:
            l +=1
        nums[r] = nums[l]
    nums[l] = temp
    self.quickSort(nums, l0, l-1)
    self.quickSort(nums, l+1, r0)
```

非递归版本，可以用队列模拟栈的递归。

```python
    def quickSort(nums):
        from queue import Queue
        q = Queue()
        n = len(nums)
        q.put(0)
        q.put(n-1)
        while not q.empty():
            left = q.get()
            right = q.get()
            if left >= right:
                continue
            l, r = left, right
            temp = nums[l]
            while l < r:
                while l < r and nums[r] >= temp:
                    r -= 1
                nums[l] = nums[r]
                while l < r and nums[l] <= temp:
                    l += 1
                nums[r] = nums[l]
            nums[l] = temp
            q.put(left)
            q.put(l-1)
            q.put(l+1)
            q.put(right)
        return nums
```

#### 复杂度分析

* 最优时间复杂度：$O(nlogn)$ 最坏时间复杂度：$O(n^2)$
* 空间复杂度：$O(h)$，其中 hh 为快速排序递归调用的层数，最坏情况下需 $O(n)$ 的空间，最优情况下每次都平衡，此时整个递归树高度为$logn$，空间复杂度为 $O(logn)$。
* 稳定性：不稳定

* 如果划分是平衡的，那么快速排序算法性能与归并排序一样（性能最优）。如果划分时不平衡的，那么快速排序的性能就接近于插入排序了(性能最差)


### 归并排序

归并排序利用了分治的思想来对序列进行排序。对一个长为$n$的待排序的序列，我们将其分解成两个长度为$\frac{n}{2}$的子序列。每次先递归调用函数使两个子序列有序，然后我们再**线性合并**两个有序的子序列使整个序列有序。

递归版本归并排序

```python
def mergeSort(nums, l, r):
    if l >= r:
        return
    mid = (l + r) // 2
    self.mergeSort(nums, l, mid)
    self.mergeSort(nums, mid + 1, r)
    tmp = []
    i, j = l, mid + 1
    while i <= mid or j <= r:
        if i > mid or (j <= r and nums[j] < nums[i]):
            tmp.append(nums[j])
            j += 1
        else:
            tmp.append(nums[i])
            i += 1
    nums[l: r + 1] = tmp
```


#### 复杂度分析

* 时间复杂度:$O(nlogn)$。由于归并排序每次都将当前待排序的序列折半成两个子序列递归调用，然后再合并两个有序的子序列，而每次合并两个有序的子序列需要$O(n)$的时间复杂度，所以我们可以列出归并排序运行时间$T(n)$的递归表达式：$T(n)=2T(\frac{n}{2})+O(n)$,我们可以得出归并排序的时间复杂度为$O(nlogn)$

* 空间复杂度：$O(n)$。我们需要额外 $O(n)$空间的 $\textit{tmp}$ 数组，且归并排序递归调用的层数最深为$log_2n$，所以我们还需要额外的$O(logn)$ 的栈空间，所需的空间复杂度即为$O(n+logn)=O(n)$。



### 堆排序

堆排序的思想就是先将待排序的序列建成大根堆，使得每个父节点的元素大于等于它的子节点。此时整个序列最大值即为堆顶元素，我们将其与末尾元素交换，使末尾元素为最大值，然后再调整堆顶元素使得剩下的 $n−1$ 个元素仍为大根堆，再重复执行以上操作我们即能得到一个有序的序列。

代码：

```python
def heapSort(nums):
    buildHeap(nums)
    for i in range(len(nums) - 1, -1, -1):
        nums[0], nums[i] = nums[i], nums[0]
        maxHeapify(nums, 0, i)

def buildHeap(nums):
    for i in range(len(nums) - 1, -1, -1):
        maxHeapify(nums, i, len(nums))

def maxHeapify(nums, root, length):
    p = root
    while 2 * p + 1 < length:
        left, right = 2 * p + 1, 2 * p + 2
        if right < length and nums[right] > nums[left]:
            big = right
        else:
            big = left
        if nums[p] < nums[big]:
            nums[p], nums[big] = nums[big], nums[p]
            p = big
        else:
            break
```

详细解释一下建堆的过程maxHeapify： 从最后一个非叶子节点开始，从底向上递归的建堆，若该父节点的index记作root，则它的左右孩子节点分别为$2*root+1,2*root+2$，因此，while的判断条件就是是否存在叶子结点。 第一个if的作用是从左右节点中找出一个最大的。 第二个if的作用是判断是否需要交换父结点和子节点，当需要交换时，以子结点的树可能不满足堆的性质，因此将递归的重建子树，直至没有叶子结点。