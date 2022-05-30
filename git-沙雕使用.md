---
title: git 沙雕使用
---

我作为个人开发者，对于git的使用了解个大概就好

首先 先拉一张图

 ![](https://img-blog.csdnimg.cn/20191016140534955.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doYXRfd2hlcmU=,size_16,color_FFFFFF,t_70) 

在git的分级中存在

这样的工作目录

[![LHh9yt.png](https://s1.ax1x.com/2022/04/26/LHh9yt.png)](https://imgtu.com/i/LHh9yt)

这个暂存区 言如其名

我也不晓得是啥

本地仓库 就是文件发送的最后一个位置

远程仓库 对于 我们来说就是github了



### 1. 本人可能进行的操作

```python
# 从远程仓库拉文件
git clone 仓库地址
# 工作地址文件提交到暂存区
git add .
# 暂存区文件提交到本地仓库
git commit -m "你要备注的消息"
# 提交代码并注释
git commit -am "你要备注的消息"
# 上传代码
git push
```

够了！