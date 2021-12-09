# DeepLearning TensorFlow笔记

## n阶张量

|  维度   | 张量  |
|  ----  | ----  |
| 0-D  | 标量 |
| 1-D  | 向量 |
判断张量是多少阶的，就看左侧括号有多少个，0个就是0阶的，
## tf.Variable
### tensorflow数据类型有：
1. tf.int, tf.float:
tf.int32, tf.float32, tf.float64
2. tf.bool:
tf.
3. tf.string:

## 使用一个线性单元 （回归任务）
任务：给出一个12维的葡萄酒数据，前11维是详细的酒指标，最后一维是酒的质量好坏
```
     fixed    volatile citric residual-free   sulfur  total-sulfur
     acidity  acidity  acid   sugar chlorides dioxide dioxide   density  pH sulphates alcohol quality
0	7.4	0.70	0.00	1.9	0.076	11.0	34.0	0.9978	3.51	0.56	9.4	5
1	7.8	0.88	0.00	2.6	0.098	25.0	67.0	0.9968	3.20	0.68	9.8	5
2	7.8	0.76	0.04	2.3	0.092	15.0	54.0	0.9970	3.26	0.65	9.8	5
3	11.2	0.28	0.56	1.9	0.075	17.0	60.0	0.9980	3.16	0.58	9.8	6
4	7.4	0.70	0.00	1.9	0.076	11.0	34.0	0.9978	3.51	0.56	9.4	5
```
1. Input shape
输入维度大小

```python
import pandas as pd
red_wine = pd.read_csv
input_shape = [11]
```

