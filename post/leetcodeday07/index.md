# Leetcode-广度优先搜索模版


## 无向图中连通分量的数目
给定编号从 0 到 n-1 的 n 个节点和一个无向边列表（每条边都是一对节点），请编写一个函数来计算无向图中连通分量的数目。

### 创建Array of Lists的技巧
```java
List<Integer>[] lists=new List[10];

//insertion
for(int i = 0; i < 10; i++){
    lists[i]=new ArrayList<Integer>();
    lists[i].add(i);
    lists[i].add(i+1);
}

//printing
for(List<Integer> list:lists){
    System.out.println(list.size());
}
```
可见这种创建方式: `lists`这个变量指向了一个数组,而数组的类型是`List<Integer>`,如果直接创建`List<List<Integer>>`才是最常见的创建方式
```java
List<List<Integer>> lists = new ArrayList<>();
for (int i = 0;i < 10; i++) {
    lists.add(new ArrayList<>());
}
```
但这种方式创建的Array of Lists无需提供实际的List的类型,事实上你甚至可以这样写:
```java
List[] lists=new List[10];
```
因为从JVM的角度,给一个`Object`类型的`List`和`Integer`类型的`List`都需要相同的字节数,也就是说这个最终创建出来的数组大小空间会是一致的.唯一的区别(限制)在于这个`Object`类型的List的值没有限制,而`Integer`类型的`List`必须是`Integer`

## 输入实例
```bash

输入: n = 5 和 edges = [[0, 1], [1, 2], [3, 4]]

     0          3
     |          |
     1 --- 2    4 

输出: 2
```

```java
import java.util.*;
public class Solution {
    public int countComponents(int n, int[][] edges) {
        // 第 1 步：构建图 (邻接表)
        List<Integer>[] adj = new ArrayList[n];
        for (int i = 0; i < n; i++) {
            adj[i] = new ArrayList<>();
        }
        // 无向图，所以需要添加双向引用
        // 对于每个边的两个端点元素 相互添加 到各自的邻居表中
        for (int[] edge : edges) {
            adj[edge[0]].add(edge[1]);
            adj[edge[1]].add(edge[0]);
        }

        // 第 2 步：开始广度优先遍历
        int res = 0;
        // 构建一个 是否访问的数组 防止重复对该位置的邻居重复访问
        boolean[] visited = new boolean[n];
        // 对这n个点的邻居进行访问
        for (int i = 0; i < n; i++) {
            //如果某个节点已经访问过(在前面的某一轮由于连接,导致被记为访问过)
            if (!visited[i]) {
                bfs(adj, i, visited);
                res++;
            }
        }
        return res;
    }

    /**
     * @param adj     邻接表
     * @param u       从 u 这个顶点开始广度优先遍历
     * @param visited 全局使用的 visited 布尔数组
     */
    //bfs的逻辑是站在当前这个节点上,分辨出与其相连的节点,将这些点标记为已经访问过,后续不会再访问
    private void bfs(List<Integer>[] adj, int u, boolean[] visited) {
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(u);
        visited[u] = true;

        while (!queue.isEmpty()) {
            Integer front = queue.poll();
            // 获得队首结点的所有后继结点
            List<Integer> successors = adj[front];
            for (int successor : successors) {
                if (!visited[successor]) {
                    queue.offer(successor);
                    // 特别注意：在加入队列以后一定要将该结点标记为访问，否则会出现结果重复入队的情况
                    visited[successor] = true;
                }
            }
        }
    }

```
复杂度分析：

时间复杂度：$O(V + E)$，这里 $E$是边的条数，即数组 $edges$ 的长度，初始化的时候遍历数组得到邻接表。这里 V 为输入整数 $n$，遍历的过程是每一个结点执行一次深度优先遍历，时间复杂度为 $O(V)$；
空间复杂度：$O(V + E)$，综合考虑邻接表 $O(V+E)$、$visited$ 数组 $O(V)$、队列的长度 $O(V)$三者得到。



