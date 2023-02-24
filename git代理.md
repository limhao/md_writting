---
title: git代理
---

这个问题碰到很多次了

每次安装git的时候都需要查查

```shell
# 设置代理
git config --global http.proxy 'socks5://127.0.0.1:1080' 
git config --global https.proxy 'socks5://127.0.0.1:1080'
# 查看代理
git config --global --get http.proxy
git config --global --get https.proxy
# 取消代理：
git config --global --unset http.proxy
git config --global --unset https.proxy
```

然后 在软件里面设置一下就好了

![](https://pic.imgdb.cn/item/63de549f4757feff3371016b.png)

# 等等

刚刚贴了张图才意识到的问题

图中的协议为socks协议

我之前找到的教程 很可能是不符合协议 才可能设置不清不楚的

```shell
git config --global https.proxy http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890
```

# 总结

1. 协议要对的（这次犯得错误）
2. 端口号要对的