---
title: wandb 可视化调参
---

主从的结构

老板和员工

![](https://pic.imgdb.cn/item/641a8314a682492fccf565cb.png)

三步

```python
1. 配置sweep_config
配置调优算法，调优目标，需要优化的超参数列表 等
2. 初始化sweep controller
sweep_id = wandb.sweep(sweep_config)
3. 启动
wandb.agent(sweep_id, function=train)
```

开始之前

```python
import os,PIL 
import numpy as np
from torch.utils.data import DataLoader, Dataset
import torch 
from torch import nn 
import torchvision 
from torchvision import transforms
import datetime
import wandb 

wandb.login()
from argparse import Namespace

device = torch.device('cuda:0' if torch.cuda.is_available() else 'cpu')

#初始化参数配置
config = Namespace(
    project_name = 'wandb_demo',
    
    batch_size = 512,
    
    hidden_layer_width = 64,
    dropout_p = 0.1,
    
    lr = 1e-4,
    optim_type = 'Adam',
    
    epochs = 15,
    ckpt_path = 'checkpoint.pt'
)
```



## 配置sweep config

- 选择一个调优算法

![](https://pic.imgdb.cn/item/641a8315a682492fccf566b2.png)

俺们这边选择random算法

```python
sweep_config = {
    'method': 'random'
    }
```

- 定义优化目标

![](https://pic.imgdb.cn/item/641a8315a682492fccf56702.png)

```python
metric = {
    'name': 'val_acc',
    'goal': 'maximize'   
    }
sweep_config['metric'] = metric
```

- 定义超参空间

![](https://pic.imgdb.cn/item/641a8316a682492fccf5676b.png)

超参空间分成三种

```python
sweep_config['parameters'] = {}

# 固定不变的超参
sweep_config['parameters'].update({
    'project_name':{'value':'wandb_demo'},
    'epochs': {'value': 10},
    'ckpt_path': {'value':'checkpoint.pt'}})

# 离散型分布超参
sweep_config['parameters'].update({
    'optim_type': {
        'values': ['Adam', 'SGD','AdamW']
        },
    'hidden_layer_width': {
        'values': [16,32,48,64,80,96,112,128]
        }
    })

# 连续型分布超参
sweep_config['parameters'].update({
    
    'lr': {
        'distribution': 'log_uniform_values',
        'min': 1e-6,
        'max': 0.1
      },
    
    'batch_size': {
        'distribution': 'q_uniform',
        'q': 8,
        'min': 32,
        'max': 256,
      },
    
    'dropout_p': {
        'distribution': 'uniform',
        'min': 0,
        'max': 0.6,
      }
})
```

- 定义剪枝策略（可选）

![](https://pic.imgdb.cn/item/641a83aea682492fccf648d4.png)

```python
sweep_config['early_terminate'] = {
    'type':'hyperband',
    'min_iter':3,
    'eta':2,
    's':3
} #在step=3, 6, 12 时考虑是否剪枝
```

- 后言

可以通过pprint 来看看我们的config写的好不好

![](https://pic.imgdb.cn/item/641a83aea682492fccf648fe.png)

```python
from pprint import pprint
pprint(sweep_config)
```

## 初始化sweep controller（老板初始化）

```python
# 在一个project内 是 可以使用多个sweep的0
sweep_id = wandb.sweep(sweep_config, project=config.project_name)
```

## 启动sweep agent（开始干活）

需要将模型训练写成一个函数

![](https://pic.imgdb.cn/item/641a83aea682492fccf648b6.png)

```python
def create_dataloaders(config):
    transform = transforms.Compose([transforms.ToTensor()])
    ds_train = torchvision.datasets.MNIST(root="./mnist/",train=True,download=True,transform=transform)
    ds_val = torchvision.datasets.MNIST(root="./mnist/",train=False,download=True,transform=transform)

    ds_train_sub = torch.utils.data.Subset(ds_train, indices=range(0, len(ds_train), 5))
    dl_train =  torch.utils.data.DataLoader(ds_train_sub, batch_size=config.batch_size, shuffle=True,
                                            num_workers=2,drop_last=True)
    dl_val =  torch.utils.data.DataLoader(ds_val, batch_size=config.batch_size, shuffle=False, 
                                          num_workers=2,drop_last=True)
    return dl_train,dl_val

def create_net(config):
    net = nn.Sequential()
    net.add_module("conv1",nn.Conv2d(in_channels=1,out_channels=config.hidden_layer_width,kernel_size = 3))
    net.add_module("pool1",nn.MaxPool2d(kernel_size = 2,stride = 2)) 
    net.add_module("conv2",nn.Conv2d(in_channels=config.hidden_layer_width,
                                     out_channels=config.hidden_layer_width,kernel_size = 5))
    net.add_module("pool2",nn.MaxPool2d(kernel_size = 2,stride = 2))
    net.add_module("dropout",nn.Dropout2d(p = config.dropout_p))
    net.add_module("adaptive_pool",nn.AdaptiveMaxPool2d((1,1)))
    net.add_module("flatten",nn.Flatten())
    net.add_module("linear1",nn.Linear(config.hidden_layer_width,config.hidden_layer_width))
    net.add_module("relu",nn.ReLU())
    net.add_module("linear2",nn.Linear(config.hidden_layer_width,10))
    net.to(device)
    return net 

def train_epoch(model,dl_train,optimizer):
    model.train()
    for step, batch in enumerate(dl_train):
        features,labels = batch
        features,labels = features.to(device),labels.to(device)

        preds = model(features)
        loss = nn.CrossEntropyLoss()(preds,labels)
        loss.backward()

        optimizer.step()
        optimizer.zero_grad()
    return model

def eval_epoch(model,dl_val):
    model.eval()
    accurate = 0
    num_elems = 0
    for batch in dl_val:
        features,labels = batch
        features,labels = features.to(device),labels.to(device)
        with torch.no_grad():
            preds = model(features)
        predictions = preds.argmax(dim=-1)
        accurate_preds =  (predictions==labels)
        num_elems += accurate_preds.shape[0]
        accurate += accurate_preds.long().sum()

    val_acc = accurate.item() / num_elems
    return val_acc

def train(config = config):
    dl_train, dl_val = create_dataloaders(config)
    model = create_net(config); 
    optimizer = torch.optim.__dict__[config.optim_type](params=model.parameters(), lr=config.lr)
    #======================================================================
    nowtime = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
    wandb.init(project=config.project_name, config = config.__dict__, name = nowtime, save_code=True)
    model.run_id = wandb.run.id
    #======================================================================
    model.best_metric = -1.0
    for epoch in range(1,config.epochs+1):
        model = train_epoch(model,dl_train,optimizer)
        val_acc = eval_epoch(model,dl_val)
        if val_acc>model.best_metric:
            model.best_metric = val_acc
            torch.save(model.state_dict(),config.ckpt_path)   
        nowtime = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
        print(f"epoch【{epoch}】@{nowtime} --> val_acc= {100 * val_acc:.2f}%")
        #======================================================================
        wandb.log({'epoch':epoch, 'val_acc': val_acc, 'best_val_acc':model.best_metric})
        #======================================================================        
    #======================================================================
    wandb.finish()
    #======================================================================
    return model   

#model = train(config) 
```

将模型训练 最后打包成一个函数

最后

```python
wandb.agent(sweep_id, train, count=5)
```

## 可视化

平行坐标系图

![](https://pic.imgdb.cn/item/641a861da682492fccfafed7.png)

通过该图 我们可以知道具体的超参数 对于模型结果的影响 

参数重要性

![](https://pic.imgdb.cn/item/641a861da682492fccfafe9c.png)

