# XGBOOST

# XGBoost
由于它计算的准而且快，所以用在各大比赛当中使用的多，不要迷信深度学习
xGBoost = extreme + GBDT = extreme + Gradient + BoostDT
Boosting -> Boosting DT -> gradient BDT -> 
使用了一阶梯度信息
## 所谓的最优函数其实是由参数决定的，其实学习方式是用最优参数。构建了模型，然后对参数求偏导。但如果没有模型参数，只有f(x)，那我们也可以只对函数本身求偏导
## 如何理解梯度提升算法？
关键在于梯度提升算法使用的不再是基于参数的负梯度，而是基于模型的**损失函数**的**负梯度**作为下次迭代中残差的近似值
$\argmin$
