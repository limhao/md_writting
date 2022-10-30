---
title: 阅读DKVMN时产生的问题
---

DKVMN 具有维持用户概念状态和概念更新的能力

对于俺的项目书来说 作用十分的大

在阅读模型代码文件时 产生了一些问题 在这里写一下

## pytorch维度广播

如果一个Pytorch运算支持广播的话，那么就意味着传给这个运算的参数会被自动扩张成相同的size，在不复制数据的情况下就能进行运算，整个过程可以做到避免无用的复制，达到更高效的运算。
广播机制实际上是在运算过程中，去处理两个形状不同向量的一种手段。
pytorch中的广播机制和numpy中的广播机制一样, 因为都是数组的广播机制。

如果两个数组的shape不同，就会触发广播机制

1）程序会自动执行操作使得A.shape==B.shape；
2）对应位置进行相加运算，结果的shape是：A.shape和B.shape对应位置的最大值，比如：A.shape=(1,9,4),B.shape=(15,1,4),那么A+B的shape是(15,9,4) 

条件

1.  **两个张量都至少有一个维度** 
2.  **从右往左顺序看两个张量的每一个维度，x和y每个对应着的两个维度都需要能够匹配上**

2.条件细致的说一下

1 维度可以和任何维度进行匹配

维度可以随意产生1来进行维度的对应

```python
x=torch.empty(5,3,4,1)
y=torch.empty( 3,1,1)
```

看看上面对应的例子

从右往左看

是一一对应的 那就符合广播机制

## chuck

它是将tensor按**dim**（行或列）分割成**chunk_num**个[tensor](https://so.csdn.net/so/search?q=tensor&spm=1001.2101.3001.7020)块，返回的是一个**元组**。  torch.chunk([tensor](https://so.csdn.net/so/search?q=tensor&spm=1001.2101.3001.7020),chunk数，维度）
chunks=n 代表切分成几个小块
dim= n代表要在哪个维度一进行操作

## torch.ge

torch.ge(a,b)比较a，b的大小，a为张量，b可以为和a相同形状的张量，也可以为一个常数。

## mask_select

一般配个torch.ge使用 

```python
import torch

x = torch.randn([3, 4])
print(x)
# 将x中的每一个元素与0.5进行比较
# 当元素大于等于0.5返回True,否则返回False
mask = x.ge(0.5)
print(mask)
print(torch.masked_select(x, mask))
 

'''
tensor([[ 1.2001,  1.2968, -0.6657, -0.6907],
        [-2.0099,  0.6249, -0.5382,  1.4458],
        [ 0.0684,  0.4118,  0.1011, -0.5684]])
tensor([[ True,  True, False, False],
        [False,  True, False,  True],
        [False, False, False, False]])
tensor([1.2001, 1.2968, 0.6249, 1.4458])
'''
```

## torch.t()

求转置矩阵

## mul matmul mm

mul 是矩阵对应位相乘 a，b的维度必须满足广播才行

mm 是矩阵相乘 最正常的情况

matmul = mm

