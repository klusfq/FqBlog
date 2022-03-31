---
title: PHP踩坑笔记
categories: []
date: 2020-07-16 13:45:36
tags: 
- PHP
---

### JSON小坑

PHP的 `json_encode()` 和 `json_decode()` 是特别好用的两个函数。

一般来说，复杂数据结构类型才需要转化为json字符串来传输。此时，比较容易忽略的是，当你把一个正常字符串传入 `json_encode()`，会返回什么呢？？？报错？还是不变？

```PHP
$rawStr = "hello world";

$encodeStr = json_encode($rawStr);

echo $encodeStr . PHP_EOL;  // "hello world"

// !!! 请注意，是 `"hello world"`，而不是 `hello world`
```

是的，你没看错，给一个字符串json_encode，<font color="red">它会给这个字符串开头和结尾加上双引号。</font>