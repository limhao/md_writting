---
title: saint+ saint的时间正确使用版本
---

## 说些废话

前面不是说过 

saint的 变种 因为时间属性没有使用正确 

导致 其效果甚至都不如原来的本体 

这个估计就是时间正确的适用版本

这篇文章 

1. transformer 下 时间属性怎么使用
2. 人家的改进方法

## 前言

论文名：

链接：

1. 论文链接 https://arxiv.org/abs/2010.12042

2. 代码链接 https://github.com/Shivanandmn/SAINT_plus-Knowledge-Tracing

3. 文章介绍链接：https://zhuanlan.zhihu.com/p/458896555（知乎）

4. 已经运行的环境：

   https://www.kaggle.com/code/shivanandmn/saint-training-using-pytorch-success-run/notebook

   https://www.kaggle.com/code/m10515009/saint-is-all-you-need-training-private-0-801/notebook


相关知识：

1. transformer
2. 知识追踪
3. saint

## 1. 相关背景

saint没有很好的将时间参数引入 embedding 中

### 1.1 核心思想

其中的时间分为两种

[![](https://s1.ax1x.com/2022/08/01/vkhyY8.md.png)](https://imgtu.com/i/vkhyY8)

- elapsed time

  做题的时候所花费的时间

   - 提出了两种编码方式
   1. 连续嵌入 （公式）
      [![](https://s1.ax1x.com/2022/08/01/vkh2lQ.png)](https://imgtu.com/i/vkh2lQ)  et 是一个时间（int） w 是一个可以学习的参数
   2. 范畴嵌入
      为每个整数秒分配唯一的潜在向量。我们将最大经过时间设置为300秒，超过该时间的任何时间都限制为300秒。
  
- lag time

  滞后时间是交互之间的时间间隔，是影响学生学习过程中出现的复杂现象的重要因素。例如，随着时间的推移，学生往往会忘记所学的内容 

   1. 连续嵌入 （公式）
      [![](https://s1.ax1x.com/2022/08/01/vkhgSg.png)](https://imgtu.com/i/vkhgSg)  it 是一个时间（int） w 是一个可以学习的参数

   2. 范畴嵌入
      其细粒度为分钟 

      0, 1, 2, 3, 4, 5, 10, 20, 30, . . . , 1440.从结果上面来看 一共分为了150类 （看看人家的embedding）

## 2. 实证分析

时间分布 一样存在长尾问题（划分的理由）细粒度划分的理由

[![](https://s1.ax1x.com/2022/08/01/vkh6fS.md.png)](https://imgtu.com/i/vkh6fS)

## 3. 问题描述

各一个序列 题目 加 响应 然后回答序列的最后一个答案

## 4. 方法（基本为论文方法部分）

[![](https://s1.ax1x.com/2022/08/01/vkhWOs.md.png)](https://imgtu.com/i/vkhWOs)

论文 的 整体方法 大致 和 saint 差不多 具体可以看看实验是怎么run出来的

时间种类的划分 取得最好的效果 （这里的时间 是放在 解码器之中）

[![](https://s1.ax1x.com/2022/08/01/vkhRyj.png)](https://imgtu.com/i/vkhRyj)

对于时间的消融实验

[![](https://s1.ax1x.com/2022/08/01/vkhhmn.png)](https://imgtu.com/i/vkhhmn)

对于添加时间特征在哪里好的实验

[![](https://s1.ax1x.com/2022/08/01/vkh4wq.png)](https://imgtu.com/i/vkh4wq)

## 5. 总结

此外，通过将时间特征合并到解码器输入中获得了最佳结果，验证了分别处理练习信息和学生反应信息适合于知识跟踪的假设。未来工作的途径包括1）不仅对学生的问题解决记录进行建模，而且对各种学习活动进行建模，例如观看讲座和学习每个练习的解释；2）探索知识跟踪模型的体系结构，而不是分别处理练习信息和学生反应信息的基于转换器的编码器-解码器模型。 

## 6. 自己的想法

1. 对方的消融实验做得十分不错
2. 对于时间的划分 可以通过 观察时间的分布 来确定离散量的分布
3. 对于 lag time 的 使用 是不是 显得有点草率 看看 能不能像lpkt一样用出来