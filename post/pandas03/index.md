# Pandas中DataFrame和Series总结

# pandas数据结构
Pandas主要的两个数据结构是DataFrame 和 Series
DataFrame: 二维数据结构,整个表格,多行多列
Series: 一维数据结构,**可以看做是DataFrame的一个行或者一个列**
如果从DataFrame查询出来的是一个区块,那么该区块仍然是DataFrame
而如果是个一维的数据,那么可以用Series的**字典键值对**进行快速访问
## Series
Series 类似一维数组,但是它仍然包含了数据和与之相关的**数据索引**
### 使用list创建Series
```python
import numpy as np
import pandas as pd
s1 = pd.Series([1, "a", 5.3, "Test"])
# 获取索引
s1.index
# 获取数据
s1.values
```
### 使用自定义的index创建Series
```python
import numpy as np
import pandas as pd
s2 = pd.Series([1, "a", 5.3, "Test"], index = ["A","B","C","D"])
# 获取索引
s2.index
# 获取数据
s2.values
```
### 使用字典创建Series
```python
import numpy as np
import pandas as pd
dict1 = {"Ohio" : 300, "Rivencrow" : 400, "Utah" : 500}
s3 = pd.Series(dict1)
# 获取索引
s3.index
# 获取数据
s3.values
```
## 根据标签索引查询数据
类似Python中的dict
```python
# 查询s2 index = "A"的内容
s2["A"]

Out[1]: 1

# 查看数据类型
type(s2["A"])

Out[2]: int

# 获取多个值 在中括号中传入一个列表值
s2[["B","A"]] 

Out[3]:
A     1
C    5.3
dtype: object
# 查看返回类型
type(s2[["B","A"]]) 

Out[4]: 
pandas.core.series.Series
```
# DataFrame
- 每一列可以是不同的值类型 (数值,字符串,bool)
- 既有行索引index又有列索引columns

## 创建方法
根据读取文件来创建 (txt, csv, excel, mySQL数据库)
## 根据多个字典序列创建dataFrame
```python
data = {
    # 每个列名  + 列值
    "states" : ["Ohio", "Nevada", "Florida","California"], 
    "year" : [2001,2000,2011,2002],
    "Dash" : [1.2, 1.3, 1.4, 1.5]
}
df = pd.DataFrame(data)

Out[5]:
	states	year	Dash
0	Ohio	2001	1.2
1	Nevada	2000	1.3
2	Florida	2011	1.4
3	California	2002	1.5

# 查看所有index
df.index
Out[6]:
RangeIndex(start=0, stop=4, step=1)

#查看所有列名称
df.columns
Out[7]:
Index(['states', 'year', 'Dash'], dtype='object')

#查看各个列的数据类型
df.dtypes
Out[8]:
states     object
year        int64
Dash      float64
dtype: object
```
## 从DataFrame中查询
- 如果只查询的是一列或者一行,那么返回值是pd.Series
- 如果查询的是多列且多行,那么返回值是pd.DataFrame
### 查询列
```python
# 结果为Series
type(df["year"])
# 结果为DataFrame
type(df[["year","Dash"]])
```
### 查询行 
**一般切片查询是不包含末尾的3,但是loc是包含的**
返回的index是列名
```python
df.loc[1]
Out[9]:
states    Nevada
year        2000
Dash         1.3

# 查询多行 
df.loc[1:3]
Out[10]:
states	year	Dash
1	Nevada	2000	1.3
2	Florida	2011	1.4
3	California	2002	1.5
```

