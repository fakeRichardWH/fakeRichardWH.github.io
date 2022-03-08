# Leetcode-二叉树的遍历

# 用递归及非递归方式实现二叉树的先/中/后序遍历
## 递归实现
递归过程,就是会访问同一个节点三次,而先序/中序/后序取决于打印的时机
### 节点数据结构
```java
public class Node {
    //这是Node的结构
    public int value;
    public Node left;
    public Node right;
    public Node(int value) {
        this.value = value;
    }
}
```
可以看出:树本身就是递归定义的,二叉树的左子树也是同一个数据结构,右子树也是同一个数据结构
### 先序遍历
```java
public void preOrderRecur(Node root) {
    // base case
    if (root == null) {
        return;
    }
    System.out.print(root.value + " ");
    preOrderRecur(root.left);
    preOrderRecur(root.right);
}
```
### 中序遍历
```java
public void inOrderRecur(Node root) {
    // base case
    if (root == null) {
        return;
    }
    preOrderRecur(root.left);
    System.out.print(root.value + " ");
    preOrderRecur(root.right);
}
```
### 后序遍历
```java
public void postOrderRecur(Node root) {
    // base case
    if (root == null) {
        return;
    }
    preOrderRecur(root.left);
    preOrderRecur(root.right);
    System.out.print(root.value + " ");
}
```
## 非递归实现
递归实现起来很简单,但是递归调用会耗费很多内存(栈空间),同时也更加耗时;递归调用无非就是将函数帧入栈和出栈的操作**用一个普通的stack**实现,只是需要将栈中保存的内容变为树节点
需要明确两件事:
1. 什么情况下入栈
2. 什么情况下出栈
### 递归的非递归实现
仔细观察递归调用的过程，我们会发现这样的规律：

1. 不管三七二十一先把**从根节点开始的所有左子树节点**放入栈中
2. 查看栈顶元素，如果栈顶元素**有右子树那么右子树入栈**并以右子树为新的根节点**重复过程1**直到栈空为止

那么我们就可以思考什么时候入栈/出栈了:
1. 什么时候入栈?
1.1 最开始时,将根节点开始的所有左子树节点放入栈内.
1.2 如果栈顶元素有右子树,那么就对其右子树重复步骤1
2. 什么时候出栈?
2.1 查看栈顶元素时,实际上可以将栈顶元素pop掉,是因为查看栈顶元素的同时我们能够确认:**当前节点的左子树一定已经处理完毕了**
2.2 因此对于栈顶元素而言,需要的仅仅是拿到其右子树信息,再拿到右子树后该节点可以完全放心的pop掉
## 关键
明确了各个节点压栈,打印,弹栈的时机!

用代码来表示:
```java
public void nonRecur(Node root) {
    if (root == null) {
        return;
    }
    Deque<Node> stack = new LinkedList<>();
     // 不管三七二十一,现将所有左子树加入栈中
    while (root != null) {
        stack.push(root);
        root = root.left;
    }
    Node top = stack.peek();
    Node curRight = top.right;
    while (!stack.isEmpty()) {
       
        // 如果栈顶元素有右子树,将其右子树入栈,并将栈顶元素弹出
        stack.pop();
        // 对栈顶元素的右子树作相同处理
        while (curRight != null) {
            stack.push(curRight);
            curRight = curRight.left;
        }
    }
}

```
### 先序的非递归实现
总体思路: 对于每棵树而言都是先输出根,再到左子树,再到右子树;而我们压栈的过程是希望将左子树作为整体压入的,弹出的时候会先弹出最左侧的节点,这样就不能达到需要的效果了,所以我们可以在根节点入栈前,对其打印;然后压入右子树,再压入左子树
<br>
所以我们的策略就是,当左子树的节点弹出后,**不要将其根节点对应的右子树立刻弹出,而是将左子树的剩余节点迅速压入栈**,从而保证栈中是左子树的整体被压栈了.
具体步骤如下:
1. 对于每个节点而言,先压入自身,然后弹出时就打印
2. 如果有右子节点,那么先压入右子节点
3. 如果有左子节点,那么先压入左子节点
4. 只要栈不为空,就进行弹出

```java
public void preOrderNonRecur(Node root) {
    if (root == null) {
        return;
    }
    Deque<Node> stack = new LinkedList<>();
    stack.push(root);
    while (!stack.isEmpty()) {   
        Node cur = stack.pop();
        System.out.print(cur.value + " ");
        if (cur.right != null) {
            stack.push(cur.right);
        }
        if (cur.left != null) {
            stack.push(cur.left);
        }
    }
}
```
## 先序非递归实现的总结
现将头节点加入栈中(保证栈不是空的)
循环的每次迭代中:
1. 从栈中弹出一个当前节点cur
2. 打印这个节点cur 或者 (处理当前cur节点,比如加入结果集list中)
3. 如果cur节点有右子节点和左子节点的话,先压入右子节点再压入左子节点
4. 重复这个循环直到栈为空

### 中序的非递归实现
总体思路: 使用栈来代替程序中的递归调用栈.<br>
对于中序遍历中的任意一个节点:先访问其**左孩子**,而其**左孩子节点**可以视为**根节点**,于是我们将这个左孩子压栈(记录下来),然后继续访问其左孩子的**左孩子节点**,直到遇到左孩子节点为空的情况,这时候我们就可以将所有访问过的节点弹出栈并且打印.
之后对栈顶的节点去访问其右孩子,并且对其右孩子按照上面相同的逻辑进行处理; <br>
那么对于任意一个非空节点**N**:
1. 如果其左孩子**不为空**,则将该节点N**入栈**,然后用N来记录N节点的左孩子 (也就是将左孩子设置为当前的N),然后对当前节点N再进行相同的处理;
2. 如果左孩子**为空**,则**弹出**栈顶元素并打印 (说明当前节点是最左的),并且将当前的N来记录栈顶节点的右孩子(也就是将右孩子设置为当前的N);
3. 直到**N为null且栈元素为空**,遍历完成 (说明我们的循环条件是或)

```java
public void inOrderNonRecur(Node root) {
    // 使用一个栈
    Deque<Node> stack = new LinkedList<>();
    // 利用一个节点来记录当前访问的节点
    Node cur = root;
    while (!stack.isEmpty() || cur != null) {
        // 持续压栈直到左孩子为空
        while (cur != null) {
            stack.push(cur);
            cur = cur.left;
        }
        // cur一旦不是空的
        cur = stack.pop();
        // 打印这一栈顶元素
        // 也可以是将结果 添加到结果list中
        //
        System.out.print(cur.value + " ");
        cur = cur.right;
    }
    return;
}
```

另一种写法:每次只处理当前这个节点,处理完就相应的弹栈打印
```java
public void inOrderNonRecur(Node root) {
    List<Integer> res = new ArrayList<>();
    // 使用一个栈
    Deque<Node> stack = new LinkedList<>();
    // 利用一个节点来记录当前访问的节点
    Node cur = root;
    // 栈元素为空,且当前节点也为空说明完成了遍历
    while (!stack.isEmpty() || cur != null) {
        if (cur != null) {
            stack.push(cur);
            cur = cur.left;
        } else {
            // cur == null 说明到了最左侧的左子节点
            // 复用这个节点cur
            cur = stack.pop(); 
            System.out.print(cur.value + " ");
            // 第二次循环会通过同一个父节点返回到右子节点
            cur = cur.right;
        }
    }
    return res;
}
```
## 中序非递归实现总结
中序非递归的实现逻辑不同于前序和后序的非递归实现,就在于它对于根节点的打印时机处理不一样,而且要尽可能的找到最左的子树(左边界),并利用栈将最左的节点放在栈的最上方,之后选择弹出,返回了父节点,此时如果遇到有右子树时,再压入栈,重复上面的操作
压栈和打印的顺序就是:
1. 压入头节点,
2. 压入左节点(压栈到空为止,弹出并打印)
3. 弹出父节点并打印
4. 如果遇到右树,做相同的操作


### 后序的非递归实现
总体思路:
后序遍历的输出就是:先左子树,后右子树,最后是根节点.
<br>而先序的输出顺序则是:根,左,右;但是前面我们知道它的**压栈的顺序**是:根节点,右子节点,左子节点;
<br>从而保证了对于每棵子树先输出根节点(由于压栈后立刻又弹出打印了),再到左子节点,最后输出右子节点的顺序
借助这个思路我们可以利用两个栈,一个栈用于压栈调整顺序,另一个栈收集最终的打印结果

方法1:
使用两个栈,由于第一个栈是按照**中左右**压入栈,然后弹出时就是**左右中**,利用第二个栈收集这个弹出结果
```java
public void postOrderNonRecur(Node root) {
    Deque<Node> stack1 = new LinkedList<>();
    Deque<Node> stack2 = new LinkedList<>();
    if (root == null) {
        return;
    }
    //第一个栈用来调整顺序
    stack1.push(root);
    while (!stack1.isEmpty()) {
        Node cur = stack1.pop();
        // 栈1中将cur的左节点压入
        if (cur.left != null) {
            stack1.push(cur.left);
        }
        // 栈1中将cur的右节点压入
        if (cur.right != null) {
            stack1.push(cur.right);
        }
        stack2.push(cur);
    }
    //最后栈2中的元素就是所有的后序遍历结果
    while (!stack2.isEmpty()) {
        System.out.print(stack2.pop().value +  " ");
    }
    
}
```
方法2:
直接用双端队列来保存这个栈中的结果,达到先入先出的目的,其实输出还是按照栈2的形式输出,只是能够将结果直接保存下来而已
```java
public void postOrderNonRecur(Node root) {
    Deque<Integer> queue = new LinkedList<>();
    Deque<Node> stack = new LinkedList<>();
    if (root == null) {
        return;
    }
    //第一个栈用来调整顺序
    stack1.push(root);
    while (!stack.isEmpty()) {
        Node cur = stack.pop();
        // 直接利用FIFO性质将所有结果向头部不断加入
        queue.offerFirst(cur.val);
        // 栈中将cur的左节点压入
        if (cur.left != null) {
            stack.push(cur.left);
        }
        // 栈中将cur的右节点压入
        if (cur.right != null) {
            stack.push(cur.right);
        }
    }
    //最后将结果输出
    while (!queue.isEmpty()) {
        System.out.println(queue.pollFirst().value + " ");
    }
}
```



