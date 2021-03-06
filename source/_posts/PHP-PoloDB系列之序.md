---
title: PHP-PoloDB系列之序
categories: []
date: 2022-05-01 14:55:12
tags: 
- PHP
- Rust
---

## 一些想法
学习rust有一段时间了，现在感觉遇到了瓶颈。为了更进一步，有必要接触具体的项目。

在网上发现一个用rust实现DB的项目：https://github.com/vincentdchan/PoloDB

简略地看了它的源码，但是仅仅看还不得要领，所以决定为这个库实现一个php的ffi绑定，众所周知php已经出到8.1版本了，对于FFI的支持已经很完善了。

<!-- more -->

本系列的主要目的有三个：
1. 学习rust在项目中的实际应用；
2. 学习B+树的具体实现；
3. 学习php的ffi功能；

思路：
```bash
rust -> FFI -> 动态链接库 -> FFI -> PHP
```


最终目标：
1. 首先，实现一个php的库，支持基本的增、删、改、查操作，并将该库发布到composer；
2. 然后，将项目中rust的实践按功能整理成文档，能够与理论知识相结合，加深理解；
3. 最后，自己用php实现一颗B+树；


## 汇总

`PoloDB`项目是仿照MongoDB去实现的接口，所有有些概念需要提前认知一下：
![](/images/mongodb_concept.png)
