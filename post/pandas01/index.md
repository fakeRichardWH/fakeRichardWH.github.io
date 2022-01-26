# Pandas中loc和iloc区别

# loc vs iloc
**loc函数**：通过行索引 "Index" 中的具体值来取行数据（如取"Index"为"A"的行）**可以认为是利用行名称来提取，方便记忆**<br>
而且当index具备特殊意思的时候：
例如 当index是人名时,会将所有相同人名字的信息提取出来

**iloc函数**：通过具体的行号来取行数据（如取第二行的数据）**利用纯粹的下标索引找行数据，如果当对行名称不记得的时候可以使用**
## 五种使用方法
本文给出loc、iloc常见的五种用法，并附上详细代码。
```Python
import numpy as np
import pandas as pd
# create a dataframe
data = pd.DataFrame(np.arange(16).reshape(4, 4), index = list("abcd"), columns = list("ABCD"))

In [6]: data
Out[6]: 
    A   B   C   D
a   0   1   2   3
b   4   5   6   7
c   8   9  10  11
d  12  13  14  15
```
## 1. 取一整行数据
1. 取出索引为“a”的行
```Python
data.loc["a"]
In [7]: data.loc["a"]
Out[7]: 
A    0
B    1
C    2
D    3
Name: a, dtype: int64
```
2. 取出第一行数据(index == 0)，也就是索引为“a”的数据
```Python
In [8]: data.iloc[0]
Out[8]: 
A    0
B    1
C    2
D    3
Name: a, dtype: int64
```
## 2. 取指定的行 和 列 数据
1. 利用 loc 根据**行和列**的索引名称 获取
```Python
In [9]: data.loc[["a","b"],["A","B"]]
Out[9]: 
   A  B
a  0  1
b  4  5
```
2. 在不知道数据分布情况下：使用行和列索引下标查询
```Python
In [10]: data.iloc[[0,1],[0,1]]
Out[10]: 
   A  B
a  0  1
b  4  5
```
## 3. 提取某一列的所有行数据
1. 使用loc查询某一列所有行数据
```python
In [11]: data.loc[:,["A"]]
Out[11]: 
    A
a   0
b   4
c   8
d  12

```
2. 使用iloc查询某一列的所有信息
```python
# 注意传入的参数都是 list
In [12]: data.iloc[:,[0]]
Out[12]: 
    A
a   0
b   4
c   8
d  12
```

## 4. 利用loc/iloc提取所有数据
```python
In [13]: data.loc[:,:]
Out[13]: 
    A   B   C   D
a   0   1   2   3
b   4   5   6   7
c   8   9  10  11
d  12  13  14  15

In [14]: data.iloc[:,:]
Out[14]: 
    A   B   C   D
a   0   1   2   3
b   4   5   6   7
c   8   9  10  11
d  12  13  14  15
```
## 5.利用loc函数，根据某个筛选条件来提取数据所在行
1. 单个条件查询
```python
In [15]: data.loc[data["A"] == 0] # data["A"]相当于拿出A一整列
Out[15]: 
   A  B  C  D
a  0  1  2  3

# 获取相同的数据其他方法
# 使用dataframe访问方法
In [19]: data[data["A"] == 0]
Out[19]: 
   A  B  C  D
a  0  1  2  3

# 使用df.isin([ value]) 方法
In [20]: data[data["A"].isin([0])]
Out[20]: 
   A  B  C  D
a  0  1  2  3

```
2. 多条件筛选

**多个条件之间一定要加括号 否则会出错**
```python
In [17]: data.loc[(data["A"] == 0) & (data["B"] == 2)]
Out[17]: 
Empty DataFrame
Columns: [A, B, C, D]
Index: []

# 使用isin()的查询方式
In [23]: data[(data["A"].isin([0])) & (data["B"].isin([2])) ]
Out[23]: 
Empty DataFrame
Columns: [A, B, C, D]
Index: []
```

