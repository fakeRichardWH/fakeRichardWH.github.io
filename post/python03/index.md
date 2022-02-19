# Python中with使用及远离

# with关键字
with语句用于异常处理,相当于是对`try...excepty...finally`进行了封装,缩短了`Python`代码,简化了文件流资源的管理
## 一个有问题的代码案例
```python
file = open("./test.txt","w")
file.write("hello world!")
file.close()
```
如果代码调用`write()`的过程中,出现异常,则`close()`将无法执行,从而导致该`test.txt`会被程序持续占用无法释放.
## 改进
```python
file = open('./test_runoob.txt','w')
try:
    file.write('hello world!')
except OSError as err:
    print("OS error raised!")
finally:
    file.close()
```
这段代码的`finally`无论是否会发生资源的占用都会将对应的文件流资源关闭,不会因为执行了异常而占用资源.
## 使用with
```python
with open("./test.txt","w") as f:
    f.write("hello world!")
# 上面这个代码等效于try...except...finally
# 验证资源是否关闭
f.closed
Out[1]: True
```
## 使用with
```python
with open("./test.txt","w") as f:
    f.read()
```
