# Leetcode-广度优先搜索模版

# 广度优先搜索模版
## 核心思路
BFS核心思想就是**把问题抽象为一个图**，从一个节点开始，向四周扩散。一般来说，BFS都会使用到一个`队列`，这样就可以将一个节点周围的所有节点加入队列。
BFS相较于DFS（回溯算法）,但代价就是空间复杂度要比DFS高。
## 算法框架
问题的本质就是在一个**图**中，从**起点**`start`找到**终点**`target`的最短距离，BFS的本质就是干这个事情。这个问题的描述可以有各种变体：
1. 走迷宫、岛屿数量。这类问题会添加限定条件比如某些位置不能走。 
2. 有两个单词，给出一些固定/非固定的替换，将其中一个变为另一个，每次能替换一个字符，最少要替换几次？
3. 连连看，点击两个坐标，如何判断两者中的最短连线有几个拐点？
```java
// 计算起点 start 到终点 target 的最近距离
int bfs(Node start, Node target) {
    Deque<Node> q; //核心数据结构：队列
    Set<Node> visited; // 避免重复访问 走回头路
    // 将起点加入 初始队列
    q.offer(start);
    visited.add(start);
    int step = 0; // 记录当前扩散到哪一步？
    while (!q.isEmpty() ) { //队列不为空
        int size = q.size(); //记录当前队列扩散了多少个节点
        /* 将当前队列中的所有节点向四周扩散 */
        // 当前层 加入 queue
        for（int i = 0; i < size; i++) {
            Node cur = q.poll();
            if (cur is target) {
                /* 判断是否是终点 */
                return step;// 最短路径生成
            }
            /* 当前节点的相邻节点加入队列中*/
            for (Node x : cur.adjacent()) {
                q.offer(x);
                visited.add(x);
            }
        }
        //当前层遍历完
        step++;//对最短路径长度更新
    }
    // 队列为空，广度搜索结束
    
}
```
队列`q`就不说了，BFS的核心数据结构；`cur.adj()` 泛指 `cur` 相邻的节点，比如说**二维数组**中，`cur` **上下左右**四面的位置就是相邻节点；`visited` 的主要作用是防止走回头路，大部分时候都是必须的，**但是像一般的二叉树结构**，没有子节点到父节点的指针，**不会走回头路就不需要** `visited`。
至于为啥这样就一定是最短的路径呢，可以用反证法的思维，假设扩散到某一层的target位置已经是最短路径了，那么前面的层一定不包含target，否则就会终止扩散。
## 二叉树的最小高度
> 给定一个二叉树，找出其最小的深度，最小深度就是指从根节点到最近的叶子节点的节点数量。
> 叶子节点就是左右子节点都为`null`

### 思考的逻辑：
 如何套用到BFS呢？ 

 1. 明确起点 **`start`** 及终点 **`target`** 分别是什么，并且在什么条件下到达了终点
 2. 队列存储的是一系列待扩散的节点，利用for循环遍历队列中的节点，并且判断是否达到了目标节点
那在这里，起点就是root，终点就是叶节点，怎么判断是否到了叶节点？
`if (cur.left == null && cur.right == null)`
```java
int minHeight(TreeNode root) {
    Linkedlist<TreeNode> queue = new LinkedList<>();
    //HashSet<TreeNode> visited = new HashSet<>(); 不再是必要的了 因为二叉树不会重复访问
    if (root == null) {
        return 0;
    }
    queue.offer(root);
    int minHeight = 1;
    while (!queue.isEmpty()) {
        int size = queue.size();
        for (int i = 0; i < size; i++) {
            TreeNode cur = queue.poll();
            if (cur.left == null && cur.right == null) {
                return minHeight;
            }
            // 否则就再将节点的左子树节点加入到队列中
            if (cur.left != null) {
                queue.offer(cur.left);
            }
            if (cur.left != null) {
                queue.offer(cur.right);
            }
        }
        minHeight++;
    }    
}

```
## 岛屿数量
>给定一个由 '1'（陆地）和 '0'（水）组成的的二维网格，计算岛屿的数量。
一个岛被水包围，并且它是通过水平方向或垂直方向上相邻的陆地连接而成的。
你可以假设网格的四个边均被水包围。

>示例 1:
输入:
11110
11010
11000
00000
输出: 1 (总共有1个岛屿)

> 示例 2:
输入:
11000
11000
00100
00011
输出: 3

### 大体思路1: 深度优先遍历：
1.初始状态所有顶点都没被访问，从每一个顶点`v`出发，先访问该顶点`v`
2.依次从 `v` 的各个 未被访问`visited[i][j] == false`的临接顶点出发，以相同的逻辑遍历图，直到图中所有和`v`相通的顶点都被访问到
3.遍历完后，还有其他的点没有被访问到，则另选一个未被访问的顶点作为起始点
4.重复上述过程，直至所有点被访问
#### 本题思路，上下左右相连为`1`的才被认为是连续岛屿
 DFS方式：
 1. 设定目前指向一个岛屿中的某个点`(i, j)`,寻找包含此点的边界
    1.1 从 `(i, j)`向此点的上下左右 `(i + 1, j)`，`(i - 1, j)`，`(i, j + 1)`，`(i, j - 1)`做深度搜索
    1.2 终止条件：
        1.2.1 `(i, j)`越过了矩阵的边界
        1.2.2 grid[i][j] == 0，代表此时分支已经越过了岛屿边界
    1.3 搜索岛屿的同时,执行grid[i][j] = 0,代表该位置已经搜索过了

### 代码实现
```java
package leetcode;
import java.util.*;
public class Main {
    static int[][] grid;
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        List<String> list = new ArrayList<>();
        while(sc.hasNextLine()) {
            list.add(sc.nextLine());
        }
        rows = list.size();
        cols = list.get(0).length();
        grid = new int[rows][cols];
        for (int i = 0; i < rows; i++) {
            for(int j = 0; j < cols; j++) {
                grid[i][j] = list.get(i).charAt(j);
            }
        }
        System.out.println(Arrays.deeptoString(countIsland(grid)));
    }

    public int countIsland(int[][] grid) {
        int count = 0;
        int m = grid.length;
        int n = grid[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {//出现岛屿
                    dfs(grid, i, j);
                    count++;
                }
            }
        }
        return count;
    }
    public void dfs(int[][] grid, int i, int j) {
        if (i < 0 || j < 0 || i >= grid.length || j >= grid[0].length || grid[i][j] == 0) return;
        grid[i][j] == 0;
        dfs(grid, i + 1, j);
        dfs(grid, i, j + 1);
        dfs(grid, i - 1, j);
        dfs(grid, i, j - 1);
    }
}

```
## 机器人可运动的范围
>地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。<br>
>一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？
<br>

>来源：力扣（LeetCode）


