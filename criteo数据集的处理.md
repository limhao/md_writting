---
title: criteo数据集的处理 生动
---

之前 写过一篇 太烂了 在重新写一篇生动的

数据预处理与特征工程：https://blog.csdn.net/qq_37334135/article/details/86825190

目前项目涉及三个数据集（criteo，amazon，movielen）

其中 criteo数据集肯定是使用的最多的啦 

这里 对于数据的梳理 （对于数据的处理 我觉得也较通用）

特地 整理一个文档

## 0. 观察数据（其实很重要）

[![qvboOs.png](https://s1.ax1x.com/2022/04/06/qvboOs.png)](https://imgtu.com/i/qvboOs)

数据 有0 1 2 这样的离散数据 和 一些68fd1e64	80e26c9b	fb936136	7b4723c4 不晓得是啥的数据 

感觉能确定的是 第一列的数据 是标签数据

欧克 总结数据如下 有标签 离散 和 不晓得是啥的数据（先确定为连续数据吧）

按照习惯 其target一般特征后面 

## 1. 读取数据

```python
# 标记 数据类型
names = ['label',
         'I1', 'I2', 'I3', 'I4', 'I5', 'I6', 'I7', 'I8', 'I9', 'I10', 'I11',
'I12', 'I13',
         'C1', 'C2', 'C3', 'C4', 'C5', 'C6', 'C7', 'C8', 'C9', 'C10', 'C11','C12', 'C13', 'C14', 'C15', 'C16', 'C17', 'C18', 'C19', 'C20', 'C21', 'C22','C23', 'C24', 'C25', 'C26']

criteo_data = pd.read_csv('./criteo-100k.txt',sep='\t',names=names)
```

### 1.1 标准数据查看三件套

```python
criteo_data.head()
criteo_data.shape
criteo_data.info()
```

### 1.2 数据质量分析

- 查看缺失值

```python
criteo_data.isnull().sum()
```

- 异常值分析

```
# 查看==数值型列==的汇总统计
# 这里的目的是查看是否有及其异常的值
criteo_data.describe()
```

[![qvOH0A.png](https://s1.ax1x.com/2022/04/06/qvOH0A.png)](https://imgtu.com/i/qvOH0A)

### 1.3 数据分类

```python
# 数据特征分为离散和连续类
sparse_features = ['C' + str(i) for i in range(1, 27)]
dense_features = ['I' + str(i) for i in range(1, 14)]
features = sparse_features + dense_features
# 检查 特征列是否添加完全
assert len(sparse_features) + len(dense_features) == criteo_data.shape[1] - 1
```

## 2. 缺失值处理

针对缺失值 的 离散 和连续缺失的值 进行缺失值处理 （缺失一般是异常值）

一般是四步操作

1. 查看变量类别
2. 查看变量缺失值情况
3. 对于缺失值进行标注
4. 查看标注完缺失值情况

```python
# 查看离散变量目前的类别
criteo_data[sparse_features].dtypes
# 查看离散变量缺失值情况
criteo_data[sparse_features].isnull().sum()
# 离散缺失值标注
criteo_data[sparse_features] = criteo_data[sparse_features].fillna('-1')
# 查看离散变量缺失值情况
criteo_data[sparse_features].isnull().sum(
```

```python
# 查看连续变量目前的类别
criteo_data[sparse_features].dtypes
# 查看连续变量缺失值情况
criteo_data[sparse_features].isnull().sum()
# 连续缺失值标注
criteo_data[sparse_features] = criteo_data[sparse_features].fillna(0)
# 查看连续变量缺失值情况
criteo_data[sparse_features].isnull().sum()
```

## 3.  **sklearn.preprocessing数据预处理**

这里搬运一下 分箱的目的 

1. 离散变量便于特征的增加和减少，便于模型快速迭代
2. 稀疏向量内积乘法更快，计算结果便于存储，容易扩展
3. 离散化后的特征对异常数据有很强的**鲁棒性**，例如，连续异常值5000可能对模型影响很大，但如果分箱后，模型影响很小
4. 为模型引入非线性，提升模型表达能力，加大拟合
5. 模型更加稳定，不会因为各别数据增加而影响模型精度
6. 简化模型，防止模型过拟合

### 3.1 处理连续型特征

在处理连续型特征 有两个方法 二值化 与 分段

- 二值化

二值化使用的类是 sklearn.preprocessing.Binarizer

根据阈值将数据二值化（将特征值设置为0或1），用于处理连续型变量。大于阈值的值映射为1，而小于或等于阈值的值映射为0。默认阈值为0时，特征中所有的正值都映射到1。二值化是对文本计数数据的常见操作，分析人员可以决定仅考虑某种现象的存在与否。

- 分段

 分段使用的类 **preprocessing.KBinsDiscretizer**，相对麻烦点，给出下面的参数列表 

 ![](https://img-blog.csdnimg.cn/20190209155408360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MzM0MTM1,size_16,color_FFFFFF,t_70) 

```python
from sklearn.preprocessing import LabelEncoder, OrdinalEncoder, KBinsDiscretizer
# 连续型特征等间隔分箱
est = KBinsDiscretizer(n_bins=100, encode='ordinal', strategy='uniform')
criteo_data[dense_features] = est.fit_transform(criteo_data[dense_features])
```

### 3.2 处理分类型特征

在机器学习中，大多数算法，譬如逻辑回归，支持向量机SVM，k近邻算法等都只能够处理数值型数据，不能处理文字，在sklearn当中，除了专用来处理文字的算法，其他算法在fit的时候全部要求输入数组或矩阵，也不能够导入文字型数据（其实手写决策树和普斯贝叶斯可以处理文字，但是sklearn中规定必须导入数值型）。

然而在现实中，许多标签和特征在数据收集完毕的时候，都不是以数字来表现的。比如说，学历的取值可以是[“小学”，“初中”，“高中”，“大学”]，付费方式可能包含[“支付宝”，“现金”，“微信”]等等。在这种情况下，为了让数据适应算法和库，我们必须将数据进行编码，即是说，将文字型数据转换为数值型

文字型数据 转换成数字

```python
from sklearn.preprocessing import LabelEncoder, OrdinalEncoder, KBinsDiscretizer
# 离散型特征转换成连续数字，为了在与参数计算时使用索引的方式计算，而不是向量乘积
criteo_data[features] = OrdinalEncoder().fit_transform(criteo_data[features])
```

处理后的效果

[][qzetZn.md.png]

![](https://s1.ax1x.com/2022/04/07/qzetZn.md.png)



## 4. 划分标签和特征

```python
# 标签和数据换个位置 然后 去除col_names 
data = data_df[features + ['label']].values
```

## 5. 划分训练，验证，测试集

```python
# 这里的意思是 大白话
# 通过数字来区分 特征与特征的不同 
# 这里的特征 啊 
# 不同于俺以前理解的数字 
# 其表示的感觉就是 0123 男，女，外星人，不是人 这个样子的分类
# 这里 加1的作用 哇 刚刚想明白的 从0-540 一共 541个数
field_dims = (self.data.max(axis=0).astype(int) + 1).tolist()[:-1]
```

### 5.1 数据划分

```python
    train, valid_test = train_test_split(self.data, train_size=train_size, random_state=2021)
    valid_size = valid_size / (test_size + valid_size)
    valid, test = train_test_split(valid_test, train_size=valid_size, random_state=2021)
	# 上面划分 是简单的 
    device = self.device
	# 这里 想 重点说一下两个[:, :-1] [:, -1]区分
    # 举个例子 一个社会 有很多家庭 每个家庭 有很多人
    # 前一个: 取 哪个家庭
    # 后一个 : 取 那些人
    # [:, :-1] 这个的意思是 取 所有家庭内 除了最后一个以外的所有人
    # [:, -1] 意思是 取 所有家庭内 最后一个人
    train_X = torch.tensor(train[:, :-1], dtype=torch.long).to(device)
    valid_X = torch.tensor(valid[:, :-1], dtype=torch.long).to(device)
    test_X = torch.tensor(test[:, :-1], dtype=torch.long).to(device)
    train_y = torch.tensor(train[:, -1], dtype=torch.float).unsqueeze(1).to(device)
    valid_y = torch.tensor(valid[:, -1], dtype=torch.float).unsqueeze(1).to(device)
    test_y = torch.tensor(test[:, -1], dtype=torch.float).unsqueeze(1).to(device)
```
