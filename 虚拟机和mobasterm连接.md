---
title: 虚拟机和mobasterm连接
---

未来可能这个教程会被反复的翻阅

我纠结这个东西纠结了好久

现在写一下

首先感谢： https://blog.csdn.net/qq_34623621/article/details/124334267

https://blog.csdn.net/u013215852/article/details/80526706



## 0. 前言

这个连接 主要看两个东西

第一个看 虚拟机

[![](https://s1.ax1x.com/2022/06/24/jFU2wQ.png)](https://imgtu.com/i/jFU2wQ)

[![](https://s1.ax1x.com/2022/06/24/jFURoj.png)](https://imgtu.com/i/jFURoj)

即看是不是在同一网段内

第二个 看 虚拟机的ssh服务是不是安装着

```shell
# ssh 状态
service sshd status
# 安装ssh
apt install openssh-server
# 关闭防火墙
ufw disable
```

我在安装ssh时候出现了错误

感谢： https://blog.csdn.net/u013215852/article/details/80526706

解决方式：

```shell
sudo apt-get  autoremove  openssh-client

sudo apt-get install openssh-client openssh-server

sudo /etc/init.d/ssh start
# ssh 状态
service sshd status
```

 ok 这时候 应该就能快乐连接了