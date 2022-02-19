# leetcode-两数之和twoSum

# 两数之和
## 思路1:
思路一: 对于`nums`数组中每个位置`i`上的`x`，要找到`target-x`,可以使用两层遍历，第一层遍历是遍历数组中的每个数字，第二层遍历是从该位置的后一个位置`j = i + 1`开始查看是否符合`target - x`
**时间复杂度:** $o(n^2)$
**空间复杂度:** $o(1)$
```java
public class Solution {
    public static void main(String[] args) {
        int[] nums = new int[]{1,3,2,4};
        int target = 5;
        int[] result = find2Sum(nums, target);
    }
    public static int[] find2Sum(int[] nums, int target) {
        if (nums == null || nums.length <= 1) {
            return null;
        }
        for (int i = 0; i < nums.length; i++) {
            rest = target - nums[i];
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[j] == rest) {
                    return new int[]{i, j};
                }
            }
        }
        return null;
    }
}
```
## 思路2:
思路二:利用`HashMap`查询`key`只要`o(1)`时间的特性.我们将每个位置的数字`nums[i]`和其对应的索引`i`存入`HashMap`中，每次遍历过程遇到一个数字将`(nums[i], i)`键值对存储之前，先查询这个数字对应的`target - nums[i]`是否在`HashMap`中已经存储，
1. 如果没有，那么就存入这个数字，遍历下一个数字
2. 如果存在，说明当前数字和能找到对应的数字构成`target`
**时间复杂度:** $o(n)$
**空间复杂度:** $o(n)$
### 注意：
1. HashMap创建时需要用到泛型
2. HashMap提供了 `boolean containsKey(Integer key)` 和 `Integer get(Integer key)`方法

```java
import java.util.HashMap;
public class Solution {
    public static void main(String[] args) {
        int[] nums = new int[]{1,3,2,4};
        int target = 5;
        int[] result = find2Sum(nums, target);
    }
    public static int[] find2Sum(int[] nums, int target) {
        int[] result = new int[2];
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{i, map.get(target - nums[i])}; 
            } else {
                map.put(nums[i], i);
            }
        }
        return null;
}
```

