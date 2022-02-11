# 判断一个链表是否为回文结构-Palindrome LinkedList

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
### 利用一个stack来实现
>Time Complexity: $\mathcal{O}(n)$
>Space Complexity: $\mathcal{O}(n)$

将链表以中点部分为界的后半部分逐个压栈，如果遇到中点就停止入栈。
然后从中点位置开始依次弹出栈元素，同时重头开始遍历链表,将链表中的元素与栈元素比较，如果不相同返回`false`否则返回`true`
**注意奇/偶长度的链表情况下**：slow指针停下的位置，以及stack是否应该添加当前位置的值
```java
public boolean ifPalindrome (Node head) {
    if (head == null || head.next == null) {
      return true;
    }
    Deque<Integer> stack = new ArrayDeque<Integer>();
    //先找到中点
    Node midPoint = findMid(head);
    Node cur = head;
    while (cur != midPoint.next) {
      stack.push(cur.val);
      cur = cur.next;
    }
    
    return true;
}
//奇偶条件下 slow指针的位置
// 1->2->2->1->null
// s  s    
// f     f
// 1->2->1->null
// s  s
// f     f
private Node findMid(Node head) {
    if(head == null || head.next) {
      return head;
    }
    Node slow = head;
    Node fast = head;
    //注意这里是且的逻辑 否则会出现NPE
    while (fast.next != null && fast.next.next != null) {
      slow = slow.next;
      fast = fast.next.next;
    }
    
    return slow;
}
```
## 思路二:
### 
>Time Complexity: $\mathcal{O}(n)$
>Space Complexity: $\mathcal{O}(1)$

用快慢指针法找到链表的中点，然后将链表的后半段进行反转，然后cur1从头节点开始遍历，cur2从中点开始遍历，如果cur1指针所指向的值不等于cur2指针的值，那么就返回false，否则返回true，最好在返回时还能够将原链表复原
```java
public Node ifPalindromeList(Node head) {
    if(head == null || head.next == null) {
       return true;
    }
    Node mid = findMid(head);
    Node head2 = reverseList(mid.next);
    //注意要断开 mid 和 mid.next 之间的联系
    mid.next = null;
    Node cur2 = head2;
    Node cur1 = head;
    //就算是奇数个节点也没有问题 
    //因为奇数个节点的时候 其中cur2会走到null
    //而cur1会走到链表中点的位置
    while (cur1 != null && cur2 != null) {
      if (cur1.val != cur2.val) {
        return false;
      }
      cur1 = cur1.next;
      cur2 = cur2.next;
    }
    head2 = reverseList(head2);
    mid.next = head2;
    return true; 
}

private Node findMid(Node head) {
    if(head == null || head.next == null) {
      return head;
    }
    Node slow = head;
    Node fast = head;
    //注意这里是且的逻辑 否则会出现NPE
    while (fast.next != null && fast.next.next != null) {
      slow = slow.next;
      fast = fast.next.next;
    }
    
    return slow;
}

private Node reverseList(Node head) {
    if (head == null || head.next == null) {
      return head;
    }
    Node pre = null;
    Node cur = head;
    Node nxt = head.next;
    // 1 -> 2 -> null
    //     cur  nxt
    while (nxt != null) {
        cur.next = pre;
        pre = cur;
        cur = nxt;
        nxt = nxt.next;
    }
    cur.next = pre;
    return cur;
}

```

