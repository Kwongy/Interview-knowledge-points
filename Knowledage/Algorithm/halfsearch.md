### 二分查找

二分查找是用于有序数组查找某一个数的算法，时间复杂度为$O(log_2n)$ 


#### 寻找一个数

寻找一个数在数组内的位置，若不存在于数组中，则返回-1。

```
def halfsearch1(nums, target):
    l, r = 0, len(nums) - 1
    while l <= r:       # 这里是<=   考虑停止条件 若为l<r 则漏掉了 [r, r]这个数
        mid = l + (r - l) // 2
        if nums[mid] == target:
            return mid
        elif nums[mid] > target:
            r = mid - 1
        else:
            l = mid + 1
    return -1
```

每次折半使用`mid = l + (r - l) // 2`防止溢出(python 不会溢出， 可以直接`(r + l)/2`)

#### 寻找一个数的位置

寻找一个数在数组内的位置，若不存在则返回它应该插入的位置。

```
def halfsearch2(nums, target):
    if len(nums) < 1: return 0      # 首先需要判断是否存在nums[-1]
    if target > nums[-1]: return len(nums)  # 当查找的数大于最后一个数时直接返回数组长度
    l, r = 0, len(nums) - 1
    while l <= r:
        mid = (r + l) // 2
        if nums[mid] >= target:
            ans = mid
            r = mid - 1
        else:
            l = mid + 1
    return ans
```

当初始化`r=len(nums)`时，此时的搜索区间为`[left, right)`,循环的判断条件为`l < r`。 相反，如果初始化`r=len(nums)-1`， 此时的搜索区间为`[left, right]`，则循环判断条件为`l <= r`。 具体的可以考虑判断中止时， 是否漏掉某一个数。

#### 寻找某一个数在数组内的左侧边界

即寻找数组中第一次出现该数的位置。

```
def halfsearch3(nums, target):
    l, r = 0, len(nums)
    while l < r:
        mid = (l + r) // 2
        if nums[mid] >= target:
            r = mid
        else:
            l = mid + 1

    return l
```

与第二个类似， 采用`r = len(nums)` 使得搜索区间为`[left, right)`， 关键点是，当`nums[mid]==target`时， 不是返回mid的值，而是压缩数组的上界，使得`left`指向第一个出现该数字的位置。

#### 寻找一个数在数组内出现的右侧边界

即寻找数组中最后一次出现该数的位置。

```
def halfsearch4(nums, target):
    if len(nums) < 1: return 0      # 首先需要判断是否存在nums[-1]
    if target > nums[-1]: return len(nums)  # 当查找的数大于最后一个数时直接返回数组长度
    l, r = 0, len(nums)
    while l < r:
        mid = (l + r) // 2
        if nums[mid] <= target:
            l = mid + 1
        else:
            r = mid
    return l - 1
```


**Ref: https://www.cnblogs.com/kyoner/p/11080078.html**