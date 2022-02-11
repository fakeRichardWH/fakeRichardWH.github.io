# Copy Linkedlist with Random pointer

>给你一个长度为 n 的链表，每个节点包含一个额外增加的随机指针 random ，该指针可以指向链表中的任何节点或空节点。构造这个链表的 **深拷贝**。 **深拷贝**应该正好由 n 个**全新**节点组成，其中每个新节点的值都设为其对应的原节点的值。新节点的 next 指针和 random 指针也都应指向复制链表中的新节点，并使原链表和复制链表中的这些指针能够表示相同的链表状态。复制链表中的指针都不应指向原链表中的节点。

>例如，如果原链表中有 **X** 和 **Y** 两个节点，其中 X.random --> Y。那么在复制链表中对应的两个节点 **x** 和 **y** ，同样有 x.random --> y 。
>返回复制链表的头节点。

>用一个由 n 个节点组成的链表来表示输入/输出中的链表。每个节点用一个 [val, random_index] 表示：
val：一个表示 Node.val 的整数。
random_index：随机指针指向的节点索引（范围从 0 到 n-1）；如果不指向任何节点，则为  null 。你的代码**只接受**原链表的头节点**head**作为传入参数。

链表节点定义如下：
```java
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
```

>来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/copy-list-with-random-pointer
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

# 思路1:
 1. 生成clone节点`copyNode`，用`HashMap`来存储`<原始Node,拷贝Node>`这样的键值对，存储起来
 2. 再去设置`copyNode`的`random`和`next`指针。
 3. 利用这个键值对的性质，每个`拷贝Node`的`random`指针需要指向的位置：就是`原始Node`的`random`指针指向那个**Node的拷贝**，而要找到这个值，就是利用`hashmap.get(Node.random)`
   比如：`1 -> 2`,`1' -> 2'` 由于`(1，1‘)`构成了键值对，`(2，2’)`也构成了键值对，所以`1‘`的random需要指向`2`的拷贝`2’`

```java
public class Solution1 {
    HashMap<Node, Node> map = new HashMap<>();
    /**
    ** 思路1
    */
    public Node copyRandomList1(Node head) {
        Node cur = head;
        //利用 key 存放原链表节点,val 存放copyNode
        while (cur != null) {
            Node copyCur = new Node(cur.val);
            map.put(cur,copyCur);
            cur = cur.next;
        }
        cur = head;
        //对应设置好 next 和 random指针
        while (cur != null) {
            Node copyCur = map.get(cur);
            //能这样设置的原因，就是提前将 (cur, copyCur)放入，
            //而每个cur能映射到它对应的copyCur
            copyCur.next = map.get(cur.next);
            copyCur.random = map.get(cur.random);
            cur = cur.next;
        }
        return map.get(head);
    }

}
```
# 思路2:
 1. 对应每个原始链表的当前节点`Cur`生成`Copy`节点，将`Copy`节点放在原始链表的当前节点`Cur`的下一个位置`nxt`
 2. 再次遍历这个由**新老节点**构成的链表，设置`Copy`节点的`random`指针，它对应的`random`指向了`Cur`的`random`所指向的节点的下一个节点
 3. 最后我们只需要将原始链表从新构成的链表分离出来就可以了，返回的答案也要是新的拷贝链表的头节点
 >举个例子来说一个新构成的链表如下：
>1 -> 1' -> 2 -> 2'
>
假定原始链表中：`2`的`random`指向了`1`，那么在设置`2‘`的`random`时只需要将其指向1的`next`就可以了

```java
    //更省内存的方法
    /**
    ** 思路2
    */
public class Solution2 { 
    public Node copyRandomList2(Node head) {
        if (head == null) {
            return null;
        }
        Node cur = head;
        Node nxt = null;//next指向原始链表中当前节点cur的下一个节点
        Node copyCur = null;
        while (cur != null) {
            // 1 -> 2
            // cur nxt
            nxt = cur.next;
            // 1‘       1 -> 2
            // copyCur  cur  nxt
            copyCur = new Node(cur.val);
            // 1 -> 1‘        2
            // cur copyCur   nxt
            cur.next = copyCur;
            //  1 -> 1‘   ->  2
            // cur copyCur   nxt
            copyCur.next = nxt;
            // 1 -> 1' -> 2
            //           cur
            cur = nxt;
        }
        //reset 回起点
        cur = head;
        // 设置rand指针
        while (cur != null) {
            // 1 -> 1' -> 2 -> 2'
            // cur       nxt
            copyCur = cur.next;
            nxt = cur.next.next;
            //一定要判断 cur.rand是否为null
            copyCur.random = cur.random != null ? cur.random.next : null;
            //移动copyCur和cur
            // 1 -> 1' -> 2 -> 2'
            //     cur        copyCur
            cur = nxt;
        }
        //split 断开两个链表的连接
        //先记下最终结果的头节点
        Node res = head.next;
        cur = head;
        while (cur != null) {
            copyCur = cur.next;
            nxt = cur.next.next;
            // 1   ->   1'   ->   2  ->  2'
            // cur   copyCur     nxt
            // 1' -> 2'
            // 1 -> 2
            copyCur.next = nxt != null ? nxt.next : null;
            cur.next = nxt;
            cur = nxt;
        }
        return res;
    }
}
```
