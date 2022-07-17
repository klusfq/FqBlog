---
title: PHP-PoloDB系列之PLDBValue结构
categories: []
date: 2022-07-17 18:25:00
tags:
- PHP
- Rust
---


## 背景

上文提到，在导出的动态库中，并没有实现头文件所申明的那些 `PLDBValue` 结构相关的接口。

本文主要基于以下几个问题和目标展开：
1. 实现 `PLDBValue` 相关（创建、销毁）的接口；
2. 研究这个结构在数据库底层的具体用途；


## `PLDBValue` 结构研究

![](/images/PLDBValue.jpg)


