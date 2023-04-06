---
title: caser环境使用方法
---

## 文章开始

```python
# conda 切换环境
conda activate caser
# 开始训练
python train_caser.py 
# 大家看看结果哇
```

# 怎么切换数据集

文章中提供了四个数据集

但是 我这边找了好半天找到了两个（ml1m gowalla）这两个

然后我按照数据结构 自己整了个

Foursquare 这个数据集、

这个数据集 是不能对应原论文中的效果的



针对于数据集切换 

![](https://pic.imgdb.cn/item/642d82f0a682492fcc9daa4d.png)

train_root 

test_toot 

里面修改下就好了

# 我自己怎么整的数据集呢

看看文件夹 my_work

俺自己整的方法 这个里面

