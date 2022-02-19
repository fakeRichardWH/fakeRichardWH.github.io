# leetcode-找到两数组中的中位数

# 题目
- 前提条件： 两个有序数组均为正序列，且两个数组有且仅有一个会是空数组
- `nums1.length = m`,`nums1.length = n`
  - 如果`m+n == 2k`,那么中位数为第`k`和`k+1`位置的数据的之和的一半 也就是$\frac{m + n}{2}$ 和$\frac{m + n}{2} + 1$
  - 如果`m+n == 2k+1`,那么中位数为`k+1`位置的数据
 $\frac{m + n}{2} + 1$
# 分析
给定两个有序数组，要求找到两个有序数组的中位数，最直观的思路有以下两种：
- 注意： 当两个数组长度之和为偶数时，其中位数为总长度`n`的$\frac{n - 2}{2}$位置上和$\frac{n}{2}$位置上的取值之和的一半
- 当两个数组长度之和为偶数时，其中位数为总长度`n`的$\frac{n}{2}$位置上的数值
# 思路1:
使用归并的方式，合并两个有序数组，得到一个大的有序数组。大的有序数组的中间位置的元素，即为中位数
```java
public double findMedianSortedArray(int[] nums1, int[] nums2) {
    int[] nums = new int[nums1.length + nums2.length];
    int m = nums1.length;
    int n = nums2.length;
    //special case
    if (m == 0) {
        if (n % 2 == 0) {//长度为偶数
            return (nums2[n / 2 - 1] + nums2[n / 2]) /2.0;
        } else {
            return (double) nums2[n / 2];
        }
    }
    if (n == 0) {
        if (m % 2 == 0) {//长度为偶数
            return (nums2[m / 2 - 1] + nums2[m / 2]) /2.0;
        } else {
            return (double) nums2[m / 2];
        }
    }
    //合并两个有序数组
    int count = 0;
    int i = 0, j = 0;
    while (count != (m + n)) {//这个逻辑不是唯一的
    //可以改写
        if (i == m) {//nums1 的指针已经到了边界
            while (j != n) {
                nums[count++] = nums2[j++];
            }
        }
        if (j == n) {//nums2 的指针已经到了边界
            while (i != m) {
                nums[count++] = nums2[i++];
            }
        }
        if (nums1[i] <= nums2[j]) {
            nums[count++] = nums1[i++];
        } else {
            nums[count++] = nums1[j++];
        }
    }
    //返回合并后数组中位数
    if (count % 2 == 0) {//长度为偶数
            return (nums2[count / 2 - 1] + nums2[count / 2]) /2.0;
        } else {
            return (double) nums2[count / 2];
    }
}
```

# 思路2:
我们不需要将两个数组真的合并，我们只需要找到中位数在哪里就可以了。

开始的思路是写一个循环，然后里边判断是否到了中位数的位置，到了就返回结果，但这里对偶数和奇数的分类会很麻烦。当其中一个数组遍历完后，出了`for`循环**对边界的判断也会分几种情况**。总体来说，虽然复杂度不影响，但代码会看起来很乱。

首先是怎么将奇数和偶数的情况合并一下。

用 `len` 表示合并后数组的长度:
1. 如果是奇数，我们需要知道第 `(len+1）/2` 个数就可以了，如果遍历的话需要遍历 `(int) (len/2) + 1` 次。比如`len`长度为5,那么我们要拿到第三个数，需要遍历三次
2. 如果是偶数，我们需要知道第 `len/2`和 `len/2+1` 个数，也是需要遍历 `len/2+1` 次。所以遍历的话，奇数和偶数都是 `len/2+1` 次。比如`len`长度为6,那么我们要拿到第三个和第四个数,可以遍历四次然后减1,拿到左边的数字

返回中位数的话，奇数需要最后一次遍历的结果就可以了，**偶数需要最后一次和上一次遍历的结果**。所以我们用两个变量`pre`和`cur`，`cur`保存当前循环的结果，在每次循环前将`cur`的值赋给`pre`。这样在进行最后一次循环的时候,`pre`将得到 `cur`的值，也就是上一次循环的结果，接下来`cur`更新为最后一次的结果。

循环中该怎么写，什么时候`nums1`数组后移，什么时候`nums2`数组后移。用`p1`和`p2`分别表示当前指向`nums1`数组和`nums2`数组的位置。如果`p1`还没有到最后并且此时`nums1`位置的数字小于`nums2`位置的数组，那么就可以后移了。也就是`p1 ＜ m && nums1[p1]< nums2[p2]`。

但如果`nums2`数组此刻已经没有数字了，继续取数字`nums2[p2]`，则会越界，所以判断下`p2`是否大于数组长度了，这样 || 后边的就不会执行了，也就不会导致错误了，所以增加为 `p1 ＜ m && (p2 >= n || nums1[p1] < nums2[p2])` 。其中`p2 >= n`的优先级更高，因为即便这个条件为假也不妨碍后面的为真时的结果，但是这个如果为真，那就一定要移动`p1`指针了。
```java
public double findMedianSortedArray(int[] nums1, int[] nums2) {
    int len1 = nums1.length;
    int len2 = nums2.length;
    int len = len1 + len2;
    int p1 = 0, p2 = 0;//设置两个数组指针
    //设置记录值的容器
    int cur = -1, pre = -1;//cur记录中位数
    while (int i = 0; i < len / 2; i++) {//循环 (len / 2) + 1 次
        pre = cur; //每次遍历前 将上一轮的值赋给 pre
        if (p1 < len1 && (p2 >= len2 || nums1[p1] < nums2[p2])) {
            cur = nums1[p1++];
        } else {
            cur = nums2[p2++]; 
        }
    }
    //退出循环时 判断一下数组总长度 
    // 如果是偶数
    if ((len & 1) == 0) {//相当于进行了取余运算
        return (pre + cur) / 2.0;
    }
    return (double) cur;
}
```
