---
title: Recommender-System-Pytorch 项目 网络参数指南
---



个人觉得成熟的rebole的工具 不太适合萌新 来操作自己对于项目的建设

最近找了一个新的项目 来操作 

感觉本项目 更贴近萌新到大佬写代码过程 于是乎 有了这篇指南

## embedding操作

### 0. 官方操作 

下面是官方例子

```python
import torch 
import torch.nn as nn

embedding = nn.embedding(10,3)
input = torch.LongTensor([[1,2,4,5],[4,3,2,9]])
embedding(input)

'''
tensor([[[-0.4057,  0.2994,  0.8345],
         [-0.9011,  0.2980, -0.8198],
         [-0.0016, -0.7422,  0.4830],
         [ 0.1380,  0.3021, -0.6233]],

        [[-0.0016, -0.7422,  0.4830],
         [-1.1394,  0.2577, -0.4709],
         [-0.9011,  0.2980, -0.8198],
         [-1.4681,  0.4755, -0.2688]]], grad_fn=<EmbeddingBackward0>)
'''
```

官方的解释：

 `torch.nn.``Embedding`(*num_embeddings*, *embedding_dim*, *padding_idx=None*, *max_norm=None*, *norm_type=2.0*, *scale_grad_by_freq=False*, *sparse=False*, *_weight=None*, *device=None*, *dtype=None*)



- num_embeddings：嵌入字典的大小（词的个数）；
- embedding_dim：每个嵌入向量的大小；
- padding_idx：若给定，则每遇到 padding_idx 时，位于 padding_idx 的嵌入向量（即 -padding_idx 映射所对应的向量）为0；
- max_norm：若给定，则每个大于 max_norm 的数都会被规范化为 max_norm；
- norm_type：为 max_norm 计算 p-范数的 p值；
- scale_grad_by_freq：若给定，则将按照 mini-batch 中 words 频率的倒数 scale gradients；
- sparse：若为 True，则 weight 矩阵将是稀疏张量。
  

### 1. 自己的瞎吉儿理解

这里呀 就只需要理解好 前三个就好

- 对于前两个的理解

torch.nn.Embedding 的**权重**为 num_embeddings * embedding_dim 的矩阵，例如输入10个词，每个词用3为向量表示，则权重为10*3的矩阵；

- 对于 padding_idx 理解

```python
import torch
from torch import nn
 
input = torch.LongTensor([[1, 2, 6, 4], [5, 6, 7, 8]])
em = nn.Embedding(10, 3, padding_idx=6)
output = em(input)
print("output:",output)
print("output.shape:",output.shape)
print("em.weight.shape:",em.weight.shape)
```

 ![](https://img-blog.csdnimg.cn/20200524013059410.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lpemhpc2h1aXhpb25n,size_16,color_FFFFFF,t_70) 

 可以看出 “6” 所对应映射的向量被填充了0。 

## 网络初始化
https://blog.csdn.net/dss_dssssd/article/details/83959474
### 1. 初始化函数

1. 均匀分布
torch.nn.init.uniform_(tensor, a=0, b=1)
服从~U ( a , b ) U(a, b)U(a,b)

2. 正太分布
torch.nn.init.normal_(tensor, mean=0, std=1)
服从~N ( m e a n , s t d ) N(mean, std)N(mean,std)

3. 初始化为常数
torch.nn.init.constant_(tensor, val)
初始化整个矩阵为常数val

4. Xavier

基本思想是通过网络层时，**输入和输出的方差相同**，包括前向传播和后向传播。具体看以下博文：

 ![](https://img-blog.csdnimg.cn/20181111153036310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rzc19kc3Nzc2Q=,size_16,color_FFFFFF,t_70) 

1. 为什么需要Xavier 初始化？
   如果初始化值很小，那么随着层数的传递，方差就会趋于0，此时输入值 也变得越来越小，在sigmoid上就是在0附近，接近于线性，失去了非线性
   如果初始值很大，那么随着层数的传递，方差会迅速增加，此时输入值变得很大，而sigmoid在大输入值写倒数趋近于0，反向传播时会遇到梯度消失的问题

2. xavier初始化的简单推导 

[![qsMJ91.png](https://s1.ax1x.com/2022/03/28/qsMJ91.png)](https://imgtu.com/i/qsMJ91)

5. kaiming (He initialization)

以后再说 现在没用上



## bug解决

感谢涛哥 对于源码的修改

### 错误信息

[![qyt5kV.png](https://s1.ax1x.com/2022/03/29/qyt5kV.png)](https://imgtu.com/i/qyt5kV)

在改错的时候注意看最后一行 即

[![qyNC1e.png](https://s1.ax1x.com/2022/03/29/qyNC1e.png)](https://imgtu.com/i/qyNC1e)

### 修改

在这里定位好 然后修改成：

```python
return self.cpu().numpy()
```

