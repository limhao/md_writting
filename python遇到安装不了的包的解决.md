---
title: python遇到安装不了的包的解决whl包的安装
---

感谢：https://blog.csdn.net/xyh_qianxiao/article/details/114591037 操作流程

https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml 包编译好的文件

2022/6/22

今天试了试 paddle ocr 包 说实话 安装的的过程有点慢吞吞

于是乎出现了这个样子的错误

[![jpseOS.md.png](https://s1.ax1x.com/2022/06/22/jpseOS.md.png)](https://imgtu.com/i/jpseOS)

错误的大致描述是 c 没有编译好这个包

嗯！

## 1. 破局

假如 你的python环境是 3.9

你就在这个网站https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml

找人家编好的文件

 然后下载完成后按照：文件位置+文件名的格式，直接pip install，例如我的安装的是： 

```python
pip install F:\software\python_Levenshtein-0.12.0-cp35-cp35m-win_amd64.whl
```

然后 这个包就转好了！

## 2. 感悟

1. 有问题 多看看论坛
2. github不好使的时候，用用gitee
3. pytorch不好使的时候 用用paddle