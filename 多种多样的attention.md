---
title: transformer 详解！
---

### 0. 奠基大佬

 **Bahdanau Attention & Luong Attention** 

 ![](https://pic2.zhimg.com/80/v2-bda918b5bf14db4dc01da8b2b352c619_720w.jpg) 

### 1. 自注意力和多头

 **Self Attention & Multi-head Attention** 

为什么自注意力呢？

 相对于 RNN，考虑长距离依赖，还要可以并行！ 

- constant path length & variable-sized perceptive field ：任意两个位置（特指远距离）的关联不再需要通过 Hierarchical perceptive field 的方式，它的 perceptive field 是整个句子，所以任意两个位置建立关联是常数时间内的。
- parallelize : 没有了递归的限制，就像 CNN 一样可以在每一层内实现并行。

 ![](https://pic4.zhimg.com/80/v2-c09efe994bffa64fe9ab854eb6c97d4f_720w.jpg) 

#### 1.1 莫斯是 自注意力

\1. QKV 都是对输入 x 的线性映射。

\2. score-function 使用 scaled-dot product。

\3. multihead 的方式将多个 head 的输出 z，进行 concat 后，通过线性变换得到最后的输出 z

 ![](https://pic3.zhimg.com/80/v2-00e535ee13e7a3e14651d3269d5fd2a2_720w.jpg) 