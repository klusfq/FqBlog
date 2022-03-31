---
title: Bash笔记
date: 2022-03-08 20:17:50
tags:
- Linux
---

### `export`命令和直接赋值区别

- `export` 的变量可被子进程继承；
- 直接赋值bash环境变量不能被子进程继承；


### 64位机器编写32位汇编

- 源码开头加上 `.code32`
- `as` 加上 `--32` 选项
- `ld` 加上 `-m elf_i386` 选项


### `ssh` 长连接问题解决

> 服务器/路由器/客户端防火墙会关闭超时空闲链接

- 客户端配置
```bash
# TCPKeepAlive=yes
# Client每隔 60 秒发送一次请求给 Server，然后 Server响应，从而保持连接
ServerAliveInterval 60
# Client发出请求后，服务器端没有响应得次数达到3，就自动断开连接，正常情况下，Server 不会不响应
ServerAliveCountMax 3
```
- 服务端配置
```bash
# Server 每隔 60 秒发送一次请求给 Client，然后 Client响应，从而保持连接
ClientAliveInterval 60
# Server发出请求后，客户端没有响应得次数达到 10，就自动断开连接，正常情况下，Client不会不响应
ClientAliveCountMax 10
```
