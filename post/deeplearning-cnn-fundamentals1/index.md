# 深度学习-CNN相关01

  > 在图像复原的过程中，图像上一点点噪声就可能对复原的结果产生较大影响，由于复原算法本身一般都会放大噪声，这时候需要在最优化的问题模型中添加一个正则项约束图像的噪声 <font color = brown>（其本身也是loss的一部分）</font>比如图片中相邻像素值之间的差异，可通过降低TV loss来解决

## Total Variation Loss (TV loss)初始定义：
> Rudin等人(Rudin  1990)观察到，受到噪声污染的图像总变分大于无噪声的图像总变分。那么就可以通过最小化图像总变分去抑制噪声，而图片中相邻像素值的差异可以通过降低TV loss来一定程度上缓解。比如对抗checkerboard。

总变分定义为梯度幅值大小的积分
$$
J(u) = \int_{\Omega_{u}}|\nabla_{u}|{dxdy} = \int_{D_{u}}\sqrt{u_{x}^{2}+v_{x}^{2}}dxdy
$$
其中：
$$
u_{x} = \frac{\partial{u}}{\partial{x}},v_{y} = \frac{\partial{v}}{\partial{y}},
$$
而图像支持域为
$
D_{u}
$
,限制总变分就会限制噪声
## 扩展定义
带有阶数的TV loss如下
$$
J^{\beta} (f)= \int_{\Omega}\Big((\frac{\partial{f(u,v)}}{\partial{u}})^{2}+(\frac{\partial{f(u,v)}}{\partial{v}})^{2}\Big)^{\frac{\beta}{2}}dxdy
$$
但是在图像中，连续的积分就是像素离散域求和
$$
J(x) = \sum_{i,j}\big( (x_{[i,j-1]}- x_{[i,j]})^{2}+(x_{[i+1,j]}- x_{[i,j]})^{2}\big)^{\frac{\beta}{2}}
$$
也就是对于每个像素点$x_{[i,j]}$与其**正下方**的像素值差的平方加上其**正左方**的像素值差的平方，然后开$\frac{\beta}{2}$次根
## 3.基于pytorch实现及效果分析
```python
import torch
import torch.nn as nn
from torch.autograd import Variable

class TVLoss(nn.Module):
    def __init__(self,TVLoss_weight = 1):#设置TVLoss_weight为1
        super(TVLoss, self).__init__() 
        self.TVLoss_weight = TVLoss_weight
    def forward(self, x):
        #输入tensor为x 
        # x = [batchSize,width,height,channels]
        batch_size = x.size()[0]
        h_x = x.size()[2]
        w_x = x.size()[3]
        #计算图片高度
        count_h = self._tensor_size(x[:,:,1:,:])
        #计算图片宽度
        count_w = self._tensor_size(x[:,:,:,1:])
        
        h_tv = torch.pow(x[:,:,1:,:]-x[:,:,:h_x-1,:]，2).sum()
        w_tv = torch.pow((x[:,:,:,1:]-x[:,:,:,:w_x-1]),2).sum()
        return self.TVLoss_weight*2*( h_tv/count_h + w_tv/count_w) / batch_size
        
    def _tensor_size(self, t):
        return t.size()[1]*t.size()[2]*t.size()[3]
    def main():
        # x = Variable(torch.FloatTensor([[[1,2],[2,3]],[[1,2],[2,3]]]).view(1,2,2,2), requires_grad=True)
        # x = Variable(torch.FloatTensor([[[3,1],[4,3]],[[3,1],[4,3]]]).view(1,2,2,2), requires_grad=True)
        # x = Variable(torch.FloatTensor([[[1,1,1], [2,2,2],[3,3,3]],[[1,1,1], [2,2,2],[3,3,3]]]).view(1, 2, 3, 3), requires_grad=True)
        x = Variable(torch.FloatTensor([[[1, 2, 3], [2, 3, 4], [3, 4, 5]], [[1, 2, 3], [2, 3, 4], [3, 4, 5]]]).view(1, 2, 3, 3),requires_grad=True)
        addition = TVLoss()
        z = addition(x)
        print x
        print z.data
        z.backward()
        print x.grad
    
    if __name__ == '__main__':
        main()
```




