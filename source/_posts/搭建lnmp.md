---
title: Linux 搭建 Nginx & Mysql & PHP 环境
date: 2019-08-12 14:49:33
toc: true
categories: 
    - Linux
tags: 
    - Linux
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/P6Cqpi8ypvau1mSLeA4NledwC281Z3s7.jpg
---

### Nginx
> Nginx官网下载地址：[http://nginx.org/en/download.html](http://nginx.org/en/download.html)

- 安装依赖：
```
    yum -y install gcc zlib* pcre*openssl* wget gcc-c++ tree
```

- 官网下载源码包：
```
    cd /usr/local/src
    wget http://nginx.org/download/nginx-1.17.3.tar.gz
    tar -zxvf nginx-1.17.3.tar.gz
```

- 安装Nginx：
```
    cd /usr/local/src/nginx-1.17.3
    ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre
    make && make install
```

- 配置Nginx：
```
    vi /usr/local/nginx/conf/nginx.conf
    // 大致修改如下：
    worker_processes  auto;
    server {
            listen       80;
            server_name  localhost;
            root         /data/www/default;
    
            location / {
                index  index.html index.htm index.php;
                if ( -f $request_filename) {
                   break;
                }
                if ( !-e $request_filename) {
                   rewrite ^(.*)$ /index.php/$1 last;
                   break;
                }
            }
    
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
    
            // php解析
            location ~ \.php {
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                fastcgi_split_path_info ^(.+\.php)(.*)$;
                fastcgi_param PATH_INFO $fastcgi_path_info;
                fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include        fastcgi_params;
            }
        }
```

- 启动Nginx：
```
    # 启动
    /usr/local/nginx/sbin/nginx
    # 查看nginx进程是否启动
    ps -ef | grep nginx
```

- 一键shell：
```
    yum -y install gcc zlib* pcre*openssl* wget gcc-c++ tree
    cd /usr/local/src
    NGINXVERSION=1.17.3
    wget http://nginx.org/download/nginx-${NGINXVERSION}.tar.gz
    tar -zxvf nginx-${NGINXVERSION}.tar.gz && cd nginx-${NGINXVERSION}
    ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre
    make && make install
    rm -f /usr/local/nginx/conf/nginx.conf && touch /usr/local/nginx/conf/nginx.conf && echo '/
    #user  nobody;
    worker_processes  auto;
    worker_cpu_affinity auto;
    worker_rlimit_nofile 65535;
    
    #error_log  logs/error.log;
    error_log  logs/error.log  notice;
    #error_log  logs/error.log  info;
    
    #pid        logs/nginx.pid;
    
    events {
        worker_connections  1024;
    }
    
    http {
        include       mime.types;
        default_type  application/octet-stream;
    
        client_max_body_size   20m;
    
        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
        #                  '$status $body_bytes_sent "$http_referer" '
        #                  '"$http_user_agent" "$http_x_forwarded_for"';
    
        #access_log  logs/access.log  main;
    
        sendfile        on;
        #tcp_nopush     on;
    
        #keepalive_timeout  0;
        keepalive_timeout  65;
    
        #gzip  on;
    
        #include     vhost/*.conf;
        server {
            listen       80;
            server_name  localhost;
            rewrite ^(.*)$  https://$host$1 permanent;
        }
        server {
            listen       443 ssl;
            server_name  localhost;
            root         /data/www/default;
    
            ssl                  on;
            ssl_certificate      ./ssl/xzky.pem;
            ssl_certificate_key  ./ssl/xzky.key;
            #ssl_client_certificate ca.crt;
            #ssl_verify_client on;
            ssl_session_timeout  5m;
            ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
            ssl_ciphers  ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
            ssl_prefer_server_ciphers   on;
    
            location / {
                index  index.html index.htm index.php;
                if ( -f $request_filename) {
                   break;
                }
                if ( !-e $request_filename) {
                   rewrite ^(.*)$ /index.php/$1 last;
                   break;
                }
            }
    
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
    
            location ~ \.php {
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                fastcgi_split_path_info ^(.+\.php)(.*)$;
                fastcgi_param PATH_INFO $fastcgi_path_info;
                fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include        fastcgi_params;
            }
        }
    }' > /usr/local/nginx/conf/nginx.conf
    /usr/local/nginx/sbin/nginx
```

- 备注：

    1. Q：关于Linux下Nginx服务启动，通过浏览器无法访问的问题？
       A：
          ```
              # 防火墙没有开启，添加 Nginx 端口配置，如80
              firewall-cmd --zone=public --add-port=80/tcp --permanent
              # 重启防火墙
              systemctl restart firewalld.service
          ```
    2. Q：nginx: [error] open() "/usr/local/nginx/logs/nginx.pid" failed (2: No such file or directory)
        A：
          ```
              # 使用指定nginx.conf文件的方式重启nginx
              /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
          ```

### PHP
> PHP官网下载地址：[https://www.php.net/downloads.php](https://www.php.net/downloads.php)

- 安装依赖：
```
    yum -y install make libtool libxml2 libxml* libxslt* gd libjpeg* libpng* freetype* curl* libaio*
```

- 官网下载源码包：
```
    cd /usr/local/src
    wget https://www.php.net/distributions/php-7.0.33.tar.gz
    tar -zxvf php-7.0.33.tar.gz
```

- 安装PHP：
```
    cd /usr/local/src/php-7.0.33
    
    // php7.3起 废弃gd库 --enable-gd-native-ttf
    ./configure --prefix=/usr/local/php --with-curl --with-freetype-dir --with-gd --with-gettext --with-iconv-dir --with-kerberos --with-libdir=lib64 --with-libxml-dir --with-mysqli --with-openssl --with-pcre-regex --with-pdo-mysql --with-pdo-sqlite --with-pear --with-png-dir --with-xmlrpc --with-xsl --with-zlib --enable-fpm --enable-bcmath --enable-libxml --enable-inline-optimization --enable-gd-native-ttf --enable-mbregex --enable-mbstring --enable-opcache --enable-pcntl --enable-shmop --enable-soap --enable-sockets --enable-sysvsem --enable-xml --enable-zip --with-jpeg-dir
    
    make && make install
```

- 配置PHP：
```
    cp ./php.ini-development /usr/local/php/lib/php.ini
    
    cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
    
    cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf
    
    cp -R /usr/local/php/sbin/php-fpm /etc/init.d/php-fpm

    sed -i "s/pm\.max_children \= 5/pm\.max_children \= 400/g" /usr/local/php/etc/php-fpm.d/www.conf
    
    sed -i "s/pm\.start_servers \= 2/pm\.start_servers \= 40/g" /usr/local/php/etc/php-fpm.d/www.conf
    
    sed -i "s/pm\.min_spare_servers \= 1/pm\.min_spare_servers \= 20/g" /usr/local/php/etc/php-fpm.d/www.conf
    
    sed -i "s/pm\.max_spare_servers \= 3/pm\.max_spare_servers \= 80/g" /usr/local/php/etc/php-fpm.d/www.conf
```

- 启动PHP：
```
    /usr/local/php/sbin/php-fpm
```

- 一键shell：
```
    yum -y install make libtool libxml2 libxml* libxslt* gd libjpeg* libpng* freetype* curl* libaio*
    cd /usr/local/src
    PHPVERSION=7.0.33
    wget https://www.php.net/distributions/php-${PHPVERSION}.tar.gz
    tar -zxvf php-${PHPVERSION}.tar.gz && cd php-${PHPVERSION}
    ./configure --prefix=/usr/local/php --with-curl --with-freetype-dir --with-gd --with-gettext --with-iconv-dir --with-kerberos --with-libdir=lib64 --with-libxml-dir --with-mysqli --with-openssl --with-pcre-regex --with-pdo-mysql --with-pdo-sqlite --with-pear --with-png-dir --with-xmlrpc --with-xsl --with-zlib --enable-fpm --enable-bcmath --enable-libxml --enable-inline-optimization --enable-gd-native-ttf --enable-mbregex --enable-mbstring --enable-opcache --enable-pcntl --enable-shmop --enable-soap --enable-sockets --enable-sysvsem --enable-xml --enable-zip --with-jpeg-dir
    make && make install
    cp ./php.ini-development /usr/local/php/lib/php.ini
    cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
    cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf
    cp -R /usr/local/php/sbin/php-fpm /etc/init.d/php-fpm
    sed -i "s/pm\.max_children \= 5/pm\.max_children \= 400/g" /usr/local/php/etc/php-fpm.d/www.conf
    sed -i "s/pm\.start_servers \= 2/pm\.start_servers \= 40/g" /usr/local/php/etc/php-fpm.d/www.conf
    sed -i "s/pm\.min_spare_servers \= 1/pm\.min_spare_servers \= 20/g" /usr/local/php/etc/php-fpm.d/www.conf
    sed -i "s/pm\.max_spare_servers \= 3/pm\.max_spare_servers \= 80/g" /usr/local/php/etc/php-fpm.d/www.conf
    /usr/local/php/sbin/php-fpm
```

- 备注：

    1. Q：nginx+php环境的file not found问题
       A：
          1. nginx配置文件中配置的php文件指向的目录不对，导致找不到文件
              ```
                  # 这种情况应该这么做，注意代码中的{{{web目录}}}改成你对应的web目录
                  location ~ .*\.php$ {
                        fastcgi_pass   127.0.0.1:9000;
                        fastcgi_index  index.php;
                        fastcgi_param  SCRIPT_FILENAME {{{web目录}}}$fastcgi_script_name;
                        include        fastcgi.conf;
                    }
              ```
          2. 访问web目录的权限不够

### Mysql
> Mysql官网下载地址：[https://dev.mysql.com/Downloads](https://dev.mysql.com/Downloads)

- 说明：下载的源码包直接可以使用，无需编译安装（绿色版）

- 官网下载源码包：
```
    cd /usr/local/src
    wget https://dev.mysql.com/Downloads/MySQL-5.7/mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz
    tar -zxvf mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz
```

- 复制Mysql：
```
    mv /usr/local/src/mysql-5.7.27-linux-glibc2.12-x86_64 ../mysql
```

- 创建Mysql用户：
```
    cd usr/local/mysql
    groupadd mysql
    useradd mysql -s /sbin/nologin -g mysql
    chown -R mysql:mysql /data/mysql
    chown -R mysql:mysql ./
```

- 配置Mysql：
```
    sed -i "s/var\/lib/data/g" /etc/my.cnf
    sed -i '12,13d' /etc/my.cnf
    usr/local/mysql/bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql
    chown -R root:root usr/local/mysql
```

- 启动Mysql:
```
    usr/local/mysql/support-files/mysql.server start
```

- 一键shell：
```
    cd /usr/local/src
    MYSQLVERSION=5.7.27
    wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-${MYSQLVERSION}-linux-glibc2.12-x86_64.tar.gz
    tar -zxvf mysql-${MYSQLVERSION}-linux-glibc2.12-x86_64.tar.gz && mv mysql-${MYSQLVERSION}-linux-glibc2.12-x86_64 ../mysql
    cd ../mysql
    groupadd mysql
    useradd mysql -s /sbin/nologin -g mysql
    chown -R mysql:mysql /data/mysql
    chown -R mysql:mysql ./
    sed -i "s/var\/lib/data/g" /etc/my.cnf
    sed -i '12,13d' /etc/my.cnf
    ./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql
    chown -R root:root ./
    ./support-files/mysql.server start
```

- 开启Mysql远程连接：
```
    mysql -u root -p
    # 使用 grant 命令重新创建一个用户
    GRANT ALL PRIVILEGES ON *.* TO '账号'@'%' IDENTIFIED BY 'your password' 
    # 刷新权限
    flush privileges;
    # 查看目前防火墙
    firewall-cmd --list-all 
    # 开放3306端口
    firewall-cmd --permanent --add-port=3306/tcp
    # 重启防火墙,查看3306端口是否开放
    service firewalld restart
```

