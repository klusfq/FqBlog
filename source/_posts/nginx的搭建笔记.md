---
title: nginx的搭建笔记
categories: []
date: 2020-04-04 20:03:36
tags: 
- Nginx
---

### 安装

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
{% blockquote %}
源码安装的nginx是无法直接用service或者systemctl来控制服务的（如有必要，可自己书写控制脚本）
{% endblockquote %}

关于启动`nginx`时遇到的<font color="red">403</font>问题：
1. 设置`location`模块的`root`路径中，所有目录必须至少是<font color="blue"> 755 </font>权限
2. 所有的静态资源必须至少是<font color="blue"> 644 </font>权限

### nginx项目配置文件

