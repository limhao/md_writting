---
title: nas可微方法
---

CVPR这个方法跟DARTS方法很像

![](https://pic.imgdb.cn/item/641bb64ba682492fcce585c1.png)

## 前言

![](https://pic.imgdb.cn/item/641bb649a682492fcce58298.png)

假如说 

一个CNN 一般有几个参数

1. 卷积核大小
2. strip的个数

假如说 咱们上面两个超参数都有三个选项

那一共就有9种

假如说 咱们一共有20层

那么 我们的可能性就是

$$9^{20}$$

ok 大致情况说明白了

## super net 

![](https://pic.imgdb.cn/item/641bb649a682492fcce582f7.png)

### 来看看一个层

输入一个x 

输出 9个模块处理的效果

然后通过这九个模块的处理结果 加权和 就能输出z

![](https://pic.imgdb.cn/item/641bb64ba682492fcce585f2.png)

针对于 每个层来说 

可训练的参数 有 每个模块的权重参数

还有 9个加权和的权重

而针对于 层与层之间 层内的参数是不共享的

![](https://pic.imgdb.cn/item/641bb64ba682492fcce58623.png)

### 总的来看

![](https://pic.imgdb.cn/item/641bb70fa682492fcce6ce23.png)

### 模型训练

![](https://pic.imgdb.cn/item/641bb70fa682492fcce6ce7b.png)

这样 就能确定要 我们需要的路径

## 还有一条路（考虑时延）

latency 延迟

模块的平均时延

![](https://pic.imgdb.cn/item/641bb8efa682492fcce9d14c.png)

总的时延

![](https://pic.imgdb.cn/item/641bb8efa682492fcce9d17e.png)

### 实验添加损失函数的方法

![](https://pic.imgdb.cn/item/641bb8efa682492fcce9d1a7.png)

## 总的来看

![](https://pic.imgdb.cn/item/641bb8efa682492fcce9d1e2.png)

![](https://pic.imgdb.cn/item/641bb8f0a682492fcce9d32c.png)

