---
title: tf安装指坑
---

## 0. 前言

本次项目是在我中了项目书重新回顾了PEBG的代码准备尝试给他跑起来

本次差不多我认真的时间应该是花了4-5天的样子

在这次坑里面 我花费了30大洋

其中20找了个人帮我看了看代码（是真的只是看了看然后给俺发了个requirement.txt）

然后我又花费了10 去找了个国内的GPT去帮我看了看代码

国内GPT真垃圾！！！

参考的文献和链接：

https://github.com/ApexEDM/PEBG 本次项目

https://blog.csdn.net/qq_41793080/article/details/104546053 tf环境安装

https://blog.csdn.net/xzq_13972146251/article/details/125810255      这个链接基本讲明白了1.x是怎么安装

https://cn.dll-files.com/     在tf运行的过程中会出现DLL文件确实 可以在这里找

https://blog.csdn.net/ahahayaa/article/details/127996657 dll 文件安装方法 conda环境下

https://blog.csdn.net/a1456123a/article/details/115097143 dll放的位置

## 1. 坑

- tf1.x 和 tf2的坑

 目前的google在推tf2 

在google根本找不到tf1.x 的安装方式

然后作者的readme 也没写个合适的requirement

- 项目作者的不维护 issue 不像我上个项目维护的那么好

![](https://pic.imgdb.cn/item/63b4ffc1be43e0d30e9346d3.png)

去github去看看 一个项目 作者要是不答疑 其实这个代码其实很难跑的

- tf GPU 和 CPU

建议在尝试了很多很多遍GPU的同学 

试着用用CPU去跑跑 很有可能会一次跑通

不得不说 tf1.12 这个CPU的库 跑通了 我很开心！！！

![](https://pic.imgdb.cn/item/63b4ffc1be43e0d30e9346ed.png)

基本尝试 2.5 GPU CPU

1.5 GPU CPU

1.2 GPU CPU

- .dll 文件缺失

放在这个地址下

C:\Users\<user>\Anaconda3\envs\<env name>\Library\bin

C:\Windows\System32 这里 我是放这里成功的