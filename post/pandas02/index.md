# pandas02

# pandas数据读取
Pandas读取表格类型的数据然后进行分析,支持的数据类型和读取方法如下
|  数据类型   | 格式  | 读取方法|
|  ----  | ----  |----|
| csv, tsv, txt  | 用**逗号**分隔或者**tab**分隔的纯文本文件 |`pd.read_csv()`|
| excel  | 微软的xls/xlsx文件|`pd.read_excel()`|
|mySQL|关系型数据库表|`pd.read_sql()`|
# 读取纯文本文件
## 1.1 读取csv,使用默认的标题行
一个DataFrame中,最左侧的是index,最上方的是columns(列名),中间的部分是data
```python
fpath = "./ratings.csv"
ratings = pd.read_csv(fpath)
# 查看数据的前10行
ratings.head(10)
# 查看数据的形状 返回值为 (行数,列数)
ratings.shape
Out[2]: (100836,4) 
# 查看列名的列表
ratings.columns
Out[3]: Index(['userID','movieID','rating','timestamp'], dtype = 'object')
# 查看每一列的数据类型
ratings.dtypes
Out[4]:
'userID': int64
'movieID': int64
'rating': float64
'timestamp': int64
dtype: object
```
## 1.2 读取txt文件,自定义分隔符和列名
```python
fpath = "./test.txt"
test = pd.read_csv(
    fpath,
    sep = '\t' # 定义分隔符 使用正则表达式
    header = None, # 没有标题行 所以设置为None
    names = ['birthDate', 'age', 'name'] # 定义列名 一定要和原数据一致 方便后续根据列名筛选数据
)
```
# 读取excel文件
```python
fpath = "./test2.xlsx"
test2 = pd.read_excel(fpath)
```
# 读取MySQL数据库
```python
import pymysql
# 利用pymysql获取与数据库的连接
conn = pymsql.connect(
    host = "127.0.0.1",
    user = "root",
    password = "123456",
    database = "db1",
    charset = "utf8"
)
# 传入两个参数:
# 第一个参数是String类型的SQL语句
# 第二个参数是数据库的一个连接
db1 = pd.read_sql("select * from tableName1", con = conn)
db1

```
