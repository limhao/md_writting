---
title: zhihurec 推荐数据集
---

## 说些废话

推荐数据集 跟知识分享相关的 

这个数据集是 知乎10天内收集约100万次交互 798k用户 165k问题 等关键字组成

一般用于 top-N推荐、顺序推荐和上下文感知推荐 

除推荐以外

用户性别预测、最有价值的回答者识别和高质量的答案识别 

目前的 缺乏推荐系统用户交互日志

## 前言

论文名：A Large-Scale Rich Context Query and Recommendation
Dataset in Online Knowledge-Sharing

链接：

1. 论文链接 https://arxiv.org/abs/2106.06467
2. 代码链接 https://github.com/THUIR/ZhihuRec-Dataset
3. 文章介绍链接：（知乎）https://cloud.tencent.com/developer/news/841295

相关知识：

## 数据集探究

[![](https://s1.ax1x.com/2022/09/14/vvX081.png)](https://imgse.com/i/vvX081)

数据集 按静动态划分

静态 评分 收藏夹

动态 点击（积极） 印象（消极）

印象 是 我最难理解的一个量

**现在理解了一丢丢 好像就是 目录中 没有点击的问题对**

其数据集分为

印象

查询

用户配置文件

答案

问题

作者配置文件

## 数据集种类

[![](https://s1.ax1x.com/2022/09/14/vvXwCR.md.png)](https://imgse.com/i/vvXwCR)

100M 20M 1M 三种

## 能做的任务

### topN，序列推荐，上下文感知，

### 最有价值的回答，高质量回答识别

