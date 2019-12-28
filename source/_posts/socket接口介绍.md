---
title: socket接口介绍
date: 2019-10-11 23:40:15
tags:
     - socket
     - Linux
---

# 1.socket的创建
```cpp

int socket(int domain, int type, int protocol);
```
dmain --- 指定底层协议（AF_INET）
type  --- 套接字类型（STREAM/DGRAM）
protocol --- 指定具体协议（Tcp -6，Tcp -17，0 -默认协议）
返回值 --- 文件描述符--->套接字操作句柄

# 2.绑定端口号
```cpp
int bind(int socket, const struct sockaddr *address,
 socklen_t address_len);
```
socket --- socket描述符
address --- IP地址
address_len --- 地址长度

# 3.监听
