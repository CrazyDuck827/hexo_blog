---
title: PHP-FPM问题合集
date: 2019-12-01 15:30:39
toc: true
categories: 
    - Linux
tags:
    - Linux
    - PHP
    - PHP-FPM
keywords: 
    - PHP-FPM
    - PHP
    - PHP-FPM问题
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/RPAIEAjwLkmZR1fayU9gQD3xEJzoBhJ3.jpg
---

### linux PHP修改文件上传大小配置

#### 解决方案

##### 1. 修改文件上传大小配置
  - vim php.ini
    ```
        max_execution_time = 600 ;每个PHP页面运行的最大时间值(秒)，默认30秒
        max_input_time = 600 ;每个PHP页面接收数据所需的最大时间，默认60秒
        memory_limit = 8m ;每个PHP页面所吃掉的最大内存，默认8M
        把上述参数修改后，在网络所允许的正常情况下，就可以上传大体积文件了
        max_execution_time = 600
        max_input_time = 600
        memory_limit = 32m
        file_uploads = on
        upload_tmp_dir = /tmp
        upload_max_filesize = 32m
        post_max_size = 32m
    ```
  - 重启
  - 不行的话修改下nginx.conf上传文件大小和请求时间
    
##### 2. 添加项目目录到指定位置
  - 找到fastcgi.conf位置，一般在/usr/local/nginx/conf/fastcgi.conf。
  - 将项目目录添加到fastcgi_param PHP_ADMIN_VALUE这一行，fastcgi_param PHP_ADMIN_VALUE “open_basedir=……..”，重启nginx即可。

#### 参考资料
[linux PHP修改文件上传大小配置](https://blog.csdn.net/qq_23013025/article/details/83653495)

-----------------------

### php-fpm开启报错-ERROR: An another FPM instance seems to already listen on /tmp/php-cgi.sock

#### 原因分析
- php-fpm已启动，但未绑定端口

#### 解决方案

##### 修改配置文件
  - vim php-fpm.conf
    ```
        [www]
        listen = /tmp/php-cgi.sock   //将此处替换成127.0.0.1:9000
        listen.backlog = -1
        listen.allowed_clients = 127.0.0.1
        listen.owner = www
        listen.group = www
    ```
  - vim nginx.conf
    ```
        location ~ [^/]\.php(/|$) {
               fastcgi_pass unix:/tmp/php-cgi.sock;    //将此处替换成127.0.0.1:9000
               fastcgi_index index.php;
               fastcgi_param SCRIPT_FILENAME     $document_root$fastcgi_script_name;
               include fastcgi_params;
        }
        location / {
              root   html;
              index index.php index.html index.htm;
        }
    ```
  - 重启

#### 参考资料
[php-fpm开启报错-ERROR: An another FPM instance seems to already listen on /tmp/php-cgi.sock](https://www.cnblogs.com/wt645631686/p/8377159.html)
