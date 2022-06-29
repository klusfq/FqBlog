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

<font style="background-color: yellow;">TODO</font>
