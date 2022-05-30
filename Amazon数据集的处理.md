---
title: Amazon数据集的处理 生动（没写完）
---

http://jmcauley.ucsd.edu/data/amazon/

http://jmcauley.ucsd.edu/data/amazon/

数据集：http://jmcauley.ucsd.edu/data/amazon/ 一个数据集3g 有点大

这个数据集按 作者的说法是处理好了的 

先于之前一篇文章的写法，这次打算按照之前criteo数据集的编写方法 在写一个

算是数据集的第二篇 一共三篇

## 0. 观察数据（有一点重要）

[![Lp88pj.md.png](https://s1.ax1x.com/2022/04/08/Lp88pj.md.png)](https://imgtu.com/i/Lp88pj)

类别有 label user itemID cateID 

重要的是 后面的 hist_item_list 和 hist_cate_list 这两个列表

可以看到这两个列表使用了 | 作为分隔符 对于后两个列表的处理 

将是比较重要的特点

可以观察到的数值特征 只有标签label一个

## 1. 读取数据

```python

```



