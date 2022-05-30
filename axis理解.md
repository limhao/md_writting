---
title: python中axis=0和axis=1的理解
---

原文链接：https://blog.csdn.net/leilei7407/article/details/104461215

axis的重点在于方向，而不是行和列。1表示横轴，方向从左到右；0表示纵轴，方向从上到下。

即axis=1为横向，axis=0为纵向，而不是行和列，具体到各种用法而言也是如此。
当axis=1时，如果是求平均，那么是从左到右横向求平均；如果是拼接，那么也是左右横向拼接；如果是drop，那么也是横向发生变化，体现为列的减少

- axis = 0 纵向处理
- axis = 1 横向处理

```python
import pandas as pd
df_1= pd.DataFrame([[1, 1, 1, 1], [2, 2, 2, 2], [3, 3, 3, 3]], \
columns=["col1", "col2", "col3", "col4"])  #\代表与下面的代码是连接着的
print(df_1)
print('-------')

print(df_1.mean(axis=1))
print('----------')
print(df_1.drop('col1',axis=1))

```



 ![img](https://img-blog.csdnimg.cn/20200223154620381.png) 