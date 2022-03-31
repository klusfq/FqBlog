---
title: 记一次nginx+php环境搭建笔记
date: 2020-06-26 00:40:16
tags: Nginx
---

### nginx访问php-fpm的sock文件，无权限问题
```bash
# err_log
... fpm.sock failed (13: Permission denied) ...
```
相关问题：
- 访问用户权限
- 文件可访问权限

<!-- more -->

### `$fastcgi_script_name` 的含义问题

```bash
# err_log
FastCGI sent in stderr: "Primary script unknown" while reading response header from upstream ...
```
相关问题：
- nginx的`location`模块中，`root`和`alias`关键字的区别
- `fastcgi_pass` 和 `fastcgi_param` 项的值

### 关于一个nginx重定向实例

- Before：`https://klusfq.cn/api/module/xxx?yyy`

- After：`https://klusfq.cn/module/index.php/xxx?yyy`

```bash
rewrite ^/api/([^/.]*)(/[^\?]*)?((\?.*)?)$ /$1/index.php$2$3 break;

# 解析: 
#       ([^/.]*)    ->  module -> $1
#       (/[^\?]*)?  ->  /xxx   -> $2
#       ((\?.*)?)   ->  ?yyy   -> $3
```
