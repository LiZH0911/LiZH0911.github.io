# 算法

相关链接：

- [JavaGuide-算法](https://javaguide.cn/cs-basics/algorithms/)

## 一、复杂度分析

### 1.1 **常见复杂度量级**


### 1.2 **循环复杂度**

**普通循环**看执行次数：

```java
// O(n)
for (int i = 0; i < n; i++) {
    // O(1)
}
```

**嵌套循环**不能只看有几层，要看每层真实次数：

```java
for (int i = 0; i < n; i++) {
    for (int j = i; j < n; j++) {
        // O(1)
    }
}
```

内层次数是 `n + (n - 1) + ... + 1`，也就是 `n(n + 1) / 2`，复杂度记作 `O(n^2)`

如果**循环变量每次翻倍**，通常是 `O(logn)`：

```
for (int i = 1; i < n; i *= 2) {
    // O(1)
}
```

**双指针**：

```java
while (left < n && right < n) {
    if (needMoveRight()) {
        right++;
    } else {
        left++;
    }
}
```

虽然是 `while` 里嵌了条件，但 `left` 和 `right` 都只单调递增，最多各移动 n 次，所以整体是 `O(n)`，不是 `O(n^2)`

### 1.3 **递归复杂度**

**二分查找**每次只进入一个子问题，规模减半：

```java
int binarySearch(int[] nums, int target, int left, int right) {
    if (left > right) {
        return -1;
    }
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) {
        return mid;
    }
    if (nums[mid] < target) {
        return binarySearch(nums, target, mid + 1, right);
    }
    return binarySearch(nums, target, left, mid - 1);
}
```

递归深度是 `logn`，每层只做 `O(1)` 工作，所以时间复杂度是 `O(logn)`，递归栈空间是 `O(logn)`。

**归并排序**每层拆成两个子问题，每层合并总工作量是 `O(n)`，层数是 `logn`，所以时间复杂度是 `O(nlogn)`，额外数组空间是 `O(n)`。


### 1.4 **空间复杂度**

空间复杂度看算法运行过程中额外使用的空间，常见来源有：

* 新建数组、哈希表、队列、栈。
* 递归调用栈。
* 排序或合并时的辅助空间。
* 结果集是否算额外空间，要看题目要求。面试时可以主动说明。

## 二、二分查找

## 二、二分查找