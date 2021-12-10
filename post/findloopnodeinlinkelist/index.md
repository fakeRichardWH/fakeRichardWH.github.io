# leetcode-寻找入环节点

> 题目描述：给定两个可能**存在环或者不存在环**的单链表head1和head2，找到两个链表的第一个相交节点。
# 总体思路：
我们希望有一个函数能调用后，告知我们一个链表是否有环，如果有环还能返回**第一个入环节点**。从而我们通过这个函数**findLoopNode**找到入环节点，接下来就可以分情况讨论两个链表相交的情况。

## 1. 两链表都无环
即`findLoopNode(head1) == findLoopNode(head2) = null`。</br>
1.1 如果两个链表相交的话:他们一定从**某个节点**开始有公共的部分并且公共部分会一直延伸到`null`节点。</br>
1.2 否则二者**一定不相交**，从而我们可以通过两者到链表结尾的**前一个非`null`节点**判断是否相同判断是否相交。
若相交： 这时候让链表长度长的一方先走差值步数`(len(head1) - len(head2))`

然后让链表长度短的与链表长度长的**再次同时出发**，如果相遇就是第一个相交🍌的节点
## 2. 两链表中一个有环，一个无环
即`findLoopNode(head1) == null || findLoopNode(head2) == null`</br>
2.1
这种情况下，无论如何两个链表一定不相交，读者可以在草稿纸上画着试一试，因为题目给定的是单链表，如果相交的话，就说明另一个链表也有环，从而和我们的假设相矛盾。
## 3. 两链表都有环
即`findLoopNode(head1) != null && findLoopNode(head2) != null`
此时分为两种情况：</br>
### 相交
#### Y字形开始相交后进入同一个环 </br>
例子🌰：
```
1->2->4->5->7->8->9
         ^     ^  | 
 6->3____|     |__|
```
这种比较简单，只需要判断入环节点是否相等，如果相等，那么一定在某个位置开始有入环，比如🌰例子中的`5`节点，那么此时只需要类似将入环节点**视为结尾点**（或者当作`null`），按照两个**无环链表相交**的情况来寻找他们的相交点即可。
#### 两链表相交后进入同一个环
例子🌰：
```
1->2->4->5->7->8->9<-3<-6
            ^     | 
            |_____|
```
由于题目说要返回第一个相交节点，可以认为两个链表的其中一个入环节点就是相交节点，将其返回即可，此时要与不相交的情况分开就只有用
### 不相交
例子🌰：
```
1->2->4->5->7->8->9
            ^     | 
            |_____|

3->0->6
   ^  | 
   |__|
```
### 主逻辑实现
```java
public Node findIntersectNode(Node head1, Node head2) {
    if (head1 == null || head2 == null) {
        return null;
    }
    Node res1 = findLoopNode(head1); 
    Node res2 = findLoopNode(head2);
    if (res1 == null && res2 == null) {
        //两链表都无环
        return intersectPointYStyle(head1, head2, null);
    } else if (res1 == null || res2 == null) {
        //其中一个链表有环，另一个无环
        return null;
    } else {
        if (res1 == res2) {
            return intersectPointYStyle(head1, head2, res1);
        } else {//剩下两个情况 1:都有环不相交 2:都有环但是是非Y型相交
            res1 = res1.next;
            while (res1 != res1) {
                //让res1在环上绕圈 如果碰到 res2
                //说明两者相交
                if (res1 == res2) {
                    return res1;
                }
                res1 = res1.next;
            }
            //绕圈中始终没碰到 res2
            //说明二者不相交
            return null;
        }
    }
}
//这个函数是找Y型相交链表的第一个相交点
public Node intersectPointYStyle(Node head1, Node head2, Node end) {
    Node cur1 = head1;
    Node cur2 = head2;
    int n = 0;//记录两链表的差值
    while (cur1 != end) {
        n++;
        cur1 = cur1.next;
    }
    while (cur2 != end) {
        n--;
        cur2 = cur2.next;
    }
    //找到先出发的节点
    Node first = n > 0 ? head1 : head2;
    Node second = n > 0 ? head2 : head1;
    n = Math.abs(n);

    //让长链表先走差值步
    while (n > 0) {
        first = first.next;
        n--;
    }
    //让两个链表同时走
    while (first != second) {
        first = first.next;
        second = second.next;
    }
    return first;
}
```
### findLoopNode()实现：
如果一个单链表有环的话，那么从头节点出发，一定会在某个时刻走到原来的位置，反之就一定会走到null的位置。<br>
因而可以使用快慢指针，如果`slow`或者`fast`指针走到了`null`节点，那么该链表一定不存在环。反之就会出现`slow==fast`的情况。
#### 入环节点判断
设两指针`fast`，`slow `指向链表头部 `head`，`fast`每轮走2步，`slow`每轮走1步；当`fast == slow`时，两指针在环中第一次相遇。<br>
**下面分析此时`fast` 与`slow`走过的步数关系：**
<br>
> 设链表共有$a+b$个节点，其中链表头部到链表入口有$a$个节点（不计链表入口节点），链表环有b个节点。
> 这里需要注意: $a$和$b$是未知数，例如下面例子中链表 $a=4$, $b=2$。

例子:
```
1->2->4->5->7->8->9
            ^     | 
            |_____|
```

0. 相遇时: 设两指针分别走了 f ，s 步，则有：`fast`走的步数是`slow`步数的$2$倍，即 $f = 2s$

1. 相遇时:`fast`比`slow`多走了$n$个环的长度，即:$f = s + nb$;（解析: 双指针都走过 a步，然后在环内绕圈直到重合，重合时`fast`比 `slow`多走了环的长度整数倍；
2. 以上两式相减得: $f = 2nb$,$s = nb$，即`fast`和`slow`指针分别走了 $2n$，$n$个环的周长（注意：$n$是未知数，不同链表的情况不同）。

##### 目前情况分析：

如果让指针从链表头部一直向前走并统计步数$k$ ，那么所有走到链表入口节点时的步数是：$k = a + nb$ (即：先走$a$步到入口节点，之后每绕$1$圈环(走$b$步) 都会再次到入口节点)<br>
而目前`slow`指针走过的步数为$nb$步。因此，我们只要想办法让 `slow `再走 $a$步停下来，就可以到环的入口。<br>
但是我们不知道$a$的值，该怎么办？依然是使用双指针法。我们**构建一个指针**，此指针需要有以下性质：此指针和`slow `一起向前走$a$步后，两者在入口节点重合。
那么从哪里走到入口节点需要$a$步？答案是链表头部head。<br>
<br>
##### 双指针第二次相遇:
`slow`指针位置不变 ，将` fast` 指针重新指向链表头部节点;`slow`和` fast` 同时每轮向前走1步；

TIPS：此时 $f = 0$，$s = nb$；

当` fast` 指针走到$f = a$步时,`slow`指针走到步$s = a+nb$，此时两指针重合，并同时指向链表环入口。
第二次相遇后，返回`slow`指针指向的节点即可。

### 实现：（使用快慢指针）
```java
public findLoopNode(Node head) {
    if (head == null || head.next == null || head.next.next == null) {
        //由于我们希望快指针一开始就运动到比slow的下个位置
        return null;
    }
    Node slow = head.next;
    Node fast = head.next.next;
    while (slow != fast) {
        if (fast.next == null || fast.next.next == null) {//一旦快指针走到终点，说明无环
            return null;
        }
        slow = slow.next;
        fast = fast.next.next;
    }
    //相遇了,将fast指向slow
    fast = head;
    while (fast != slow) {
        fast = fast.next;
        slow = slow.next;
    }
    return slow;
}
```


