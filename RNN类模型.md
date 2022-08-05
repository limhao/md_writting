---
title: RNN模型
---

感谢：https://zhuanlan.zhihu.com/p/32085405

 文章 写的怪好的 但是 在博客的体现并不怎么好看 如果想看 去看md文档

## 0. RNN

先简单介绍一下一般的RNN。 

 ![](https://pic4.zhimg.com/80/v2-f716c816d46792b867a6815c278f11cb_720w.jpg) 

这里：

x为当前状态下数据的输入， h表示接收到的上一个节点的输入。

y为当前节点状态下的输出，而**h'**为传递到下一个节点的输出。



通过上图的公式可以看到，输出 **h'** 与 **x** 和 **h** 的值都相关。

而 **y** 则常常使用 **h'** 投入到一个线性层（主要是进行维度映射）然后使用softmax进行分类得到需要的数据。

对这里的**y**如何通过 **h'** 计算得到往往看具体模型的使用方式。



通过序列形式的输入，我们能够得到如下形式的RNN。

 ![](https://pic2.zhimg.com/80/v2-71652d6a1eee9def631c18ea5e3c7605_720w.jpg) 

## 1. LSTM 

 长短期记忆（Long short-term memory, LSTM）是一种特殊的RNN，主要是为了解决长序列训练过程中的梯度消失和梯度爆炸问题。简单来说，就是相比普通的RNN，LSTM能够在更长的序列中有更好的表现。 

 ![](https://pic4.zhimg.com/80/v2-e4f9851cad426dfe4ab1c76209546827_720w.jpg) 

相比RNN只有一个传递状态 ![](https://www.zhihu.com/equation?tex=h%5Et+) ，LSTM有两个传输状态，一个 ![](https://www.zhihu.com/equation?tex=c%5Et) （cell state），和一个 ![](https://www.zhihu.com/equation?tex=h%5Et) （hidden state）。（Tips：RNN中的 ![](https://www.zhihu.com/equation?tex=h%5Et) 对于LSTM中的 ![](https://www.zhihu.com/equation?tex=c%5Et) ）

其中对于传递下去的 ![](https://www.zhihu.com/equation?tex=c%5Et) 改变得很慢，通常输出的 ![](https://www.zhihu.com/equation?tex=c%5Et) 是上一个状态传过来的 ![](https://www.zhihu.com/equation?tex=c%5E%7Bt-1%7D) 加上一些数值。

而 ![](https://www.zhihu.com/equation?tex=h%5Et) 则在不同节点下往往会有很大的区别。

### 1.1 LSTM 结构深入

下面具体对LSTM的内部结构来进行剖析。

首先使用LSTM的当前输入 ![](https://www.zhihu.com/equation?tex=x%5Et) 和上一个状态传递下来的 ![](https://www.zhihu.com/equation?tex=h%5E%7Bt-1%7D) 拼接训练得到四个状态。

 ![](https://pic4.zhimg.com/80/v2-15c5eb554f843ec492579c6d87e1497b_720w.jpg) 

 ![img](https://pic1.zhimg.com/80/v2-d044fd0087e1df5d2a1089b441db9970_720w.jpg) 

 其中， ![](https://www.zhihu.com/equation?tex=z%5Ef+) ， ![](https://www.zhihu.com/equation?tex=z%5Ei) ，![](https://www.zhihu.com/equation?tex=z%5Eo) 是由拼接向量乘以权重矩阵之后，再通过一个 ![](https://www.zhihu.com/equation?tex=sigmoid+) 激活函数转换成0到1之间的数值，来作为一种门控状态。而 ![](https://www.zhihu.com/equation?tex=z) 则是将结果通过一个 ![](https://www.zhihu.com/equation?tex=tanh) 激活函数将转换成-1到1之间的值（这里使用 ![](https://www.zhihu.com/equation?tex=tanh) 是因为这里是将其做为输入数据，而不是门控信号） 

 **下面开始进一步介绍这四个状态在LSTM内部的使用。（敲黑板）** 

 ![](https://pic2.zhimg.com/v2-556c74f0e025a47fea05dc0f76ea775d_b.jpg) 

其经典图片是这样的

 ![](https://pic4.zhimg.com/v2-561a4eeb23e5bdd3a0bb08d818511aa3_b.jpg) 

### 1.2 主要的阶段

LSTM内部主要有三个阶段：

1. 忘记阶段。这个阶段主要是对上一个节点传进来的输入进行**选择性**忘记。简单来说就是会 “忘记不重要的，记住重要的”。

具体来说是通过计算得到的 ![](https://www.zhihu.com/equation?tex=z%5Ef) （f表示forget）来作为忘记门控，来控制上一个状态的 ![](https://www.zhihu.com/equation?tex=c%5E%7Bt-1%7D) 哪些需要留哪些需要忘。

2. 选择记忆阶段。这个阶段将这个阶段的输入有选择性地进行“记忆”。主要是会对输入 ![](https://www.zhihu.com/equation?tex=x%5Et) 进行选择记忆。哪些重要则着重记录下来，哪些不重要，则少记一些。当前的输入内容由前面计算得到的 ![](https://www.zhihu.com/equation?tex=z+) 表示。而选择的门控信号则是由 ![](https://www.zhihu.com/equation?tex=z%5Ei) （i代表information）来进行控制。

> 将上面两步得到的结果相加，即可得到传输给下一个状态的 ![](https://www.zhihu.com/equation?tex=c%5Et) 。也就是上图中的第一个公式。

3. 输出阶段。这个阶段将决定哪些将会被当成当前状态的输出。主要是通过 ![](https://www.zhihu.com/equation?tex=z%5Eo) 来进行控制的。并且还对上一阶段得到的 ![](https://www.zhihu.com/equation?tex=c%5Eo) 进行了放缩（通过一个tanh激活函数进行变化）。

与普通RNN类似，输出 ![](https://www.zhihu.com/equation?tex=y%5Et) 往往最终也是通过 ![](https://www.zhihu.com/equation?tex=h%5Et) 变化得到。

### 1.3 总结

以上，就是LSTM的内部结构。通过门控状态来控制传输状态，记住需要长时间记忆的，忘记不重要的信息；而不像普通的RNN那样只能够“呆萌”地仅有一种记忆叠加方式。对很多需要“长期记忆”的任务来说，尤其好用。

但也因为引入了很多内容，导致参数变多，也使得训练难度加大了很多。因此很多时候我们往往会使用效果和LSTM相当但参数更少的GRU来构建大训练量的模型。

## 2. GRU

 GRU（Gate Recurrent Unit）是循环神经网络（Recurrent Neural Network, RNN）的一种。和LSTM（Long-Short Term Memory）一样，也是为了解决长期记忆和反向传播中的梯度等问题而提出来的 。

其性能与LSTM相当 ，但是 其计算的消耗较前者少。

 ![preview](https://pic4.zhimg.com/v2-a8424cd80eae1b7d312991692decbe8b_r.jpg) 

### 2.1 输入输出

GRU的输入输出结构与普通的RNN是一样的。

有一个当前的输入 ![](https://www.zhihu.com/equation?tex=x%5Et) ，和上一个节点传递下来的隐状态（hidden state） ![](https://www.zhihu.com/equation?tex=h%5E%7Bt-1%7D) ，这个隐状态包含了之前节点的相关信息。

结合 ![](https://www.zhihu.com/equation?tex=x%5Et+) 和 ![](https://www.zhihu.com/equation?tex=h%5E%7Bt-1%7D)，GRU会得到当前隐藏节点的输出 ![](https://www.zhihu.com/equation?tex=y%5Et+) 和传递给下一个节点的隐状态 ![](https://www.zhihu.com/equation?tex=h%5Et) 。

 ![](https://pic2.zhimg.com/80/v2-49244046a83e30ef2383b94644bf0f31_720w.jpg) 

### 2.2 内部结构

 通过上一个传输下来的状态 ![](https://www.zhihu.com/equation?tex=h%5E%7Bt-1%7D) 和当前节点的输入 ![](https://www.zhihu.com/equation?tex=x%5Et) 来获取两个门控状态。 其中 ![](https://www.zhihu.com/equation?tex=r+) 控制重置的门控（reset gate）， ![](https://www.zhihu.com/equation?tex=z) 为控制更新的门控（update gate）。 

 ![](https://pic3.zhimg.com/80/v2-7fff5d817530dada1b279c7279d73b8a_720w.jpg) 

 得到门控信号之后，首先使用重置门控来得到**“重置”**之后的数据 ![](https://www.zhihu.com/equation?tex=%7Bh%5E%7Bt-1%7D%7D%27+%3D+h%5E%7Bt-1%7D+%5Codot+r+) ，再将 ![](https://www.zhihu.com/equation?tex=%7Bh%5E%7Bt-1%7D%7D%27) 与输入 ![](https://www.zhihu.com/equation?tex=x%5Et+) 进行拼接，再通过一个[tanh](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/tanh)激活函数来将数据放缩到**-1~1**的范围内。即得到如下图2-3所示的 ![](https://www.zhihu.com/equation?tex=h%27) 。 

 ![](https://pic4.zhimg.com/80/v2-390781506bbebbef799f1a12acd7865b_720w.jpg) 

 这里的 ![](https://www.zhihu.com/equation?tex=h%27+) 主要是包含了当前输入的 ![](https://www.zhihu.com/equation?tex=x%5Et) 数据。有针对性地对 ![](https://www.zhihu.com/equation?tex=h%27) 添加到当前的隐藏状态，相当于”记忆了当前时刻的状态“。 

 ![](https://pic3.zhimg.com/80/v2-5b805241ab36e126c4b06b903f148ffa_720w.jpg) 

最后介绍GRU最关键的一个步骤，我们可以称之为**”更新记忆“**阶段。

在这个阶段，我们同时进行了遗忘了记忆两个步骤。我们使用了先前得到的更新门控 ![](https://www.zhihu.com/equation?tex=z) （update gate）。

**更新表达式**： ![](https://www.zhihu.com/equation?tex=h%5Et+%3D+%281-z%29+%5Codot+h%5E%7Bt-1%7D+%2B+z%5Codot+h%27)

首先再次强调一下，门控信号（这里的 ![](https://www.zhihu.com/equation?tex=z) ）的范围为0~1。门控信号越接近1，代表”记忆“下来的数据越多；而越接近0则代表”遗忘“的越多。

> 有读者发现在pytorch里面的GRU[[链接](https://link.zhihu.com/?target=https%3A//pytorch.org/docs/stable/nn.html%3Fhighlight%3Dgru%23torch.nn.GRU)]写法相比原版对 ![](https://www.zhihu.com/equation?tex=h%5E%7Bt-1%7D) 多了一个映射，相当于一个GRU变体，猜测是多加多这个映射能让整体实验效果提升较大。如果有了解的同学欢迎评论指出。

GRU很聪明的一点就在于，**我们使用了同一个门控 ![](https://www.zhihu.com/equation?tex=z) 就同时可以进行遗忘和选择记忆（LSTM则要使用多个门控）**。

- ![](https://www.zhihu.com/equation?tex=%281-z%29+%5Codot+h%5E%7Bt-1%7D) ：表示对原本隐藏状态的选择性“遗忘”。这里的 ![](https://www.zhihu.com/equation?tex=1-z) 可以想象成遗忘门（forget gate），忘记 ![](https://www.zhihu.com/equation?tex=h%5E%7Bt-1%7D) 维度中一些不重要的信息。
- ![](https://www.zhihu.com/equation?tex=z+%5Codot+h%27) ： 表示对包含当前节点信息的 ![](https://www.zhihu.com/equation?tex=h%27) 进行选择性”记忆“。与上面类似，这里的 ![](https://www.zhihu.com/equation?tex=%281-z%29) 同理会忘记 ![](https://www.zhihu.com/equation?tex=h+%27) 维度中的一些不重要的信息。或者，这里我们更应当看做是对 ![](https://www.zhihu.com/equation?tex=h%27+) 维度中的某些信息进行选择。
- ![](https://www.zhihu.com/equation?tex=h%5Et+%3D%281-+z%29+%5Codot+h%5E%7Bt-1%7D+%2B+z%5Codot+h%27) ：结合上述，这一步的操作就是忘记传递下来的 ![](https://www.zhihu.com/equation?tex=h%5E%7Bt-1%7D+) 中的某些维度信息，并加入当前节点输入的某些维度信息。

> 可以看到，这里的遗忘 ![](https://www.zhihu.com/equation?tex=z) 和选择 ![](https://www.zhihu.com/equation?tex=%281-z%29) 是联动的。也就是说，对于传递进来的维度信息，我们会进行选择性遗忘，则遗忘了多少权重 （![](https://www.zhihu.com/equation?tex=z) ），我们就会使用包含当前输入的 ![](https://www.zhihu.com/equation?tex=h%27) 中所对应的权重进行弥补 ![](https://www.zhihu.com/equation?tex=%281-z%29) 。以保持一种”恒定“状态。

### 2.3 总结

GRU输入输出的结构与普通的RNN相似，其中的内部思想与LSTM相似。

与LSTM相比，GRU内部少了一个”门控“，参数比LSTM少，但是却也能够达到与LSTM相当的功能。考虑到硬件的**计算能力**和**时间成本**，因而很多时候我们也就会选择更加”实用“的GRU啦。