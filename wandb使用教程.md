---
title: wandb 项目修改教程
---

## 0. 前言

距离上次发博客 是4/6号

现在 过了快两周 

差不多是我这篇新的论文 验证完思路 效果好 且 跟师兄讨论的一个时间点

wandb 来找找超参 之前学过一些



链接：

https://docs.wandb.ai/guides/sweeps/add-w-and-b-to-your-code

https://docs.wandb.ai/guides/sweeps/existing-project



自己的问题是 在一个已经能跑好的模型 添加wandb sweep 来 搜索超参

这个跑好的模型存在Namespace 命名空间 

来看看 代码的修改

## 1. 来看看官方要我们加什么

```python
import wandb
import numpy as np 
import random

# Define sweep config
sweep_configuration = {
    'method': 'random',
    'name': 'sweep',
    'metric': {'goal': 'maximize', 'name': 'val_acc'},
    'parameters': 
    {
        'batch_size': {'values': [16, 32, 64]},
        'epochs': {'values': [5, 10, 15]},
        'lr': {'max': 0.1, 'min': 0.0001}
     }
}

# Initialize sweep by passing in config. 
# (Optional) Provide a name of the project.
sweep_id = wandb.sweep(
  sweep=sweep_configuration, 
  project='my-first-sweep'
  )

# Define training function that takes in hyperparameter 
# values from `wandb.config` and uses them to train a 
# model and return metric
def train_one_epoch(epoch, lr, bs): 
  acc = 0.25 + ((epoch/30) +  (random.random()/10))
  loss = 0.2 + (1 - ((epoch-1)/10 +  random.random()/5))
  return acc, loss

def evaluate_one_epoch(epoch): 
  acc = 0.1 + ((epoch/20) +  (random.random()/10))
  loss = 0.25 + (1 - ((epoch-1)/10 +  random.random()/6))
  return acc, loss

def main():
    run = wandb.init()

    # note that we define values from `wandb.config`  
    # instead of defining hard values
    lr  =  wandb.config.lr
    bs = wandb.config.batch_size
    epochs = wandb.config.epochs

    for epoch in np.arange(1, epochs):
      train_acc, train_loss = train_one_epoch(epoch, lr, bs)
      val_acc, val_loss = evaluate_one_epoch(epoch)

      wandb.log({
        'epoch': epoch, 
        'train_acc': train_acc,
        'train_loss': train_loss, 
        'val_acc': val_acc, 
        'val_loss': val_loss
      })

# Start sweep job.
wandb.agent(sweep_id, function=main, count=4)
```

需要添加什么？

1. sweep config
2. sweep id
3. 在main函数里面init() 好 且给超参赋值 log好我们需要记录的值
4. 开始工作 wandb.agent(sweep_id, function=main, count=4)

## 2. 原样

我的train文件 有 

一个类 一个方法 一个main 入口

其中的main 函数里面有 Namespace 命名空间

![](https://pic1.imgdb.cn/item/6441f00d0d2dde5777b20b1c.png)

其实 一开始 是打算将 参数搜索的字典放在main入口里面的

但是 我 找了很多方法 就是config打印不出来结果 

![](https://pic1.imgdb.cn/item/6441f00e0d2dde5777b20bab.png)

## 3. 解法

看了看韩同学的代码 有可能是 pykt的代码

很牛 

先交代好参数

![](https://pic1.imgdb.cn/item/6441f0f20d2dde5777b316c3.png)

将main入口 写成一个函数 def main():

![](https://pic1.imgdb.cn/item/6441f0f20d2dde5777b3170c.png)

参数的传递 通过修改 parser 中default来修改

![](https://pic1.imgdb.cn/item/6441f00e0d2dde5777b20bda.png)

设置好wandb 需要log的值

![](https://pic1.imgdb.cn/item/6441f0f20d2dde5777b316ec.png)yo

然后将 将执行函数 写入 wandb.agent(sweep_id, function=main, count=4)

![](https://pic1.imgdb.cn/item/6441f00e0d2dde5777b20bfc.png)

## 4. 总结

一共四步

1. 定义config
2.  将之前的main调用改成 main方法
3. 修改好命名空间内的default中的值
4. 设置好wandb需要log的值
5. 在main调用内 写wandb.agent

最后 

用nohup保护进程

```python
nohup python -u main.py > log.out 2>&1 &
```

