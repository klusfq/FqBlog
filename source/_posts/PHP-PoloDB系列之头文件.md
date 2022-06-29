---
title: PHP-PoloDB系列之头文件
categories: []
date: 2022-06-29 16:12:00
tags:
- PHP
- Rust
---

## 概述

打包成动态链接库提供给php的ffi调用，需要研究头文件：
- 首先，要看提供了哪些api
- 然后，再看api涉及到了哪些结构体
- 最后，再去看这些结构体的具体实现和相应用途

<!-- more -->

综上，这种研究方式叫【自顶向下】，与之对应的是【自底向上】

## 功能接口函数

> 只罗列主要的接口（对应了增、删、改、查的功能需求）；
>
> 后续在php中会对这些功能进行封装，最终形成一个composer包；

```c
// 1. 插入
int PLDB_insert(Database* db, uint32_t col_id, uint32_t meta_version, DbDocument* doc);

// 2. 查找
//
// <query> is nullable
int PLDB_find(Database* db, uint32_t col_id, uint32_t meta_version, const DbDocument* query, DbHandle** out_handle);

// 3. 更新
//
// <query> is nullable
int64_t PLDB_update(Database* db, uint32_t col_id, uint32_t meta_version, const DbDocument* query, const DbDocument* update);

// 4. 删除
int64_t PLDB_delete(Database* db, uint32_t col_id, uint32_t meta_version, const DbDocument* query);

// 5. 删除所有
int64_t PLDB_delete_all(Database* db, uint32_t col_id, uint32_t meta_version);
```


## 结构体

> 只罗列主要的结构体

```c
struct Database;
struct DbHandle;
struct DbDocument;
struct DbDocumentIter;
struct DbArray;
struct DbObjectId;

typedef struct DbDocument DbDocument;
typedef struct DbDocumentIter DbDocumentIter;
typedef struct DbArray DbArray;
typedef struct Database Database;
typedef struct DbHandle DbHandle;
typedef struct DbObjectId DbObjectId;

typedef enum PLDB_VALUE_TYPE {
  PLDB_VAL_NULL = 0x0A,
  PLDB_VAL_DOUBL = 0x01,
  PLDB_VAL_BOOLEAN = 0x08,
  PLDB_VAL_INT = 0x16,
  PLDB_VAL_STRING = 0x02,
  PLDB_VAL_OBJECT_ID = 0x07,
  PLDB_VAL_ARRAY = 0x17,
  PLDB_VAL_DOCUMENT = 0x13,
  PLDB_VAL_BINARY = 0x05,
  PLDB_VAL_UTC_DATETIME = 0x09,
} PLDB_VALUE_TYPE;

typedef struct PLDBValue {
    PLDB_VALUE_TYPE tag: 8;
    union {
        int64_t     int_value;
        double      double_value;
        int         bool_value;
        const char* str;
        DbObjectId* oid;
        DbArray*    arr;
        DbDocument* doc;
        uint64_t    utc;
    } v;
} PLDBValue;
```
