---
title: argparse操作
---

## 0. 前言

对于 argparse 这个命令行小助手

其 对于深度网络的开发具有相当重要的作用 

一开始 对于开发者来说 对于其描述 add_argument 定义 一眼就能看出来 这个东西需要啥

要给啥 

其使用 具体三个步骤

1. 实例化 ArgumentParser
2. 使用add_argument函数添加参数

3. 使用parse_args 解析参数

## 1. 实例化ArgumentParser（挺固定的）

```python
# 导入关键包
import argparse
# 创建一个 ArgumentParser 对象
parser = argparse.ArgumentParser()
```

## 2. 添加参数

举一下本人的例子

```python
parser.add_argument('--train_root', type=str, default='datasets/ml1m/test/train.txt')
parser.add_argument('--test_root', type=str, default='datasets/ml1m/test/test.txt')
parser.add_argument('--L', type=int, default=5)
parser.add_argument('--T', type=int, default=2)
# train arguments
parser.add_argument('--n_iter', type=int, default=50)
parser.add_argument('--seed', type=int, default=1234)
parser.add_argument('--batch_size', type=int, default=512)
parser.add_argument('--learning_rate', type=float, default=1e-3)
parser.add_argument('--l2', type=float, default=1e-6)
parser.add_argument('--neg_samples', type=int, default=3)
parser.add_argument('--use_cuda', type=str, default=True)
```

在add_argument 中间有三个参数

参数一： 看作变量名 前面得加 --

参数二： 变量数据类型

参数三： 缺省值（default）

目前 俺觉得俺能学这三个参数就好了

## 3. 解析参数

最后会被解析成

 ```python
args = parser.parse_args()
# 在ipython中 这个操作得是这个样子
args = parser.parse_args(args=[])
 ```

## 神奇操作

[![](https://s1.ax1x.com/2022/05/03/OAMjln.md.png)](https://imgtu.com/i/OAMjln)

这里存在 我叫做变量的继承 挺有意思的

[![](https://s1.ax1x.com/2022/05/03/OAlCut.md.png)](https://imgtu.com/i/OAlCut)

被封装成namespace对象 不能在使用add_argument 函数对其 变量进行添加