---
title: Nginx反向代理
date: 2019-09-07 09:25:00
toc: true
author: CrazyDuck827
categories: Nginx
tags:
  - Nginx
  - Linux
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/zAMpLeDInvEZkBW7eSKlgOpKWd7suxx1.png
---

# Nginx反向代理

### 概念
> 反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

### 正向代理与反向代理
- 引入如下两张理解：

![正向代理和反向代理 图1](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx1.jpg)

![正向代理和反向代理 图2](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx2.png)

- 正向代理:客户端 <-> 代理 ->服务端

        正向代理简单地打个租房的比方:
        
        A(客户端)想租C(服务端)的房子,但是A(客户端)并不认识C(服务端)租不到。
        B(代理)认识C(服务端)能租这个房子所以你找了B(代理)帮忙租到了这个房子。
        
        这个过程中C(服务端)不认识A(客户端)只认识B(代理)
        C(服务端)并不知道A(客户端)租了房子，只知道房子租给了B(代理)。
        
- 反向代理:客户端 ->代理 <-> 服务端

        反向代理也用一个租房的例子:
        
        A(客户端)想租一个房子,B(代理)就把这个房子租给了他。
        这时候实际上C(服务端)才是房东。
        B(代理)是中介把这个房子租给了A(客户端)。
        
        这个过程中A(客户端)并不知道这个房子到底谁才是房东
        他都有可能认为这个房子就是B(代理)的
        
### 配置演示

> 正向代理

首先在A服务器的nginx设置访问控制
访问控制之前我访问A下的test.html是这样的:

![正向代理和反向代理3](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx3.png)

打开nginx.conf
设置访问A的IP不是118.126.106.11(我的B服务器)则返回403

```
    location / {
        if ( $remote_addr !~* "^118\.126\.106\.11") {
            return 403;
        }
        root   /opt/app/demo/html;
        index  index.html index.htm;
    }
```

添加后reload一下nginx再访问test.html:

![正向代理和反向代理4](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx4.png)

此时本地我的浏览器就是被限制了,访问不了该资源。

现在登录上B服务器,打开nginx.conf

添加resolver和proxy_pass,设置如下:

```
server {
    listen       80;
    server_name  localhost nginx.tangll.cn;

    resolver 8.8.8.8;
    location / {
        proxy_pass http://$http_host$request_uri;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

resolver为DNS解析,这里填写的IP为Google提供的免费DNS服务器的IP地址

proxy_pass配置代理转发

至此便是配置了B服务器所有访问根一级的请求全部都代理转发对应到$http_host$request_uri去了,$http_host就是我们要访问的主机名,$request_uri就是我们后面所加的参数。

简单的说至此就是相当于配置好了我们请求了B服务器,B服务器再去请求我们所请求的地址。

那么来看一下结果：

添加网络代理：

![正向代理和反向代理5](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx5.png)

登录http://www.ip138.com/ 可以看到此时我们的IP地址已经为B服务器的IP,说明代理成功。

![正向代理和反向代理6](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx6.png)

然后再来访问一下test.html:

![正向代理和反向代理7](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx3.png)

结果证明,此时的客户端已经可以成功访问A服务器的资源。
    
> 以上就是正向代理的一个场景演示,这个过程中可以知道,我们客户端是想要A的资源,但是A的资源只有B能拿到,便让B代理去帮助我们访问A的资源。整个过程A只知道B拿了他的资源,并不知道客户端拿到。
    
> 反向代理

首先在/etc/nginx/conf.d/下新建一个test.conf:

```
server {
    listen       8080;
    server_name  localhost nginx.tangll.cn;

    location / {
        root   /opt/app/demo/html;
        index  index.html index.htm;
    }

    error_page   500 502 503 504 404  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

可以看到server里listen的是8080端口,但是服务器本身不对外开放8080端口,只开放了80端口。

所以我们此时访问test.html结果是访问不到的:

![正向代理和反向代理8](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx7.png)

然后打开nginx.conf
添加proxy_pass设置如下:

```
server {
    listen       80;
    server_name  localhost nginx.tangll.cn;
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
  
    #设置代理
    #location ~ /test.html$ {
    #    proxy_pass http://127.0.0.1:8080;
    #}

    error_page   500 502 503 504 404  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

设置当匹配test.html结尾的URL时就去代理访问本机的8080端口

为了对比先注释掉,然后直接80端口访问一下test.html:

![正向代理和反向代理9](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx8.png)

可以看到此时返回的404。

这时候取消注释reload一下nginx然后用80端口访问test.html

![正向代理和反向代理10](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/nginx3.png)

此时便可访问8080端口配置的资源。

> 以上便是完成了一个反向代理的演示,这个过程中我们可以知道,客户端想要访问的是test.html,但是test.html实际上是8080端口下配置的,中间经过了代理才能拿到。也就是说客户端并不知道中间经历了什么代理过程,只有服务端知道。客户端只知道他拿到了test.html也就是8080端口下配置的资源内容。

### 步骤

>上述测试使用Nginx进行反向代理HTTP请求实质有经过几个步骤。首先，访问网站后，会进入hosts文件，寻到对应访问的IP地址（节省去了域名解析的时间）。而后请求变为127.0.0.1:80，而后借助Nginx中的代理配置，将请求代理到端口为8089的端口，即完成使用域名对127.0.0.1:8089的访问。
    
### 总结

 - 代理服务器站在客户端那边就是正向代理，

 - 代理服务器站在原始服务器那边就是反向代理,

 - Nginx通过proxy_pass可以设置代理服务。
 
### 备注

 - 一个需要注意点就是DNS解析是有缓存的，可能导致访问反向代理不生效，可以使用ipconfig /flushdns 刷新一下本地 dns 缓存

### 附录

```
#运行用户
#user somebody;
 
#启动进程,通常设置成和cpu的数量相等
worker_processes  1;
 
#全局错误日志
error_log  D:/Tools/nginx-1.10.1/logs/error.log;
error_log  D:/Tools/nginx-1.10.1/logs/notice.log  notice;
error_log  D:/Tools/nginx-1.10.1/logs/info.log  info;
 
#PID文件，记录当前启动的nginx的进程ID
pid        D:/Tools/nginx-1.10.1/logs/nginx.pid;
 
#工作模式及连接数上限
events {
    worker_connections 1024;    #单个后台worker process进程的最大并发链接数
}
 
#设定http服务器，利用它的反向代理功能提供负载均衡支持
http {
    #设定mime类型(邮件支持类型),类型由mime.types文件定义
    include       D:/Tools/nginx-1.10.1/conf/mime.types;
    default_type  application/octet-stream;
    
    #设定日志
    log_format  main  '[$remote_addr] - [$remote_user] [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
                      
    access_log    D:/Tools/nginx-1.10.1/logs/access.log main;
    rewrite_log     on;
    
    #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，对于普通应用，
    #必须设为 on,如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，以平衡磁盘与网络I/O处理速度，降低系统的uptime.
    sendfile        on;
    #tcp_nopush     on;
 
    #连接超时时间
    keepalive_timeout  120;
    tcp_nodelay        on;
    
    #gzip压缩开关
    #gzip  on;
 
    #设定实际的服务器列表 
    upstream zp_server1{
        server 127.0.0.1:8089;
    }
 
    #HTTP服务器
    server {
        #监听80端口，80端口是知名端口号，用于HTTP协议
        listen       80;
        
        #定义使用www.xx.com访问
        server_name  www.helloworld.com;
        
        #首页
        index index.html
        
        #指向webapp的目录
        root D:\01_Workspace\Project\github\zp\SpringNotes\spring-security\spring-shiro\src\main\webapp;
        
        #编码格式
        charset utf-8;
        
        #代理配置参数
        proxy_connect_timeout 180;
        proxy_send_timeout 180;
        proxy_read_timeout 180;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarder-For $remote_addr;
 
        #反向代理的路径（和upstream绑定），location 后面设置映射的路径
        location / {
            proxy_pass http://zp_server1;
        } 
 
        #静态文件，nginx自己处理
        location ~ ^/(images|javascript|js|css|flash|media|static)/ {
            root D:\01_Workspace\Project\github\zp\SpringNotes\spring-security\spring-shiro\src\main\webapp\views;
            #过期30天，静态文件不怎么更新，过期可以设大一点，如果频繁更新，则可以设置得小一点。
            expires 30d;
        }
    
        #设定查看Nginx状态的地址
        location /NginxStatus {
            stub_status           on;
            access_log            on;
            auth_basic            "NginxStatus";
            auth_basic_user_file  conf/htpasswd;
        }
    
        #禁止访问 .htxxx 文件
        location ~ /\.ht {
            deny all;
        }
        
        #错误处理页面（可选择性配置）
        #error_page   404              /404.html;
        #error_page   500 502 503 504  /50x.html;
        #location = /50x.html {
        #    root   html;
        #}
    }
}
```

### 参考资料

[Nginx正向代理与反向代理](https://www.jianshu.com/p/ae76c223c6ef)

[Nginx实现HTTP反向代理配置](https://blog.csdn.net/qq_33404395/article/details/80523850)
