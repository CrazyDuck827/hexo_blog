---
title: Linux面试汇总
categories: 
    - Linux
tags:
    - Linux
    - 面试
keywords: 
    - Linux
    - 面试
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/RPAIEAjwLkmZR1fayU9gQD3xEJzoBhJ3.jpg
---

### 基础
#### 基本命令
- 修改目录，文件权限和属主及数组命令
        Chmod：用于改变指定目录或文件的权限命令。
        Chown：用于改变文件拥有属性的命令。
        Chgrp：用于改变文件群组的命令。
        
- 创建和删除目录的命令
        Mkdir：用于创建目录
        Rmdir：用于删除空的目录
        
- 创建和删除，重命名，复制文件的命令
        Touch：创建一个新的文件
        Vi:创建一个新的文件
        Rm：删除文件或目录
        Mv：重命名或移动文件的命令
        Cp：复制命令
        
- 压缩和打包命令
        Tar：用于多个文件或目录进行打包，但不压缩，同时也用命令进行解包
        Zip/unzip:压缩解压缩.zip文件
        
- 查找命令
        Find：查找指定目录或文件的命令。
        Whereis：查找指定的文件源和二进制文件和手册等
        Which：用于查询命令或别名的位置。

- 其他
        Wget：用于下载ftp或http服务器文件到本地。
        Netstat：显示网络连接.路由表和网络接口信息
        Kill： 杀死一些特定的进程
        Ps：显示瞬间进程的动态

### PHP-FPM
#### 进程管理方式
- static静态
    在启动时master按照pm.max_children配置fork出相应数量的worker进程，即worker进程数是固定不变的
    pm.max_children：静态方式下开启的php-fpm进程数量
- dynamic动态
    首先在fpm启动时按照pm.start_servers初始化一定数量的worker，运行期间如果master发现空闲worker数低于pm.min_spare_servers配置数(表示请求比较多，worker处理不过来了)则会fork worker进程，但总的worker数不能超过pm.max_children，如果master发现空闲worker数超过了pm.max_spare_servers(表示闲着的worker太多了)则会杀掉一些worker，避免占用过多资源，master通过这4个值来控制worker数
    pm.start_servers：动态方式下的起始php-fpm进程数量
    pm.min_spare_servers：动态方式下的最小php-fpm进程数量
    pm.max_spare_servers：动态方式下的最大php-fpm进程数量
- Ondemand按需
    在这种方式下，PHP-FPM启动时，不会创建Worker进程，当请求到达的时候Master进程才会fork出子进程。在这种模式下，如果请求量比较大，Master进程会非常繁忙，会占用大量CPU时间。所以这种模式不适合大流量的环境

#### nginx与php-fpm的通信方式
- IP:Port 监听方式（tcp socket）
   - 配置：nginx.conf中填写php-fpm的php-fpm运行的ip地址和端口号
        ```
            #nginx.conf中填写php-fpm的php-fpm运行的ip地址和端口号
            location ~ \.php$ {
              include fastcgi_params;
              fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;;
              fastcgi_pass 127.0.0.1:9000;
              fastcgi_index index.php;
            }
        ```
   - 特点：windows系统只能使用tcp socket的通信方式
    
- UDS 方式监听（unix socket）
   - 配置：nginx.conf中填写php-fpm的pid文件位置
    ```
       location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
      }
     ```
   - 特点：
      - 可以跨服务器，当nginx和php-fpm不在同一台机器上时，只能使用这种方式      
      - unix socket是一种终端，可以使同一台操作系统上的两个或多个进程进行数据通信，效率要比tcp socket高
      
#### 处理FastCGI请求的方式
- ipv4:端口号
- ipv6:端口号
- port相当于 0.0.0.0:port（本机所有ipv4对应的端口号）
- [::]:port（包括ipv4和ipv6）
- unix socket文件

### Nginx
   - 正向代理
   - 反向代理
      - 指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器
        ```
        server {  
            listen       80;                                                        
            server_name  localhost;                                              
            client_max_body_size 1024M;
        
            location / {
                proxy_pass http://localhost:8080;
                proxy_set_header Host $host:$server_port;
            }
        }
        ```
   - 负载均衡
      - 定义：操作分摊到多个操作单元上进行执行
      - 策略
         - RR（默认）：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除
         
         ```
             upstream test {
                 server localhost:8080;
                 server localhost:8081;
             }
             server {
                 listen       81;                                                        
                 server_name  localhost;                                              
                 client_max_body_size 1024M;
          
                 location / {
                     proxy_pass http://test;
                     proxy_set_header Host $host:$server_port;
                 }
             }
         ```
        
         - 权重：指定轮几率，weight和访问比率成正比，用于后端服务器性能不均的情况
         
         ```
          upstream test {
                 server localhost:8080 weight=9;
                 server localhost:8081 weight=1;
          }
         ```
        
         - ip_hash：iphash的每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题
         
         ```
              upstream test {
                      ip_hash;
                      server localhost:8080;
                      server localhost:8081;
               }
         ```
         
         - fair（第三方）：按后端服务器的响应时间来分配请求，响应时间短的优先分配
         
         ```
              upstream backend {
                      fair;
                      server localhost:8080;
                      server localhost:8081;
               }
         ```
          
         - url_hash（第三方）：按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。 在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法
         
         ```
            upstream backend {
                    hash $request_uri;
                    hash_method crc32;
                    server localhost:8080;
                    server localhost:8081;
             }
         ```
          
   - 与apache的区别
      - apache是同步多进程模型，一个连接对应一个进程
      - nginx是异步的，多个连接（万级别）可以对应一个进程
   
   
   