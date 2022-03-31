---
title: Nginx+PHP环境搭建笔记
date: 2020-06-26 00:40:16
tags:
- Nginx
- PHP
---

### nginx安装

```bash
// 下载
wget http://nginx.org/download/nginx-1.16.1.tar.gz

// 解压
tar -xf nginx-1.16.1.tar.gz
cd nginx-1.16.1

// (Ubuntu 18.04.4 LTS)依赖环境
    - sudo apt-get install libpcre3-dev
    - sudo apt-get install zlib1g-dev
    - sudo apt-get install libssl-dev

// 安装
./configure --with-http_ssl_module
    // -- 此命令完成后会出现如下输出
    // Configuration summary
    //   + using system PCRE library
    //   + using system OpenSSL library
    //   + using system zlib library

make
sudo make install
```
<!-- more -->

### 环境配置
> 源码安装的nginx是无法直接用service或者systemctl来控制服务的（如有必要，可自己书写控制脚本）

关于启动`nginx`时遇到的<font color="red">403</font>问题：
1. 设置`location`模块的`root`路径中，所有目录必须至少是<font color="#0066ff"> 755 </font>权限
2. 所有的静态资源必须至少是<font color="#0066ff"> 644 </font>权限



### nginx访问php-fpm的sock文件，无权限问题
```bash
# err_log
... fpm.sock failed (13: Permission denied) ...
```
相关问题：
- 访问用户权限
- 文件可访问权限

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
