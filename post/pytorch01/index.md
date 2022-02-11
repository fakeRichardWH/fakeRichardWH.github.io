# Pytorch中view方法的坑

# pytorch View视图
## 你咋就这么特别呢？
查看Pytorch官方文档:
>PyTorch allows a tensor to be a `View(视图)` of an existing tensor.`View(视图)` tensor shares the same underlying data with its base tensor. Supporting `View(视图)` avoids explicit data copy, thus allows us to do fast and memory efficient reshaping, slicing and element-wise operations.
也就是说 视图类型的tensor 本身 不是显式的拷贝了数据，而是使用了它的一个引用。
```python
>>> t = torch.rand(4, 4)
>>> b = t.view(2, 8) 
>>> t.storage().data_ptr() == b.storage().data_ptr() # True 't' 和 'b' 用的是同一块内存

# 但是 b 的 shape 属性是改变了的
>>> t.shape
torch.Size([4, 4])
>>> b.shape
torch.Size([2, 8])
>>> print(t)
tensor([[0.4473, 0.3079, 0.5223, 0.2924],
        [0.9936, 0.9032, 0.7414, 0.6115],
        [0.0904, 0.6525, 0.6720, 0.8673],
        [0.0829, 0.1356, 0.9617, 0.2030]])
>>> print(b)
tensor([[0.4473, 0.3079, 0.5223, 0.2924, 0.9936, 0.9032, 0.7414, 0.6115],
        [0.0904, 0.6525, 0.6720, 0.8673, 0.0829, 0.1356, 0.9617, 0.2030]])
```
由于共用一个地址空间，修改 b 也会同时修改 t

```python
>>> b[0][0] = 1
>>> print(b)
tensor([[1.0000, 0.3079, 0.5223, 0.2924, 0.9936, 0.9032, 0.7414, 0.6115],
        [0.0904, 0.6525, 0.6720, 0.8673, 0.0829, 0.1356, 0.9617, 0.2030]])
>>> print(t)
tensor([[1.0000, 0.3079, 0.5223, 0.2924],
        [0.9936, 0.9032, 0.7414, 0.6115],
        [0.0904, 0.6525, 0.6720, 0.8673],
        [0.0829, 0.1356, 0.9617, 0.2030]])
```
## 与view()类似的方法
>Typically a PyTorch op returns a new tensor as output, e.g. add(). But in case of view ops, outputs are views of input tensors to avoid unncessary data copy. No data movement occurs when creating a view, view tensor just changes the way it interprets the same data. 
>Taking a view of contiguous tensor could potentially produce a non-contiguous tensor. Users should be pay additional attention as contiguity might have implicit performance impact. transpose() is a common example.

可以看到一般来说一个Pytorch操作会返回一个新的tensor作为输出，也就是在一个新内存空间上申请了一个位置，例如add()方法，但是对于view的这种方式，一般输出都会避免不必要的数据深拷贝,仅仅是改变数据的读取顺序。

```python
## 下面的代码中的变量与前面的一致
>>> c = torch.ones_like(b)
>>> print(c)
tensor([[1., 1., 1., 1., 1., 1., 1., 1.],
        [1., 1., 1., 1., 1., 1., 1., 1.]])
>>> b = b.add(c)
>>> id(b) == id(c) 
False
```

**需要注意**的是：`view()`和`reshape()`最大的区别就是当你用`view()`查看一个连续的tensor的时候会产生不连续的tensor

```python
## 下面的代码中的变量与前面的一致
>>> d = torch.tensor([[1, 2],[3, 4]])
>>> t = d.transpose(0,1) # `t`是d的一个view，没有发生地址变化
# 但是view tensor本身是不连续的
>>> t.is_contiguous()
False
# 如果想要得到的 view tensor(视图张量)也是连续的
>>> c = t.contiguous()
```
下面列举一些常见的方法也是类似View视图的操作，并且也具有刚才的几个坑
- tensor[0, 2:, 1:7:2]返回的也是tensor的一个视图
- unflatten()
- unfold()
- unsqueeze()
- squeeze()
- detach()
当通过索引来访问tensor，Pytorch和Numpy的机制类似，基础的索引就只是返回（views）视图（例如:通过索引赋值都是在原地址上修改），高级的索引操作返回的是数据的深拷贝。
## 注意区分`view()` 和 `reshape()`
reshape(), reshape_as() and flatten() 可以返回一个视图 或者 是一个新tensor
contiguous() 返回它自身 当且仅当输入tensor已经是连续的了，否则就会返回一个新的拷贝后的连续tensor
