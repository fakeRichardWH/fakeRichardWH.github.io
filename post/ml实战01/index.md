# ML实战-特征工程-Two sigma租房数据

# 步骤:
## 1.获取数据
在terminal中输入:
```bash
wget https://cdn.coggle.club/kaggle/two-sigma-connect-rental-listing-inquiries.zip
unzip two-sigma-connect-rental-listing-inquiries.zip
```
## 2.读取数据
```python
import pandas as pd
import numpy as np
test_df = pd.read_json("train.json.zip", compression = "zip")
test_df = pd.read_json("test.json.zip", compression = "zip")
```
## 3.特征工程
### 数据统计
<font color='red'> **【特征提取步骤1.】** </font>
<font color='red'> **1、【统计训练集、测试集中所有房间个数，bedrooms与bathroom之和】，新增一列特征命名为num_rooms** </font>
```python
train_df["num_rooms"] = train_df["bedrooms"] + train_df["bathrooms"]
test_df["num_rooms"] = test_df["bedrooms"] + test_df["bathrooms"]
```
<font color='red'> **2、【统计训练集、测试集中所有照片的数量】，新增一列特征命名为num_photos** </font>
```python
train_df["num_photos"] = train_df["photos"].apply(len)
test_df["num_photos"] = test_df["photos"].apply(len)
```
`Pandas.Series.apply(func)`是pandas中提供对Series中的值产生相应`func`作用的一个函数,上面的例子就是传入了一个len函数,对photos这个Series中的每个value也就是list元素进行求长度的运算,你也可以自定义一个func代替len计算
#### 一个使用apply的例子🌰
```python

>> s = pd.Series([20, 21, 12],
              index=['London', 'New York', 'Helsinki'])
>> s
Out[1]:
London      20
New York    21
Helsinki    12
dtype: int64
# 使用匿名函数 对Series中的每个元素进行平方
# lambda x 中的x就是Series中的每个变量
>> s.apply(lambda x : x**2)
Out[2]:
London      400
New York    441
Helsinki    144
dtype: int64
```

<font color='red'> **3、【删除照片这一列】** </font>
```python
train_df = train_df.drop('photos', axis=1)
test_df = test_df.drop('photos', axis=1)
```
`Pandas.DataFrame.drop()`方法默认是对每一行进行操作,如果要对列操作需要将axis设置为1

<font color='red'> **4、【对于具有文本特征的列进行分析】,统计训练集、测试集中description不重复单词个数，并删除原始description列** </font>
```python
# 这里先是用匿名函数 对 description列的每个元素,用正则表达式带有空格的部分做了拆分
# 其次再用set方法保留不重复的单词,并进行个数统计len

train_df["num_description_words_Distinct"] = train_df["description"].apply(lambda x : len(set(x.split(" "))))
test_df["num_description_words_Distinct"] = test_df["description"].apply(lambda x : len(set(x.split(" "))))
train_df.drop('description',axis = 1)
test_df.drop('description',axis = 1)
```
<font color='red'> **5、【对于时间/日期元素处理】将时间元素的字符串列转为datetime object** </font>
```python
train_df["created"] = pd.to_datetime(train_df["created"])
test_df["created"] = pd.to_datetime(test_df["created"])

# 获取年/月/日/小时
train_df["created_hr"] = train_df["created"].dt.hour
train_df["created_d"] = train_df["created"].dt.day
train_df["created_m"] = train_df["created"].dt.month
train_df["created_y"] = train_df["created"].dt.year

test_df["created_hr"] = test_df["created"].dt.hour
test_df["created_d"] = test_df["created"].dt.day
test_df["created_m"] = test_df["created"].dt.month
test_df["created_y"] = test_df["created"].dt.year
```
<font color='red'> **4、【count_encoder】** </font>
