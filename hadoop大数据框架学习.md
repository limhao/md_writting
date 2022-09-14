---
 title: 大数据学习
---

2022/8/30日 开始 

https://www.bilibili.com/video/BV1CU4y1N7Sh

课程的学习 

预计花费时间两周

## 数据分析基本步骤

- 明确分析目的和思路
  思路是使分析框架体系化，比如先分析什么，后分析什么，使各分析点之间具有逻辑联系，保证分析维度的完整性，分析结果的有效性以及正确性，需要数据分析方法论进行支撑；

  PEST分析法

- 数据收集

  互联网公开数据
  业务数据
  日志数据
  爬虫数据

- 数据处理

  数据预处理

  数据清洗 数据转化 数据提取 数据计算

- 数据分析

  数据分析方法 数据分析软件

- 数据展现

  数据可视化 分析结果图表展示

- 报告撰写

  数据分析报告是对整个数据分析过程的一个总结与呈现
  把数据分析的起因、过程、结果及建议完整地呈现出来，供决策者参考
  需要有明确的结论，最好有建议或解决方案

## 分布式与集群

共同点：多台机器

分布式： 多台机器每台机器上部署不同组件

集群：多台机器每台机器上部署相同组件

## vimware

第一件事 是调整虚拟机 与 本机 是在同一个网段下面

vm中 是 编辑-》虚拟网络编辑器-》更改设置

win下 是 网络连接 -》vm8 -》tcpipv4 -》设置网段

- 挂起

  类似软件冻结 再次开机恢复原来状态

- 快照

  可以将虚拟机 恢复到我们之前设定好的状态 （几乎是一瞬间回复）

  说实话 这个功能今天才知道 算是一个大杀器

## ssh

[![](https://s1.ax1x.com/2022/08/30/v4SjO0.md.png)](https://imgse.com/i/v4SjO0)

公钥私钥成双成对

都是服务器生成的

服务器将公钥给客户端 客户端使用公钥进行加密

最后服务器将加密数据用私钥进行解密 就完成了这个过程

## linux 文件系统

- 操作系统中负责管理和存储文件信息的软件机构称为文件管理系统，简称文件系统;

- 文件系统的结构通常叫做目录树结构，从斜杠/根目录开始;
- Linux号称“万物皆文件”，意味着针对Linux的操作，大多数时间是在针对Linux文件系统操作。

```shell
ls 查看文件
cd 切换路径
pwd 查看位置
mkdir 创建文件夹
touch 创建空文件
rm 删除
rm -f 强制删除 无需确认
rm -r 递归删除 针对文件夹
rm - rf/* 要死人
cp 复制文件或目录
 -r 若给出的源文件是一个目录文件 将复制该目录下所有的子目录和文件
mv 文件或者目录改名 或者 文件或目录移入其他位置
# 文件内容查看
cat 小文件查看
more 类似cat space下一页 b上一页
tail 用于查看文件结尾部分内容
 -n 用于显示行数 默认为是10
 -f 用于实时显示文件动态追加的内容。会把文件里的最尾部的内容显示在屏幕上，并且不断刷新，只要文件有更新，就可以看到最新的文件内容。
# 其他
| 管道命令 （有趣）
管道命令:将前一个命令执行的结果作为内容交给下一个命令处理。可以形成多级管道操作。
命令1|命令2 可以将命令1的结果通过命令2作进一步的处理
echo 命令 用于内容的输出 将内容 输出到console控制台上
> 输出重定向（覆盖）
command > file 执行command然后将输出的内容存入file ,file内已经存在的内容将被新内容覆盖替代。
>> 输出重定向（追加）
command >> file 执行command然后将输出的内容存入file，新内容追加在文件末尾。
```
[![](https://s1.ax1x.com/2022/08/31/v5PDMj.md.png)](https://imgse.com/i/v5PDMj)


```shell
# 解压缩命令 tar
 -z 表示 tar 包是被 gzip 压缩过的，所以解压时需要用 gunzip 解压
 -c 建立新的备份文件
 -x 从备份文件中还原文件
 -v 显示命令执行过程
 -f 指定备份文件
 # 解压文件到bbb中
 tar -xvf demo.tar -C bbb/
 -zxvf 从解压出来的tar中把文件提取出来，并且详细的展示！
```

```shell
free 显示内存使用情况
free -h 人性化显示
df -h 查看磁盘利用率
ps 查看进程状态
ps -ef|grep 进程名 查看本机所有进程
kill -9 【进程号】杀死进程
jps java jdk自带的命令 专门查看本机运行的java进程情况
```

## vim编辑器

https://blog.csdn.net/qq_52914969/article/details/123026341

## Apache Hadoop

hadoop集群包括两个

1. HDFS

2. YARN

两个集群 逻辑上分离 物理上在一起

两个集群 是标准的主从架构

[![](https://s1.ax1x.com/2022/08/31/v5Prss.md.png)](https://imgse.com/i/v5Prss)

[![](https://s1.ax1x.com/2022/08/31/v5PsLn.md.png)](https://imgse.com/i/v5PsLn)

### hadoop 安装包目录结构

- 结构

  [![](https://s1.ax1x.com/2022/09/03/voGqbR.md.png)](https://imgse.com/i/voGqbR)

- 配置文件

[![voGXUx.md.png](https://s1.ax1x.com/2022/09/03/voGXUx.md.png)](https://imgse.com/i/voGXUx)

第一类 java路径 和 运行的进程

第二类 核心模块的配置 （hdfs mapreduce yarn） 外加 核心模块设置

第三类 表示 小弟从角色运行在什么机器上

- 将hadoop添加配置到环境变量

  大G小o 来到最后一行进行编辑

然后 source /etc/profile 弄好配置文件

- namenode format 初始化操作（只需要做一次）

  hdfs namenode -format

  本质上是初始化工作 进行hdfs清理和准备工作

[![](https://s1.ax1x.com/2022/09/03/voGbr9.png)](https://imgse.com/i/voGbr9)

成功的样子！

[![](https://s1.ax1x.com/2022/09/03/voGj56.md.png)](https://imgse.com/i/voGj56)

### hadoop开启

- 开启脚本

[![](https://s1.ax1x.com/2022/09/03/voGOV1.md.png)](https://imgse.com/i/voGOV1)

- 开启成功检测方法

  [![](https://s1.ax1x.com/2022/09/03/voGz8O.md.png)](https://imgse.com/i/voGz8O)

### web ui开启

HDFS 集群

[![](https://s1.ax1x.com/2022/09/04/vT94RU.md.png)](https://imgse.com/i/vT94RU)

yarn集群

[![](https://s1.ax1x.com/2022/09/04/vT95zF.md.png)](https://imgse.com/i/vT95zF)

## HDFS

- 命令行

```python
# shell 命令操作
# 个人感知 在 linux命令前面 加个 hadoop fs 
# 就像是在windows操作一样
hadoop fs -mkdir /itcast
# 上传xxx到 /itcast 文件夹
hadoop fs -put xxxx /itcast
# 查询
hadoop fs -ls /
```

- web ui 操作

  [![](https://s1.ax1x.com/2022/09/04/vT9hGT.md.png)](https://imgse.com/i/vT9hGT)



### hadoop重要组件-hdfs

HDFS - hadoop分布式文件系统

一个分布式存储系统核心属性

1. 分布式存储
2. 元数据记录
3. 分块存储
4. 副本机制（冗余存储）

[![](https://s1.ax1x.com/2022/09/04/vT9oM4.md.png)](https://imgse.com/i/vT9oM4)

HDFS 设计场景 是 大文件 大数据场景

[![](https://s1.ax1x.com/2022/09/04/vT97L9.md.png)](https://imgse.com/i/vT97L9)

- 文件系统协议

```shell
# HDFS Shell CLI支持操作多种文件系统，包括本地文件系统(file:///)、分布式文件系统 ( hdfs:/nn:8020)等具体操作的是什么文件系统取决于命令中文件路径URL中的前缀协议。
# 如果没有指定前缀，则将会读取环境变量中的fs.defaultFS属性，以该属性值作为默认文件系统。
# fs -> file system
hadoop fs -ls file:/// # 操作本地文件系统
hadoop fs -ls hdfs://node1:8020/ # 操作HDFS分布式文件系统
hadoop fs -ls / # 直接根目录 没有指定协议 将加载读取fs.defaultFS 中的值
```

- 命令行常用指令

```shell
# 创建文件夹
hadoop fs -mkdir [-p] <path>
path 指定目录路径
-h 人性化
-R 递归查看指定目录及其子目录
# 查看文件目录
hadoop fs -ls [-h] [-R] [<path>...]
path 指定目录路径
-h 人性化
-R 递归查看指定目录及其子目录
# 上传文件
hadoop fs -put [-f] [-p] <localsrc> <dst>
-f 覆盖目标文件
-p 保留访问和修改时间
localsrc 本地文件系统（客户端所在机器）
dst 目标文件系统（HDFS）
下面是标准写法
# hadoop fs -put file:///root/2.txt hdfs://node1:8020/
# 查看HDFS 文件内容
hadoop fs -cat <src>
读取指定文件全部内容，显示在标准输出控制台。注意:对于大文件内容读取，慎重。
# 下载文件
hadoop fs -get [-f] [-g] <src> <localdst>
下载文件到本地文件系统指定目录，localdst必须是目录
-f 覆盖目标文件（已存在下)
-p 保留访问和修改时间，所有权和权限。
# 追加数据到HDFS文件中（很重要）小文件合并
hadoop fs -appendToFile <localsrc> <dst>
将所有给定本地文件的内容追加到给定dst文件。dst如果文件不存在，将创建该文件。
如果<localSrc>为-，则输入为从标准输入中读取。
相对于将文件进行合并
```

### HDFS 角色划分

- namenode

[![](https://s1.ax1x.com/2022/09/04/vT9bZR.md.png)](https://imgse.com/i/vT9bZR)

- datanode

[![](https://s1.ax1x.com/2022/09/04/vTCidI.md.png)](https://imgse.com/i/vTCidI)

- secondnamenode

namenode的秘书

一般是给namenode分担压力 

但是 不能替代namenode

### 写数据流程

三个核心概念

- pipeline管道

[![](https://s1.ax1x.com/2022/09/04/vT9qd1.md.png)](https://imgse.com/i/vT9qd1)

- ack应答响应

[![](https://s1.ax1x.com/2022/09/04/vTCPeA.md.png)](https://imgse.com/i/vTCPeA)

- 副本存储

[![vT9LIx.md.png](https://s1.ax1x.com/2022/09/04/vT9LIx.md.png)](https://imgse.com/i/vT9LIx)

## MapReduce 

### MapReduce-分布式计算框架

**MapReduce的思想核心是“先分再合，分而治之”。**
所谓“分而治之”就是把一个复杂的问题，按照一定的“分解”方法分为等价的规模较小的若干部分，然后逐个解决，分别找出各部分的结果，然后把各部分的结果组成整个问题的最终结果。
这种思想来源于日常生活与工作时的经验。即使是发布过论文实现分布式计算的谷歌也只是实现了这种思想，而不是自己原创。

- MAP 负责拆分

即把复杂的任务分解为若干个“简单的子任务”来并行处理。可以进行拆分的前提是这些小任务可以并行计算，彼此间几乎没有依赖关系。

- Reduce 负责 合并

对map阶段结果进行全局汇总



在当下 MR框架已经退居二线  

- 局限性

实时计算性能差，主要用于离线作业

- 不能进行流式计算

流式计算的特点是数据源源不断得计算，并且数据是动态的

### MapReduce 实例进程

MRAppMaster:负责整个MR程序的过程调度及状态协调
MapTask :负责map阶段的整个数据处理流程
ReduceTask:负责reduce阶段的整个数据处理流程

### 阶段组成

- 一个编程模型中组成

1.  1个map 1个reduce
2. 只有map

[![](https://s1.ax1x.com/2022/09/04/vTZC5j.md.png)](https://imgse.com/i/vTZC5j)

### 数据类型

![](https://s1.ax1x.com/2022/09/04/vTZpVg.md.png)

### mapreduce 程序运行

![](https://s1.ax1x.com/2022/09/04/vTZ9aQ.md.png)

### map阶段执行过程

[![](https://s1.ax1x.com/2022/09/04/vTZAx0.md.png)](https://imgse.com/i/vTZAx0)

[![](https://s1.ax1x.com/2022/09/04/vTZFGn.md.png)](https://imgse.com/i/vTZFGn)

[![](https://s1.ax1x.com/2022/09/04/vTZVMV.md.png)](https://imgse.com/i/vTZVMV)

### reduce阶段执行过程

[![](https://s1.ax1x.com/2022/09/04/vTZk2q.md.png)](https://imgse.com/i/vTZk2q)

### shuffle机制

- map端shuffle

[![](https://s1.ax1x.com/2022/09/05/vTdAAJ.md.png)](https://imgse.com/i/vTdAAJ)

- reduce端shuffle

[![](https://s1.ax1x.com/2022/09/05/vTdF74.md.png)](https://imgse.com/i/vTdF74)

- shuffle机制弊端

1. Shuffle是MapReduce程序的核心与精髓，是MapReduce的灵魂所在。
2. Shuffle也是MapReduce被诟病最多的地方所在。MapReduce相比较于Spark、Flink计算引擎慢的原因，Shuffle机制有很大的关系。
3. Shuffle中频繁涉及到数据在内存、磁盘之间的多次往复。

## YARN - 通用资源管理系统和调度平台

[![](https://s1.ax1x.com/2022/09/05/vTdEN9.md.png)](https://imgse.com/i/vTdEN9)

1. 资源管理系统:集群的硬件资源，和程序运行相关，比如内存CPU等。
2. 调度平台:多个程序同时申请计算资源如何分配，调度的规则（算法）。
3. 通用∶不仅仅支持MapReduce程序，理论上支持各种计算程序。YARN不关心你干什么，只关心你要资源，在有的情况下给你，用完之后还我。
4. 可以把Hladoop YARN理解为相当于一个分布式的操作系统平台，而MapReduce等计算程序则相当于运行于操作系统之上的应用程序，YARN为这些程序提供运算所需的资源(内存、CPU等）。
5. Hadoop能有今天这个地位，YARN可以说是功不可没。因为有了YARN，更多计算框架可以接入到HDFS中，而不单单是MapReduce，正是因为YARN的包容，使得其他计算框架能专注于计算性能的提升。
6. HDFS可能不是最优秀的大数据存储系统，但却是应用最广泛的大数据存储系统，YARN功不可没。

### YARN 三大组件

1. RM（ResourceManager）

YARN集群中的主角色，决定系统中所有应用程序之间资源分配的最终权限，即最终仲裁者。接收用户的作业提交，并通过NM分配、管理各个机器上的计算资源。

2. NM (NodeManager)

YARN中的从角色，一台机器上一个，负责管理本机器上的计算资源。根据RM命令，启动Container容器、监视容器的资源使用情况。并且向RM主角色汇报资源使用情况。

3. AM (ApplicationMaster ( App Mstr ) )

用户提交的每个应用程序均包含一个AM。应用程序内的“老大”，负责程序内部各阶段的资源申请，监督程序的执行情况。

### 程序交互流程

[![](https://s1.ax1x.com/2022/09/05/vTHPWn.md.png)](https://imgse.com/i/vTHPWn)

- 核心交互流程

1. MR作业提交 Client -> RM
2. 资源申请 App Mstr -> RM
3. MR 作业状态汇报  Container ( Map|Reduce Task ) -->Container ( MrAppMaster )
4. 节点状态汇报 NM -> RM

用户向YARN 提交了一个应用程序后 YARN分两个阶段运行该程序

1. 第一个阶段： 客户端申请资源启动本次程序 AM
2. 第二个阶段： AM根据本次程序内部具体情况，为它申请资源，并监控它整个运行过程

- 分步讲解

1. 用户通过客户端向YARN中ResourceManager提交应用程序（比如hadoop jar提交MR程序）;
2. ResourceManager为该应用程序分配第一个Container(容器），并与对应的NodeManager通信，要求它在这个Container中启动这个应用程序的ApplicationMaster。
3. ApplicationMaster启动成功之后，首先向ResourceManager注册并保持通信，这样用户可以直接通过ResourceManage查看应用程序的运行状态（处理了百分之几）;
4. AM为本次程序内部的各个Task任务向RM申请资源，并监控它的运行状态;
5. 一旦ApplicationMaster 申请到资源后，便与对应的NodeManager通信，要求它启动任务
6. NodeManager为任务设置好运行环境后，将任务启动命令写到一个脚本中，并通过运行该脚本启动任务
7. 各个任务通过某个 RPC 协议向 ApplicationMaster汇报自己的状态和进度，以让 ApplicationMaster随时掌握各个任务的运行状态，从而可以在任务失败时重新启动任务。在应用程序运行过程中，用户可随时通过RPC向ApplicationMaster查询应用程序的当前运行状态。
8. 应用程序运行完成后，ApplicationMaster向 ResourceManager 注销并关闭自己

### scheduler 资源调度器

调度策略