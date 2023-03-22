---
title: wandb 使用教程（真的好用）
---

## 0 前言

相比TensorBoard，wandb具有如下主要优势：

- 日志上传云端永久存储，便于分享不怕丢失
- 可以存管代码数据集和模型的版本，随时复现。(wandb.Artifact)
- 可以使用交互式表格进行case分析(wandb.Table)
- 可以自动化模型调参。(wandb.sweep)



wandb 的平台 能干啥？

![](https://pic.imgdb.cn/item/64187035a682492fcc9f2327.png)

![](https://pic.imgdb.cn/item/64187034a682492fcc9f2150.png)

核心功能有4个

- 实验跟踪:experiment tracking (wandb.log)

- 版本管理:version management (wandb.log_artifact, wandb.save) 

- case分析:case visualization (wandb.Table, wandb.Image)

- 超参调优:model optimization (wandb.sweep)

## 实验跟踪（experiment tracking）

先总结下 欸 自己的想法

```python
针对于 这个模块的使用
其实就三步走
1. wandb.init 初始化wandb
2. wandb.log 欸 让wandb 知道你要记啥
3. wandb.finish 完事了 你走吧

```

![](https://pic.imgdb.cn/item/64187035a682492fcc9f2364.png)

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
from argparse import Namespace

device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
# argprase 设置超参数 对于模型可读性会比较好
# 可以通过config.lr 来读取参数可读性会很好
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
# 创建数据集代码

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
# 在这里 就会开始使用一些 config里面的超参数
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
# 训练

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
# 验证

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
    # 初始化设置 需要将参数config设置成字典 init 
    # 刚刚一直在想超参数 是放在哪里的说 
    # 原来一直在这里
    wandb.init(project=config.project_name, config = config.__dict__, name = nowtime, save_code=True)
    model.run_id = wandb.run.id
    #======================================================================
    model.best_metric = -1.0
    for epoch in range(1,config.epochs+1):
        # 先train 后 val
        model = train_epoch(model,dl_train,optimizer)
        val_acc = eval_epoch(model,dl_val)
        if val_acc > model.best_metric:
            model.best_metric = val_acc
            torch.save(model.state_dict(),config.ckpt_path)   
        nowtime = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
        print(f"epoch【{epoch}】@{nowtime} --> val_acc= {100 * val_acc:.2f}%")
        #======================================================================
        # 在跑完模型之后 保存下 模型的最好的结果
        wandb.log({'epoch':epoch, 'val_acc': val_acc, 'best_val_acc':model.best_metric})
        #======================================================================        
    #======================================================================
    # train 完之后 finish 
    wandb.finish()
    #======================================================================
    return model  

"""
三步走
1. wandb init 规定好 项目名称 超参数 run的这一下的名称 是否保存代码
2. wandb.log 定义好要记录的东西 来绘制折线图（一般来说 acc 和 最好的acc 是肯定要记录的 ）
3. wandb.finish 打完收工  

"""
# 三步走 的作用 
model = train(config) ##3,2,1 点火🔥🔥
```

![](https://pic.imgdb.cn/item/641870c2a682492fcca0795d.png)

### metrics记录

1. 针对于跑过的记录

![](https://pic.imgdb.cn/item/64194c50a682492fcc029d68.png)

2. 在模型中 可以添加图 来展现重要数据

![](https://pic.imgdb.cn/item/64194c50a682492fcc029dc5.png)

3. 在table 中 通过pin数据 来关注重要数据 然后 在之前也能展现出来

![](https://pic.imgdb.cn/item/64194ce6a682492fcc038cda.png)    

![](https://pic.imgdb.cn/item/64194c50a682492fcc029d68.png)

## 版本管理--最终成果保存

![](https://pic.imgdb.cn/item/64187034a682492fcc9f21fa.png)

log_artifact 来保存任务关联的重要成果

**注:artifact翻译为"工件"，是指软件开发中产出的最终成果**

版本管理 存在于三种

1. 数据集
2. 模型文件
3. 模型权重

```python
import wandb 
# 通过指定模型的run_id 来恢复run任务 
# id嘛 独一无二
run = wandb.init(project='wandb_demo', id= model.run_id, resume='must')
# print(model.run_id)
# save dataset 
arti_dataset = wandb.Artifact('mnist', type='dataset')
arti_dataset.add_dir('mnist/')
wandb.log_artifact(arti_dataset)
# save code 

arti_code = wandb.Artifact('ipynb', type='code')
arti_code.add_file('./1122.ipynb')
wandb.log_artifact(arti_code)
# save model

arti_model = wandb.Artifact('cnn', type='model')
arti_model.add_file(config.ckpt_path)
wandb.log_artifact(arti_model)

wandb.finish() #finish时会提交保存
```

在外面的大循环上 可以看到经典的 init，finish这样的操作

在save里面

![](https://pic.imgdb.cn/item/64194c52a682492fcc02a0fd.png)



```python
1. wandb.Artifact
2. add_dir add_file 添加的文件 前面是文件夹 后面是文件
3. wandb.log_artifact 保存文件
```

## case分析

![](https://pic.imgdb.cn/item/6419ba9aa682492fccdac702.png)

```python
import wandb 
run = wandb.init(project=config.project_name, id= model.run_id, resume='must')

mport matplotlib.pyplot as plt 

transform = transforms.Compose([transforms.ToTensor()])
ds_train = torchvision.datasets.MNIST(root="./mnist/",train=True,download=True,transform=transform)
ds_val = torchvision.datasets.MNIST(root="./mnist/",train=False,download=True,transform=transform)
# 常规的matplotlib 分析
# visual the  prediction
device = None
for p in model.parameters():
    device = p.device
    break

plt.figure(figsize=(8,8)) 
for i in range(9):
    img,label = ds_val[i]
    tensor = img.to(device)
    y_pred = torch.argmax(model(tensor[None,...])) 
    img = img.permute(1,2,0)
    ax=plt.subplot(3,3,i+1)
    ax.imshow(img.numpy())
    ax.set_title("y_pred = %d"%y_pred)
    ax.set_xticks([])
    ax.set_yticks([]) 
plt.show()    
```
- 定义辅助函数 不是必须的
```python
def data2fig(data):
    # 图像转换成 matplotlib 
    import matplotlib.pyplot as plt 
    fig = plt.figure()
    ax = fig.add_subplot()
    ax.imshow(data)
    ax.set_xticks([])
    ax.set_yticks([]) 
    return fig

def fig2img(fig):
    # mat转换成plo
    import io,PIL
    buf = io.BytesIO()
    fig.savefig(buf)
    buf.seek(0)
    img = PIL.Image.open(buf)
    return img
```

**开始重要的了**

```python
# 这里使用了 wandb.table 函数
from tqdm import tqdm 
good_cases = wandb.Table(columns = ['Image','GroundTruth','Prediction'])
bad_cases = wandb.Table(columns = ['Image','GroundTruth','Prediction'])
```

- 作为table 是可以添加 数据的 add_data

```python
plt.close()
# 找到50个good cases 和 50 个bad cases

for i in tqdm(range(1000)):
    features,label = ds_val[i]
    tensor = features.to(device)
    y_pred = torch.argmax(model(tensor[None,...])) 
    
    # log badcase
    if y_pred!=label:
        if len(bad_cases.data)<50:
            data = features.permute(1,2,0).numpy()
            input_img = wandb.Image(fig2img(data2fig(data)))
            bad_cases.add_data(input_img,label,y_pred)
            
    # log goodcase
    else:
        if len(good_cases.data)<50:
            data = features.permute(1,2,0).numpy()
            input_img = wandb.Image(fig2img(data2fig(data)))
            good_cases.add_data(input_img,label,y_pred)
```

- 最后两步

```python
wandb.log({'good_cases':good_cases,'bad_cases':bad_cases})
wandb.finish()
```

![](https://pic.imgdb.cn/item/6419ba9aa682492fccdac6aa.png)