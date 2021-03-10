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

### I/O模型
#### 概念：
- I/O：输入/输出（I/O）是在主存和外部设备（例如磁盘驱动器、终端和网络）之间复制数据的过程。
   - 输入操作：是从 I/O 设备复制数据到主存
   - 输出操作：数据是从主存复制数据到 I/O 设备
- 同步：在发出一个调用之后，调用没有得到结果之前该调用不返回。即同步是主动等待消息
- 异步：调用不会等待结果而是立即返回，然后等待被调用者使用消息、通知或者回调函数来通知调用者。即异步是被动接收消息
- 阻塞：指程序在等待结果的时候被挂起，不能去完成别的任务【浪费时间】
- 非阻塞：指程序在等待的过程中可以做别的事情【需要切换开销】
- 同步IO和异步IO的区别就在于：数据拷贝的时候进程是否阻塞
- 阻塞IO和非阻塞IO的区别就在于：应用程序的调用是否立即返回

#### 分类：
- 同步阻塞 I/O：在内核等待数据和将数据复制到进程地址空间的两个过程，除了等待啥也不做。及时返回数据，无延迟
- 同步非阻塞 I/O：在内核等待数据的阶段，进程可以轮询（瞅瞅它准备好数据了没），后一阶段等待。此外，在轮询之外的时间可以干其他活儿了，但是这样会拉长此进程的时延（也许人家在你轮询之前准备好了）
- I/O 复用(select 和 poll)：不停地查看多个任务的完成状态，只要有任何一个任务完成了，就去处理它
   - 多路：多个连接
   - 复用：一个或少量线程。即使用一个或少量的线程去处理多个连接
   - 三种模式
      - select：轮询，任何一个进程的数据准备好了就来通知一声，限制只能同时监视1024个接口；
      - poll：和select一样，不过去掉了1024的限制；
      - epoll：回调，不用去轮询了。
- 信号驱动 I/O：建立SIGIO信号处理函数，数据准备好后进程会收到SIGIO信号。可以在信号处理函数中调用IO操作函数处理数据
- 异步 I/O：异步模式时被动接收消息，如通过回调、通知、状态等方式被动获取；不是顺序执行。异步非阻塞IO中，用户进程进行系统调用后，无论内核是否准备好都会返回响应，进程可以去做别的事情，内核复制好数据之后会通知进程

----- 

### 常见面试问题
#### 什么是Nginx？
> Nginx是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。

#### nginx的四大功能是什么？
> 1. 正向代理
    在客户端(浏览器)配置代理服务器，通过代理服务器进行互联网访问。
> 2. 反向代理
    我们只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器,暴露的是代理服务器地址，隐藏了真实服务器IP地址。
> 3. 负载均衡
    单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡。
> 4. 动静分离
    为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度。降低原来单个服务器的压力。

#### nginx和apache的区别？
> - 轻量级：同样起web 服务，nginx 比apache 占用更少的内存及资源；
> - 抗并发：nginx处理请求是异步非阻塞的，而apache 则是阻塞型的，在高并发下nginx 能保持低资源低消耗高性能；
> - 模块化：nginx编写模块相对简单；
> - IO事件模型：apache是同步多进程模型，一个连接对应一个进程，阻塞；nginx是异步的，多进程单线程，异步非阻塞，多个连接（万级别）可以对应一个进程。
> - 功能丰富：Nginx提供负载均衡，可以做做反向代理，前端服务器

#### nginx报500、502、503、504 有什么区别？
> - 500：Internal Server Error 内部服务错误，比如脚本错误，编程语言语法错误。
> - 502：Bad Gateway错误，网关错误。比如服务器当前连接太多，响应太慢，页面素材太多、带宽慢。
> - 503：Service Temporarily Unavailable，服务不可用，web服务器不能处理HTTP请求，ip超频访问导致限流，或者临时超载或者是服务器进行停机维护。
> - 504：Gateway timeout 网关超时，程序执行时间过长导致响应超时，例如程序需要执行20秒，而nginx最大响应等待时间为10秒，这样就会出现超时。

#### Nginx的负载均衡算法都有哪些？
> 1. 轮询（默认）
  每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
> 2. weight
  指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。
> 3. ip_hash
  每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。
> 4. fair（第三方）
  按后端服务器的响应时间来分配请求，响应时间短的优先分配。
> 5. url_hash（第三方）
  根据url的hash结果分配

#### Nginx的 反向代理和正向代理怎么理解？
> 正向代理：局域网中的电脑用户想要直接访问网络是不可行的，只能通过代理服务器来访问，这种代理服务就被称为正向代理。
> 反向代理：客户端无法感知代理，因为客户端访问网络不需要配置，只要把请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据，然后再返回到客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器IP地址
> 正向代理服务器代理的是客户端，而反向代理服务器代理的是服务端

#### 为什么Nginx性能这么高？
> 异步非阻塞事件处理机制：运用了epoll模型，提供了一个队列，排队解决

#### 为什么Nginx不使用多线程？
> Nginx:采用单线程来异步非阻塞处理请求（管理员可以配置Nginx主进程的工作进程的数量），不会为每个请求分配cpu和内存资源，节省了大量资源，同时也减少了大量的CPU的上下文切换，所以才使得Nginx支持更高的并发

#### Nginx 压缩了解吗，如何开启压缩？
> 开启nginx gzip压缩后，图片、css、js等静态资源的大小会减小，可节省带宽，提高传输效率，但是会消耗CPU资源。
```
    # 开启gzip
    gzip off;
    # 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
    gzip_min_length 1k;
    # gzip 压缩级别，1-9，数字越大压缩的越好，也越占用CPU时间，后面会有详细说明
    gzip_comp_level 1;
    # 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png application/vnd.ms-fontobject font/ttf font/opentype font/x-woff image/svg+xml;
```

#### nginx是如何实现高并发的？
> 简单来说：异步，非阻塞，使用了epoll和大量的底层代码优化
> 详细来说分两个模块：
> 1. 进程模型
>    nginx采用一个master进程，多个woker进程的模式
>   - master进程主要负责收集、分发请求。当一个请求过来时，master拉起一个worker进程负责处理这个请求
>   - master进程也要负责监控woker的状态，保证高可靠性
>   - woker进程一般设置为跟cpu核心数一致。nginx的woker进程跟apache不一样。apche的进程在同一时间只能处理一个请求，所以它会开很多个进程，几百甚至几千个。而nginx的woker进程在同一时间可以处理额请求数只受内存限制，因此可以处理多个请求
> 2. 事件模型
>    nginx是异步非阻塞的
>    每进来一个request，会有一个worker进程去处理。但不是全程的处理，处理到可能发生阻塞的地方，比如向上游（后端）服务器转发request，并等待请求返回。那么，这个处理的worker不会这么傻等着，他会在发送完请求后，注册一个事件：“如果upstream返回了，告诉我一声，我再接着干”。于是他就休息去了。此时，如果再有request 进来，他就可以很快再按这种方式处理。而一旦上游服务器返回了，就会触发这个事件，worker才会来接手，这个request才会接着往下走。

#### nginx怎么结合php处理动态请求？
> Nginx不支持对外部程序的直接调用或者解析，所有的外部程序（包括PHP）必须通过FastCGI接口来调用
> 1. FastCGI接口在Linux下是socket，（这个socket可以是文件socket，也可以是ip socket）。为了调用CGI程序，还需要一个FastCGI的wrapper（wrapper可以理解为用于启动另一个程序的程序），这个wrapper绑定在某个固定socket上，如端口或者文件socket。
> 2. 当Nginx将CGI请求发送给这个socket的时候，通过FastCGI接口，wrapper接纳到请求，然后派生出一个新的线程，这个线程调用解释器或者外部程序（php-fpm）处理脚本并读取返回数据
> 3. 接着，wrapper再将返回的数据通过FastCGI接口，沿着固定的socket传递给Nginx；
> 4. 最后，Nginx将返回的数据发送给客户端，这就是Nginx+FastCGI的整个运作过程。

#### 作为负载均衡，lvs和nginx有什么区别？
> lvs：linux虚拟服务器
>负载度：    lvs 优于 nginx 
 稳定度：    lvs 优于 nginx
 服务器性能要求： lvs 优于 nginx
 网络层数的效率： lvs 优于 nginx（网络七层：应用层、会话层、表示层、传输层、网络层、链路层、 物理层）
 功能多少 ：  nginx 优于 lvs
 nginx工作在网络的第7层，lvs工作在第四层。所以nginx可以针对http应用本身来做分流策略，比如针对域名、目录结构等，相比之下lvs并不具备这样的功能。





