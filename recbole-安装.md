---
title: recbole 安装
---

最近发现安装方法挺难受的 官网有些的错

时间 2023/3/24

记一下 代码过程吧

## anaconda 创建recbole环境

```python
# 创建环境 这里注意是3.9 版本这里有很大问题 一定是3.9
conda create -n recbole python=3.9
# cd 到recbole 文件夹
git clone https://github.com/RUCAIBox/RecBole.git && cd RecBole
# 从源文件安装 -i是走的国内源
pip install -e . --verbose -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## 自己遇到的bug

其实还缺两个库没装

```python
# conda 到你创建的环境 
# ray
pip install ray -i https://pypi.tuna.tsinghua.edu.cn/simple
# texttable
pip install textable -i https://pypi.tuna.tsinghua.edu.cn/simple
```

![](https://pic.imgdb.cn/item/641dab86a682492fcc21385f.png)

