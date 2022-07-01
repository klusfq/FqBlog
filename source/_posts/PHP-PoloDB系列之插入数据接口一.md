---
title: PHP-PoloDB系列之插入数据接口一
categories: []
date: 2022-06-29 16:30:00
tags:
- PHP
- Rust
---

## 概述

本文，将开始研究Polodb新增数据功能的实现。

<!-- more -->

```c
int PLDB_insert(Database* db, uint32_t col_id, uint32_t meta_version, DbDocument* doc);
```

以上接口的参数，涉及到
- 两个结构体：`Database *db` 和 `DbDocument *doc`;
- 无符号32位整型，用来表示 `col_id` 和 `meta_version`;

## 创建`Database`

在头文件中查找结构体关键字，发现有两个函数：
1. 第一个函数`PLDB_open`，接受文件路径作为参数，然后返回一个指向Database的指针；
2. 第二个函数`PLDB_close`，接受一个指向Database的指针，无返回值；

这种封装方式叫`opaque`，就是你不用关注Database结构体的具体内部实现，只需要把它作为参数传入相应提供的接口函数中，当然在使用完以后，需要手动调用释放资源的函数。

这种封装方式往往是成对存在的，如果用面向对象的方式去理解，就是【构造函数】和【析构函数】。

```c
// -- 生成
Database* PLDB_open(const char* path);

// -- 释放
void PLDB_close(Database* db);
```


## 创建`DbDocument`

类似，在头文件中通过该类型的搜索关键字来找到相关接口函数。

我们发现这个结构体相关的接口函数有点多，而且还涉及到一个结构体`PLDBValue`。

简单说明一下，这个结构体顾名思义，就是`mongodb`【文档】的概念。
那么用关系型数据库理解，就是【行】； 而一行数据可以有多个列/字段/域，所以就理解成是【字段】的容器。
结构体`PLDBValue`自然就是【字段】这一实体的表示。

再来看多出来的三个接口：`PLDB_doc_set()`，`PLDB_doc_get()`和`PLDB_doc_len()`，就很容易理解了。
表示的功能就是：
1. 给一个文档设置字段；
2. 从一个文档读取字段；
3. 获取这个文档字段的数量；

```c
DbDocument* PLDB_mk_doc();

void PLDB_free_doc(DbDocument* doc);

int PLDB_doc_set(DbDocument* doc, const char* key, PLDBValue val);

int PLDB_doc_get(DbDocument* doc, const char* key, PLDBValue* out_val);

int PLDB_doc_len(DbDocument* doc);
```


## 创建`PLDBValue`

关于PLDBValue结构体相关操作，头文件中也给定了以下三个功能函数。

> 但是，很坑的是貌似作者并没有实现这几个方法！！！

首先，`PLDB_mk_binary_value()`这个函数接收一个字节串和一个长度参数，然后返回一个PLDBValue结构体（我其实不是很理解为啥要在栈中保留这个结构体，而不是像我们已知的那些结构体一样放在堆上）。

然后，`PLDB_dup_value()`这个方法，貌似就是为了复制PLDBValue用的。

最后，`PLDB_free_value()`我最迷惑的就是这个函数。PLDBValue的生命周期，应该和它所在栈上下文一样吧，那么这个函数究竟是干啥呢？提前销毁这个变量吗？那只是销毁这个变量，还是连变量指向的数据也一起释放了呢？

```c
PLDBValue PLDB_mk_binary_value(const char* bytes, uint32_t size);

PLDBValue PLDB_dup_value(PLDBValue val);

void PLDB_free_value(PLDBValue val);
```

仔细阅读头文件，我还发现了PLDBValue相关的几个宏
```c
#define PLDB_NULL { PLDB_VAL_NULL, { .int_value = 0 } }
#define PLDB_INT(x) { PLDB_VAL_INT, { .int_value = (x) } }
#define PLDB_DOUBLE(x) { PLDB_VAL_DOUBL, { .double_value = (x) } }
#define PLDB_BOOL(x) { PLDB_VAL_BOOLEAN, { .bool_value = !!(x) ? 1 : 0 } }
```