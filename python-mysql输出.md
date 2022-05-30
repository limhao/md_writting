---
title: python mysql输出
---

这个文章会有点长 

从安装到mysql基础 到 python 操作 mysql 

OK 现在开始

## mysql安装和卸载 8.0.26

### 安装

感谢：https://blog.csdn.net/a802976/article/details/119255644

文章没什么大问题 注意 安装配置的问题

- 下载安装包（没错）

- 安装配置（有一些很注意的东西）

 解压![img](https://img-blog.csdnimg.cn/770ef7859e5c43fd83ca557cda7e7dca.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2E4MDI5NzY=,size_16,color_FFFFFF,t_70) 

**编写MySQL**配置文件

在解压目录下新建my.ini文件

  ![img](https://img-blog.csdnimg.cn/648a35f4427e48bd9ccb9c4a944befaa.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2E4MDI5NzY=,size_16,color_FFFFFF,t_70) 

 将下面文本拷贝进my.ini文件中 

注意 下面的 两个地址basedir 和 datadir 这个很重要

```python
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录   ----------是你的文件路径-------------
basedir=D:\mysql-8.0.26-winx64\mysql-8.0.26-winx64
# 设置mysql数据库的数据的存放目录  ---------是你的文件路径data文件夹自行创建
#datadir=E:\mysql\mysql\data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。
max_connect_errors=10
# 服务端使用的字符集默认为utf8mb4
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
#mysql_native_password
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4
```

- 配置环境变量 （没错）

### 卸载

感谢：https://blog.csdn.net/weixin_42369926/article/details/81042133

教程完全无误 很不错

## mysql 教程

感谢：https://blog.csdn.net/weixin_45851945/article/details/114287877

这边找到了一个很好的工具网站

可以通过 json 转 mysql

https://www.json.cn/jsonzhuansql/

## Python之pymysql详解

pymysql 是个python 操作数据库的工具 然后 我连了下数据库 发现能用 很不错

感谢 ： https://blog.csdn.net/linwow/article/details/90269246