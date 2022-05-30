---
title: PyTorch 张量操作 
---





感谢： https://mp.weixin.qq.com/s/Xy47wBVurmCH6QmGtM_mow

## 0. 前言

​	在阅读别人编写的源码的过程中，对于他人能够灵活操作torch向量 感觉十分的神奇，于是乎俺也来写一个文档作为一个手册的使用不就好了嘛。（先统计）

## 1. 基本数据形式

读Caser论文有感！其对于数据的操控 我作为一个单纯的研究生感到大受震撼 

### 4d数据结构

深度学习框架，数据为4D 用NCHW或NHWC表示

- N - Batch
- C - Channel
- H - Height
- W - Width

在pytorch 为 NCHW 

在tensorflow 缺省NHWC GPU支持NCHW

tensor.size 

[N,C,H,W]

### 1.1 有些数据 这样子的[N,H,W]

当然 在我推荐领域 channel 也不存在rgb 这么丰富的颜色

直接看代码 给channel 设置成1 就好

```python
torch.unsqueeze(1)
```

### 1.2 还有些数据 是这个亚子 [w,h]

那就这个亚子吧 因为 加batch（一般不为1） 所以 我也没啥好的方法来着

### 1.3 有些数据 是最后这个样子 [N,H] [N,W]

那这个样子呢 是存在补救空间的 嗯嗯！

```python
torch.unsqueeze(你想添加的维度)
```

## 2. 我看到的操作

### 张量的数据类型

 PyTorch有9种CPU张量类型和9种GPU张量类型 

 ![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/gYUsOT36vfouFe7PiabXDb2iaAXq0FCUdnONvpcpnD1xKFIY04ghjwbtvQSCqWgteHnFV1ibH5tgY96xmdJFQXTWQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1) 
### 张量基本信息
```python
tensor = torch.randn(3,4,5)
print(tensor.type())  # 数据类型
print(tensor.size())  # 张量的shape，是个元组
print(tensor.dim())   # 维度的数量
```
### 数据类型转换
```python
# 设置默认类型，pytorch中的FloatTensor远远快于DoubleTensor
torch.set_default_tensor_type(torch.FloatTensor)

# 类型转换
tensor = tensor.cuda()
tensor = tensor.cpu()
tensor = tensor.float()
tensor = tensor.long()
```
### torch.div

```python
x = torch.tensor([ 0.3810,  1.2774, -0.2972, -0.3719,  0.4637])
torch.div(x, 0.5)
tensor([ 0.7620,  2.5548, -0.5944, -0.7438,  0.9274])

a = torch.tensor([[-0.3711, -1.9353, -0.4605, -0.2917],
                   [ 0.1815, -1.0111,  0.9805, -1.5923],
                   [ 0.1062,  1.4581,  0.7759, -1.2344],
                  [-0.1830, -0.0313,  1.1908, -1.4757]])
b = torch.tensor([ 0.8032,  0.2930, -0.8113, -0.2308])
torch.div(a, b)
tensor([[-0.4620, -6.6051,  0.5676,  1.2639],
        [ 0.2260, -3.4509, -1.2086,  6.8990],
        [ 0.1322,  4.9764, -0.9564,  5.3484],
        [-0.2278, -0.1068, -1.4678,  6.3938]])
```

可以看出来 这玩意是要对其的

### squeeze() 

当其中没有参数的时候，其缺省值是将里面维度为1的进行压缩

感谢：https://blog.csdn.net/qq_40714949/article/details/112770987

**压缩 原来是压缩 维度为1的操作啊**

首先 先说明白 维度一维 线

二维 面

三维 正方体

**在程序中 就是 0，1，2 来表示上面的三维**

本人自己的操作

[![](https://s1.ax1x.com/2022/04/27/LbWnJJ.png)](https://imgtu.com/i/LbWnJJ)

```python
# 现在压缩函数 squeeze 其实和zip 联想 想一想哈哈哈
# 输入
import torch

x = torch.rand(3, 2, 1, 2, 1)
print(x.size())
x = x.squeeze()
print(x.size())
# 输出
torch.Size([3, 2, 1, 2, 1])
torch.Size([3, 2, 2])

# 此外，也可以指定尝试将哪一个维度进行压缩。如果被指定的维度其维数为1，则压缩，反之不对该维度操作：
import torch

x = torch.rand(3, 2, 1, 2, 1)
print(x.size())
x = x.squeeze(0)
print(x.size())
x = x.squeeze(2)
print(x.size())
# 输出
torch.Size([3, 2, 1, 2, 1])
torch.Size([3, 2, 1, 2, 1])
torch.Size([3, 2, 2, 1])
```

### unsqueeze()

我的很直接的说法就是 这玩意是拿来扩维的

这个函数必须在其中填入dim=？这个参数

如果单纯从数字角度来考虑这个事情，那就很简单

例如 

```python
import torch
x = torch.rand(3,2)
# 在维度2的位置增加维数1
x = torch.unsqueeze(1)
```

[![](https://s1.ax1x.com/2022/04/27/Lb5VXR.png)](https://imgtu.com/i/Lb5VXR)

### torch.cat

我觉得我在代码中的注释 很好的解释了这个问题

```python
# 输出在一维进行拼接 
# 意思就是在二维进行拼接
# 我的脑子突然有冒出个有意思的想法
# concat这个操作不是拼接嘛
# 一个一个的对象 凭借成一对一对的（最近的情况哈哈）
# 既然是拼接 那么带我们对应拼接的维度 就应该有所增加
# 这里的 tensor 为 4个 [1,4] 的向量
# concat（0） 的话 就会成为 [4, 4] 
# concat（1） 的话 就会变成 [1,16]
out_hs = torch.cat(out_hs, 1)
```

### torch.view

感谢： https://blog.csdn.net/Flag_ing/article/details/109129752

网上的看不懂啊

网上的看不懂啊

网上的看不懂啊

可能是我水平差了

我用我的大白话来讲明白这个事儿

​	日常我们输入卷积层的数据形式 为 4D，view这个小妖精呢，我的感觉就像是捏泥巴。我脑子响起了东北玩神曲 ![](https://img0.baidu.com/it/u=2634558474,2858767346&fm=253&fmt=auto&app=138&f=JPEG?w=430&h=268)      

给大家看看有意思的

[![](https://s1.ax1x.com/2022/05/11/Oa1psU.png)](https://imgtu.com/i/Oa1psU)

注意看两次的size 打印 

我对于这个玩意的感觉就是将数字打散 然后重新组合起来

十分有趣

### torch.mm
```python
# Matrix multiplcation: (m*n) * (n*p) * -> (m*p).
result = torch.mm(tensor1, tensor2)
```


### torch.bmm

```python
# Batch matrix multiplication: (b*m*n) * (b*n*p) -> (b*m*p)
result = torch.bmm(tensor1, tensor2)
```

### torch.baddbmm

感谢： https://blog.csdn.net/qq_41289353/article/details/123644925

![](C:\Users\warma\AppData\Roaming\Typora\typora-user-images\1652250868273.png)

```python
M = torch.randn(10, 3, 5)
batch1 = torch.randn(10, 3, 4)
batch2 = torch.randn(10, 4, 5)
torch.baddbmm(M, batch1, batch2).size()
'''
output:torch.Size([10, 3, 5])
'''
```

