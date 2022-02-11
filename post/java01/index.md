# java-Stack

为什么`Java`的集合类没有单独的`Stack`接口呢？因为有个遗留类名字就叫Stack，出于兼容性考虑，所以没办法创建`Stack`接口，只能用`Deque`接口来“模拟”一个`Stack`了
```java
public static testStack() {
    Deque<Integer> stack = new Deque<Integer>();
    stack.push(1);
    stack.push(3);
    stack.push(4);
    stack.peek(); // 4
    stack.pop(); // 4
    stack.pop(); // 3
    stack.pop(); // 1
}
```
Deque接口的stack主要调用有三个方法: `push()` `peek()` `pop()` 
不要调用`addFirst()`/`removeFirst()`/`peekFirst()`方法，这样代码更加清晰

