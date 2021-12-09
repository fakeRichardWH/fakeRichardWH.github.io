# leetcode-判断一个链表是否为回文结构

# Q1: 判断一个链表
是否属于回文结构
```java
// example1
  input: 1 -> 2 -> 23 -> 23 -> 2 -> 1
  output: true
// example2
  input: 1 -> 2 -> 23 -> 2 -> 1
  output: true 
// example3
  input: 1 -> 2 -> 23 -> 3 -> 1
  output: false 
```

## 思路一:
利用一个stack来实现
Time Complexity: $\mathcal{O}(n)$
Space Complexity: $\mathcal{O}(n)$

