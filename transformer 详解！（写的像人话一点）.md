---
title: transformer 详解！（写的像人话一点）
---

## 0. 奠基大佬

 **Bahdanau Attention & Luong Attention** 

 ![](https://pic2.zhimg.com/80/v2-bda918b5bf14db4dc01da8b2b352c619_720w.jpg) 

## 1. 自注意力和多头

 **Self Attention & Multi-head Attention** 

为什么自注意力呢？

 相对于 RNN，考虑长距离依赖，还要可以并行！ 

- constant path length & variable-sized perceptive field：任意两个位置（特指远距离）的关联不再需要通过 Hierarchical perceptive field 的方式，它的 perceptive field 是整个句子，所以任意两个位置建立关联是常数时间内的。
- parallelize : 没有了递归的限制，就像 CNN 一样可以在每一层内实现并行。

 ![](https://pic4.zhimg.com/80/v2-c09efe994bffa64fe9ab854eb6c97d4f_720w.jpg) 

### 1.1 宏观角度看自注意力机制

​	随着模型处理输入序列的每个单词，自注意力会关注整个输入序列的所有单词，帮助模型对本单词更好地进行编码。

​	如果你熟悉RNN（循环神经网络），回忆一下它是如何维持隐藏层的。RNN会将它已经处理过的前面的所有单词/向量的表示与它正在处理的当前单词/向量结合起来。而自注意力机制会将所有相关单词的理解融入到我们正在处理的单词中。

### 1.2 微观角度

在NLP 中 ，k = v

具体流程（本人的脑子思考）：

1. 词向量（为x1）

2. 生成qkv（词向量乘对应的权重矩阵 wq wk wv）

3. qk计算注意力分数（scaled-dot product）

4. 注意力分数softmax （alignment function）

5. 通过注意力分数softmax 计算最后的值向量

6. 求和 （context vector）

    ![](https://n.sinaimg.cn/sinacn20116/669/w746h723/20190108/ad95-hrkkwef7015564.jpg) 

### 1.3 代码实践

softmax 的 dim  = 1 

  ```python
import torch
import torch.nn as nn
# 各一个原始的base 词向量 v 就是值
base = torch.randn(2, 2)
v = torch.randn(2, 3)
print(base)
print(v)

# softmax （alignment function）
softmax_0 = nn.Softmax(dim=1)
out = softmax_0(base)
out

# 输出 求和项
# 矩阵乘法
out_add = torch.mm(out, v)
out_add
  ```



## 2. 注意力简单解释

https://blog.csdn.net/longxinchen_ml/article/details/86533005

自己发现 softmax dim 范围其实是dim = 1