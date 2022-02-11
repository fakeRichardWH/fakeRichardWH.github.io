# BinarySearch

# 二分查找绕口令
>管他左侧还右侧，搜索区间定乾坤<br>
>搜索一个元素时，搜索区间两端闭<br>
>while条件带等号，否则需要打补丁<br>
>if相等就返回，其他的事甭操心<br>
>mid必须加减一，因为区间两端闭<br>
>while条件结束了，惨惨戚戚返-1；<br>
>
>搜索左右边界时，搜索区间要阐明<br>
>左闭右开最常见，其余逻辑便自明<br>
>while要用小于号，这样才能不漏掉<br>
>if相等别返回，利用mid锁边界<br>
>mid加一或减一？要看区间开活闭<br>
>while结束不算完，因为函数没返回<br>
>索引可能出边界，if检查保安全<br>
>左闭右开很常见，常见不一定合理<br>
>搜索区间心中记，或开或闭本无异<br>
>二分搜索三变体，逻辑统一记忆起

# 二分搜索框架
```java
int binarySearch (int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;//也不一定是这个 看清题目要求
    while (...) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] > target) {
            right = ...;
        } else if (nums[mid] < target) {
            left = ...;
        }
    }
    return ...;
}
```
二分查找的代码一个极其重要的技巧就是：**不要出现else，而是将所有情况用else if写清楚**，清楚的展现出所有细节。
下面共有三种类型题：
# 寻找一个数（基本的二分搜索）
这个场景是最简单的，肯能也是大家最熟悉的，即搜索一个数，如果存在，返回其索引，否则返回 -1
```java
int binarySearch(int[] nums, int target) {
    int left = 0; 
    int right = nums.length - 1; // 注意

    while(left <= right) {
        int mid = left + (right - left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; // 注意
        else if (nums[mid] > target)
            right = mid - 1; // 注意
    }
    return -1;//如果是搜索插入位置 可以改为return left
}
```
1. 为什么while 循环中使用<= 而非 < ?

答：
因为初始化`right`的值是`nums.length - 1`，即最后一个元素的索引，而不是`nums.length`。
`<=`或者`<`可能出现在**不同功能**的二分查找中，区别是：前者相当于两端都闭区间`[left, right]`，后者相当于左闭右开区间`[left, right)`，因为索引大小为`nums.length`是越界的。
这个算法中使用的是前者`[left, right]`两端都闭的区间。这个区间其实就是每次进行搜索的区间。
什么时候应该停止搜索呢？当然，找到了目标值的时候可以终止：
```java
if(nums[mid] == target) {
        return mid;
}     
```
如果上述条件没有找到对应的target，则`while`循环会终止，返回`-1`。当while循环终止的时候，搜索区间为空：`left == right + 1`,写成区间的形式是：`[right + 1, right]`，例如`[3,2]`。
而如果循环条件是：`while(left < right)` 可见此时终止条件为：`left == right`,搜索区间为`[left, right]`,例如`[2,2]`。此时区间含有2这个索引，但是`while`循环已经终止了,所以该索引不会被搜索到，因此直接返回`-1`是错误的
如果一定要使用`while(left < right)`那么就要做后处理
```java
while(left < right) {
    // ...
    ...
}
return nums[left] == target ? left : -1;
```
2. 为什么`left = mid + 1`,`right = mid -1`?

因为本算法的搜索区间两端都是闭的，即`[left, right]`,当索引`mid`不是target，下一步的搜索要么就是`[mid + 1, right]`或者`[left, mid - 1]`,因为`mid`已经被搜索过，所以应该去除
3. 算法的局限性

比如说给一个有序数组`nums = [1,2,2,2,3], target = 2`,那么用上面的函数返回的索引是`2`，如果想要获取与`target`相同的最左侧边界，即索引`1`,那么就需要后处理，向左不断线性搜索，那么算法的复杂度可能就从$\mathcal{O}(\log(n))$变为$\mathcal{O}(n)$级别的

# 寻找左侧边界的二分搜索
## 左边界-原始代码
```java
int findLeftBound(int[] nums, int target) {
    if (nums == null || nums.length == 0) return -1;
    int left = 0;
    int right = nums.length; //注意这是 nums.length

    while (left < right) {//注意是小于号
        int mid = (left + right) / 2;
        if (nums[mid] == target) {//调整右边界
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid; // 注意
        }
    }
    return left;//注意这个返回值需要改变
}
```
1. 为什么 while 中是 < 而不是 <=?

答：用相同的方法分析，因为`right = nums.length` 而不是 `nums.length - 1`。因此每次循环的「搜索区间」是 `[left, right) `左闭右开。

`while(left < right) `终止的条件是 `left == right`，此时搜索区间 `[left, left) `为空，所以是一个正确的终止。

PS：这里先要说一个搜索左右边界和上面这个算法的一个区别，也是很多读者问的：刚才的`right` 不是`nums.length - 1`吗，为啥这里非要写成 `nums.length`使得「搜索区间」变成左闭右开呢？

因为对于搜索左右侧边界的二分查找，这种写法比较普遍，我就拿这种写法举例了，保证你以后遇到这类代码可以理解。你非要用两端都闭的写法反而更简单，我会在后面写相关的代码，把三种二分搜索都用一种两端都闭的写法统一起来，你耐心往后看就行了。

2. 为什么没有返回`-1`的操作？如果`nums`中不存在`target`这个值，怎么办？

![alt binarySearch](/images/postImgs/findLeftBoundary.png)

对于这个数组，算法会返回`1`,而这个`1`可以解读为`nums`中中小于`2`的元素有`1`个。

比如对于有序数组`nums = [2,3,5,7], target = 1`，算法会返回`0`，含义是：`nums`中小于`1`的元素有`0`个。

再比如说 `nums = [2,3,5,7], target = 8`，算法会返回`4`，含义是：nums 中小于`8`的元素有`4`个。综上可以看出，函数的返回值（即`left`变量的值）取值区间是闭区间 `[0, nums.length]`，所以我们简单添加两行代码就能在正确的时候 `return -1`
```java
while (left < right) {
    //...
}
if (nums[left] < target) {
    return -1;
} //target大于所有数组中的数
/** 上面这个if语句的等价写法
* if (left == nums.length) return -1;
*/
return nums[left] == target ? left : -1;
```
1. 为什么`left = mid + 1，right = mid`？和之前的算法不一样？

答：这个很好解释，因为我们的「搜索区间」是`[left, right)`左闭右开，所以当`nums[mid]`被检测之后。
下一步的搜索区间应该去掉 `mid` 分割成两个区间，即 `[left, mid)` 或 `[mid + 1, right)`。
4. 为什么该算法能够搜索左侧边界？

答：关键在于对于 `nums[mid] == target` 这种情况的处理：
```java
if (nums[mid] == target)
        right = mid;
```
当我们找到target时，不是立刻返回，而是不断缩小「搜索区间」的上界 `right`，在区间 `[left, mid)` 中继续搜索，即不断向左收缩，达到锁定左侧边界的目的。
5、为什么返回 left 而不是 right？

答：都是一样的，因为 while 终止的条件是 left == right。

6. 能不能想办法把 `right` 变成 `nums.length - 1`，也就是继续使用两边都闭的「搜索区间」？这样就可以和第一种二分搜索在某种程度上统一起来了。

答：当然可以，只要你明白了「搜索区间」这个概念，就能有效避免漏掉元素，随便你怎么改都行。下面我们严格根据逻辑来修改：

因为你非要让搜索区间两端都闭，所以 `right` 应该初始化为 `nums.length - 1`，`while` 的终止条件应该是 `left == right + 1`，也就是其中应该用 `<=`：
```java
int findLeftBound(int[] nums, int target) {
    // 搜索区间为 [left, right]
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        // if else ...
    }
```
因为搜索区间是两端都闭的，且现在是搜索左侧边界，所以 `left `和 `right `的更新逻辑如下：
```java
if (nums[mid] < target) {
    // 搜索区间变为 [mid+1, right]
    left = mid + 1;
} else if (nums[mid] > target) {
    // 搜索区间变为 [left, mid-1]
    right = mid - 1;
} else if (nums[mid] == target) {
    // 收缩右侧边界
    right = mid - 1;
}

```
由于 `while` 的退出条件是 `left == right + 1`，所以当 `target` 比 `nums` 中所有元素都大时，会存在以下情况使得索引越界：
![img](/images/postImgs/outOfBound.png)

因此，最后返回结果时，代码需要检查**越界情况**：
```java
if (left >= nums.length || nums[left] != target) {
    return -1;
}
return left;
```
## 左边界 最终代码
所以最终的代码完整如下：
```java
int findLeftBound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    // 搜索区间为 [left, right]
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            // 搜索区间变为 [mid+1, right]
            left = mid + 1;
        } else if (nums[mid] > target) {
            // 搜索区间变为 [left, mid-1]
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 收缩右侧边界
            right = mid - 1;
        }
    }
    // 检查出界情况
    if (left >= nums.length || nums[left] != target)
        return -1;
    return left;
}
```
这样就和第一种二分搜索算法统一了，都是两端都闭的「搜索区间」，而且最后返回的也是 `left` 变量的值。只要把住二分搜索的逻辑，两种形式大家看自己喜欢哪种记哪种吧。

# 寻找右侧边界
类似寻找左侧边界的算法，这里也会提供两种写法，还是先写常见的左闭右开的写法，只有两处和搜索左侧边界不同，已标注：
```java
int right_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0, right = nums.length;
    
    while (left < right) {
        int mid = (left + right) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // 注意
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    return left - 1; // 注意
}
```
1. 为什么能找到右侧边界？
```java
if (nums[mid] == target) {
            left = mid + 1; // 注意这里
```
当`nums[mid] == target`时,不是立刻返回`mid`,而是而是增大「搜索区间」的下界 `left`，使得区间不断向右收缩，达到锁定右侧边界的目的。

2. 为什么最后返回 `left - 1` 而不像左侧边界的函数，返回 `left`？而且我觉得这里既然是搜索右侧边界，应该返回 `right` 才对。

答：`while` 循环的终止条件是 `left == right`，所以` left` 和 `right` 是一样的，你非要体现右侧的特点，返回` right - 1 `好了。
至于为什么要减一，这是搜索右侧边界的一个特殊点，关键在这个判断条件：
```java
if (nums[mid] == target) {
            left = mid + 1; // 注意这里
```
这之后，`left`被更新为`left = mid + 1`，就是说` while `循环结束时，`nums[left]` 一定不等于 `target` 了，而 `nums[left-1]` 可能是 `target`。见下图：

![rightBound](/images/postImgs/rightBound.png)

3. 为什么没有返回 `-1` 的操作？如果 `nums `中不存在 `target `这个值，怎么办？
答：类似之前的左侧边界搜索，因为 `while` 的终止条件是 `left == right`，就是说 `left` 的取值范围是 `[0, nums.length]`，所以可以添加两行代码，正确地返回 `-1`:
```java
while (left < right) {
    // ...
}
if (left == 0) return -1;
return nums[left - 1] == target ? (left - 1) : -1;
```

4. 是否也可以把这个算法的「搜索区间」也统一成两端都闭的形式呢？这样这三个写法就完全统一了，以后就可以闭着眼睛写出来了。
```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 这里改成收缩左侧边界即可
            left = mid + 1;
        }
    }
    // 这里改为检查 right 越界的情况，见下图
    if (right < 0 || nums[right] != target)
        return -1;
    return right;
}
```
当`target`比所有的元素都小时，`right`会减小到`-1`,如下图：
![outofleftBound](/images/postImgs/outOfBound.png)

# 总结
来梳理一下这些细节差异的因果逻辑：

第一个，最基本的二分查找算法：
>因为我们初始化 `right = nums.length - 1`
所以决定了我们的「搜索区间」是 `[left, right]`
所以决定了 `while (left <= right)`
同时也决定了更新方式是: `left = mid+1` 和 `right = mid-1`
因为我们只需找到一个 `target` 的索引即可
所以当 `nums[mid] == target` 时可以立即返回

第二个，寻找左侧边界的二分查找：

>因为我们初始化 `right = nums.length`
所以决定了我们的「搜索区间」是 `[left, right)`
所以决定了` while (left < right)`
同时也决定了 `left = mid + 1 `和 `right = mid`
因为我们需找到 `target` 的最左侧索引
所以当 `nums[mid] == target` 时不要立即返回
而要收紧右侧边界以锁定左侧边界

第三个，寻找右侧边界的二分查找：


>因为我们初始化 `right = nums.length`
所以决定了我们的「搜索区间」是 `[left, right)`
所以决定了 `while (left < right)`
同时也决定了 `left = mid + 1` 和 `right = mid`
因为我们需找到 `target `的最右侧索引
所以当 `nums[mid] == target` 时不要立即返回
而要收紧左侧边界以锁定右侧边界
又因为收紧左侧边界时必须 `left = mid + 1`
所以最后无论返回 `left `还是 `right`，必须减一

