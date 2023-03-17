---
title: t-sne指南
---

### 前言

2-27 3-17 这个星期三发的文章给老师看

![](https://pic.imgdb.cn/item/64146641a682492fcc29fc03.png)

距离上次发文章快20天了

差不多这20天 自己重新把文章又写了一遍

主要是发现 

文章不能写太久了 写太久的话 文章会忘掉好多东西 

在 写完这篇之后 随便整理个文章框架出来 很不错

### 为什么会用到这个东西

这篇论文是一个多任务相关的推荐

采取的交叉压缩单元 通过交叉压缩两个向量 来保证两个特征之间的相关性 

即 为了考虑到相关性 我考虑是不是可以使用t-sne来展现特征两者的相关性

于是乎 就出现了这么个我学习的东西



### 学习到了什么？

参考 链接

https://blog.csdn.net/tszupup/article/details/84997804

1. 模型的权重文件 在模型save的时候 要注意下面这个

![](https://pic.imgdb.cn/item/64146640a682492fcc29fb91.png)

注意不要是save_dict() 否者模型无法验证

2. 看了看aitm 里面 出现了 sample正负样本的方法 在本次使用的是下面这个

```python
data.info()
minidata = data.sample(5)

minidata
minidata = minidata.drop(columns=['search_id'])
minidata.to_csv('tsne.csv')
```

sample 函数真好用 但是 下次看下 是不是 sample函数是不是可以给些限制条件 来保证sample出的正负样本

3. sklearn 中 tsne 函数奇奇怪怪的点

```error
ValueError: perplexity must be less than n_samples
```

![](https://pic.imgdb.cn/item/64146641a682492fcc29fc4d.png)

说实话 真的有人看的懂这个东西？

我当时还问了chatgpt

sklearn 的中文化教程是真的差 csdn也没找到

其中 得到的解答是  perplexity 这个超参数的值 大于了 n_samples 的值

soga 立马 改了 

```python
# 改之前
tsne = TSNE(n_components=2, init='pca', random_state=0)
# 改之后 默认的值好像是50来着
tsne = TSNE(n_components=2, init='pca', random_state=0, perlexity=5)
```

但是 结果是真的奇怪 或者 t-sne 不太合适去展现中间的过程吧 去找找

![](https://pic.imgdb.cn/item/64146641a682492fcc29fca1.png)

4. 下一步的思考

找找什么方法可以较好的展现中间结果

试一试三维的t-sne 估计又得学一会

我对于模型的权重文件的理解 

应该是将四个结果展现出来 我画个图！ 最后使用三维图将结果弄出来

![](https://pic.imgdb.cn/item/64146e43a682492fcc464b87.png)

但我感觉 我想了想内积 来保证权重的相似性

多想想

### 代码整个流程

```python
# 模型导入
import torch
model = torch.load('t-sne模型文件/AliExpress_US_sharedbottom.pt')
model.eval()
# 数据导入
from datasets.aliexpress import AliExpressDataset
train_dataset = AliExpressDataset('tsne.csv')
# 数据加载器导入
from torch.utils.data import DataLoader
import random 
import numpy as np
seed = 2022
random.seed(seed)
np.random.seed(seed)
torch.manual_seed(seed)
torch.cuda.manual_seed(seed)
torch.cuda.manual_seed_all(seed)
train_data_loader = DataLoader(train_dataset, batch_size=1, num_workers=4, shuffle=False)
# 下面的代码是 https://blog.csdn.net/tszupup/article/details/84997804 改的
# coding='utf-8'
"""t-SNE对手写数字进行可视化"""
from time import time
import numpy as np
import matplotlib.pyplot as plt
import tqdm
device = 'cuda:0'
from sklearn import datasets
from sklearn.manifold import TSNE
from sklearn.decomposition import PCA


def plot_embedding(data, label, title):
    x_min, x_max = np.min(data, 0), np.max(data, 0)
    data = (data - x_min) / (x_max - x_min)

    fig = plt.figure()
    ax = plt.subplot(111)
    colors = ['r', 'b']
    for i in range(data.shape[0]):
        plt.scatter(data[i, 0], data[i, 1], c=colors[label[i]], alpha=0.5)
    plt.xticks([])
    plt.yticks([])
    plt.title(title)
    return fig


def main():
    with torch.no_grad():
        data1 = []
        data2 = []
        for categorical_fields, numerical_fields, labels in tqdm.tqdm(train_data_loader, smoothing=0, mininterval=1.0):
            categorical_fields, numerical_fields, labels = categorical_fields.to(device), numerical_fields.to(device), labels.to(device)
            y, _ ,cross= model(categorical_fields, numerical_fields)
            data1 += cross[0].cpu().numpy().tolist()
            data2 += cross[1].cpu().numpy().tolist()
        # data = np.array(data1)
        data = np.concatenate((data1, data2), axis=0)
        label = len(data1)*[0] + len(data2)*[1]
        # label = len(data1)*[1]
        n_samples, n_features = data.shape
        print(data.shape)
    print('data.shape',data.shape) 
    # print(data)
    print('label',label)
    print('label中数字有',len(set(label)),'个不同的数字')
    print('data有',n_samples,'个样本')
    print('每个样本',n_features,'维数据')
    print('Computing t-SNE embedding')
    tsne = TSNE(n_components=2, init='pca', random_state=0)
    # pca = PCA(n_components=2, random_state=0)
    t0 = time()
    # result = tsne.fit_transform(data)
    result = tsne.fit_transform(data)
    print('result.shape',result.shape)
    fig = plot_embedding(result, label,
                         't-SNE embedding of the digits (time %.2fs)'
                         % (time() - t0))
    plt.show(fig)


if __name__ == '__main__':
    main()

```

