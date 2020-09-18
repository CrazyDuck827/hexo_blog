---
title: Nginx问题合集
date: 2019-11-28 19:30:39
toc: true
categories: 
    - Linux
tags:
    - Linux
    - Nginx
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/zAMpLeDInvEZkBW7eSKlgOpKWd7suxx1.png
---

### Nginx服务器偶尔出现 No input file specified.

#### 原因分析
- Nginx出于安全考虑，默认开启了防跨目录设置

#### 解决方案
- 直接#注释
    - 找到fastcgi.conf位置，一般在/usr/local/nginx/conf/fastcgi.conf。
    - 将fastcgi_param PHP_ADMIN_VALUE这一行#注释，重启nginx即可。
    
- 添加项目目录到指定位置
    - 找到fastcgi.conf位置，一般在/usr/local/nginx/conf/fastcgi.conf。
    - 将项目目录添加到fastcgi_param PHP_ADMIN_VALUE这一行，fastcgi_param PHP_ADMIN_VALUE “open_basedir=……..”，重启nginx即可。

#### 参考资料
[Nginx服务器出现 No input file specified](https://blog.csdn.net/resilient/article/details/89234482)

-----------------------

### Nginx下出现Access denied (403) see security.limit_extensions

#### 原因分析
- 配置文件问题

#### 解决方案
- 修改配置文件
    - 在你php-fpm配置文件php-fpm.conf中设置security.limit_extensions 为 .php 或 .php5，或者其他任何与你环境一致的后缀名。 对于开发环境下的一些用户来说, 完全移除所有security.limit_extensions的值或设置为FALSE，能够保证可以正常工作
    - 在你的nginx配置文件中设置fastcgi_pass 为你的socket地址(e.g. unix:/var/run/php-fpm/php-fpm.sock;)， 替代ip地址:端口这种方式(127.0.0.1:9000)
    - 检查你的SCRIPT_FILENAME, fastcgi_param 并根据你文件的地址来设置它们.
    - 在你的nginx配置文件中包含有fastcgi_split_path_info ^(.+.php)(/.+)$; 则所有其他的对应fastcgi参数也都应该在location块中定义；具体可参考phalcon的nginx官方配置
    - 在你的php.ini配置文件中，设置cgi.fix_pathinfo=1
    - 一般是最后一点原因

#### 参考资料
[Nginx下出现Access denied (403) see security.limit_extensions错误的解决方法](https://blog.csdn.net/u012129607/article/details/62042169)
