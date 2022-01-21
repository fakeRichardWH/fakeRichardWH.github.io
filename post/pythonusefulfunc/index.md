# Lambda函数使用

# 匿名函数
以`lambda` 作为关键字,可以在一行内完成定义和声明，这个匿名函数由于没有实际的名称，所以一般在Python中没有什么复杂的功能。
## 语法
```Python
lambda arg1, arg2, arg3 ... : <expression>
```
其中 `arg1`,`arg2`,`arg3`是函数的输入参数，而`
<expression>`则是函数体，运算结果就是表达式结果。

### 例子
```Python
# 输入是x,y; 输出是x*y
lambda x, y : x*y 

# 输入是空; 输出是None
lambda : None 

# 输入是任意个数的参数（以tuple传入），但是隐含条件需要这些参数可以相加
lambda *args : sum(args) 
```

## 使用
`lambda`往往有下面三种常用的使用方式

### 赋值给其他变量
例如，执行语句
```Python
add = lambda x, y: x+y
```
定义了加法函数`lambda x, y: x+y`，并将其赋值给变量`add`，这样变量`add`便成为具有加法功能的函数。例如，执行`add(1,2)`，输出为`3`。

### 替换其他函数
例如，为了把标准库`time`中的函数`sleep`的功能屏蔽`(Mock)`，我们可以在程序初始化时调用：
```Python
time.sleep = lambda x : None
```
。这样，在后续代码中调用`time`库的`sleep`函数将不会执行原有的功能。例如，执行`time.sleep(3)`时，程序不会休眠3秒钟，而是什么都不做。

### 作为返回值
由于函数的返回值也可以是函数。例如
```Python
def cal(x, y):
    x = x**2
    y = 1
    return lambda x, y: x+y
```
返回一个加法函数。这时，`lambda`函数实际上是定义在某个函数内部的函数，称之为嵌套函数，或者内部函数。对应的，将包含嵌套函数的函数称之为外部函数。内部函数能够访问外部函数的局部变量，这个特性是闭包(Closure)编程的基础，在这里我们不展开。

### 内置函数接受lambda表达式
1. filter函数
filter(function, iterable)函数本身传入的第一个参数为函数名，第二参数为可迭代对象，返回值是可迭代对象
`lambda`用来作为指定过滤列表元素的条件。
```python
import numpy as np
list1 = np.arange(3)
filter(lambda x : x % 2 == 0, list1)

# result: [0,2]
```
2. sorted函数
sorted(iterable, key = function)函数本身是对iterable对象中所有元素按照function排序
`lambda`用来作为指定的排序条件。
```python
import numpy as np
arr = np.arange(9)
print(sorted(arr, key = lambda x : abs(5 - x)))
# result: [5, 4, 6, 3, 7, 2, 8, 1, 0]
# 结果是依次排序数组中离5最近的元素值
```
3. map函数
map(function, iterable)函数本身是对iterable对象中所有元素按照function排序
`lambda`用于指定对列表中每一个元素的相同映射操作。
```python
import numpy as np
list = np.arange(9)
sqr = map(lambda x : x**2, list)
print([x for x in sqr])
# result: [0, 1, 4, 9, 16, 25, 36, 49, 64]
# 对[0,9]逐元素平方
```
4. reduce函数
![reduceProcess.png](/images/postImgs/reduceProcess.png)
