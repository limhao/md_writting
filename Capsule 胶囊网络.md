---
title: Capsule 网络
---

## 0. 简单总结

感谢：https://juejin.cn/post/6844903986995134471

https://zhuanlan.zhihu.com/p/90073690

为了改善CNN对旋转不具备不变性，学习不到3D空间信息和CNN只关注要检测的目标是否存在，而不关注这些组件之间的位置和相对的空间关系。 

- CNN 自己的处理

虽然max pooling在很多任务上提高了原始CNN的准确率，但是我们也可以看到max pooling丢失了很多有价值的信息，并没有很好地理解内容 

 **Capsule尝试去解决这些问题** （优势）

1.  Capsule可以学习到物体之间的位置关系，例如它可以学习到眉毛下面是眼睛，鼻子下面是嘴唇，可以减轻前面的目标组件乱序问题 
2.  Capsule可以对3D空间的关系进行明确建模，capsule可以学习到上面和下面的图片是同一个类别，只是视图的角度不一样。Capsule可以更好地在神经网络的内部知识表达中建立层次关系。 
3.   在训练的时候 能使用相对较少的数据集，但相对来说 其训练时间较CNN变得更长





## 1. capsule的结构

​	capsule 是向量，其可以理解为object的某个类别，其模长表示某个entity存在的概率，其方向表示某个entity属性

其计算的方式 为以下四步：

1. 对输入向量做乘法，其中 ![](https://juejin.cn/equation?tex=v_1) 和 ![](https://juejin.cn/equation?tex=v_2) 分别来自与前面的 capsule 的输出，在单个 capsule 内部，对  ![](https://juejin.cn/equation?tex=v_1) 和 ![](https://juejin.cn/equation?tex=v_2)  分别乘上  ![](https://juejin.cn/equation?tex=w_1) 和 ![](https://juejin.cn/equation?tex=w_2)  得到了 新的  ![](https://juejin.cn/equation?tex=u_1) 和 ![](https://juejin.cn/equation?tex=u_2)  。

2. 对输入向量进行标量加权，令![](https://juejin.cn/equation?tex=u_1)与![](https://juejin.cn/equation?tex=c_1)相乘，![](https://juejin.cn/equation?tex=u_2)与![](https://juejin.cn/equation?tex=c_2)相乘，其中![](https://juejin.cn/equation?tex=c_1)和![](https://juejin.cn/equation?tex=c_2)均为标量，且![c_1 + C_2 = 1](https://juejin.cn/equation?tex=c_1%20%2B%20C_2%20%3D%201)。

3. 对得到向量求和，得到![](https://juejin.cn/equation?tex=s%3Dc_1%20u_1%20%2Bc_2u_2)。

4. 向量到向量的非线性化，将得到的结果向量 ![](https://juejin.cn/equation?tex=s) 进行转换，即通过函数 ![Squash(s)= \cfrac{||s||^2}{1+||s||^2} \cfrac{s}{||s||}](https://juejin.cn/equation?tex=Squash(s)%3D%20%5Ccfrac%7B%7C%7Cs%7C%7C%5E2%7D%7B1%2B%7C%7Cs%7C%7C%5E2%7D%20%5Ccfrac%7Bs%7D%7B%7C%7Cs%7C%7C%7D)得到结果 ![](https://juejin.cn/equation?tex=s)，作为这个capsule 的输出，且这个结果 ![](https://juejin.cn/equation?tex=v) 可以作为下一个 capsule 的输入

 ![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/11/4/16e359ecb29a13e4~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp) 

## 2. 细节（训练方式）

动态寻路算法

鬼都看不懂下面这个![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/11/4/16e359ecb2e9c973~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp) 

- 直观理解

其中两个高层胶囊的输出用紫色向量 ![](https://juejin.cn/equation?tex=v_1%20v_2) 表示，橙色向量表示接受自某个低层胶囊的输入，其他黑色向量表示接受其他低层胶囊的输入。左边的紫色输出 ![](https://juejin.cn/equation?tex=v_1) 和橙色输入 ![](https://juejin.cn/equation?tex=u_1) 指向相反的方向，所以它们并不相似，这意味着它们点积是负数，更新路由系数的时候将会减少![](https://juejin.cn/equation?tex=c_1%5E1)。右边的紫色输出 ![](https://juejin.cn/equation?tex=v_2) 和橙色输入 ![](https://juejin.cn/equation?tex=u_2) 指向相同方向，它们是相似的，因此更新参数的时候路由系数 ![](https://juejin.cn/equation?tex=c_1%5E2) 会增加。在所有高层胶囊及其所有输入上重复应用该过程，得到一个路由参数集合，达到来自低层胶囊的输出和高层胶囊输出的最佳匹配。


 ![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/11/4/16e359ecb8feb633~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp) 