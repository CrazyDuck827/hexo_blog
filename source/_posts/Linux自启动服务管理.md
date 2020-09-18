---
title: Linux自启动服务管理
date: 2019-08-20 16:49:33
toc: true
categories: 
    - Linux
tags: 
    - Linux
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/P6Cqpi8ypvau1mSLeA4NledwC281Z3s7.jpg
---

# linux服务管理的两种方式是service和systemd

### service

> 概述：service命令其实是去/etc/init.d目录下，去执行相关程序
    
    # service命令启动redis脚本
    service redis start
    # 直接启动redis脚本
    /etc/init.d/redis start
    # 开机自启动
    update-rc.d redis defaults
    
> 缺点：
                                                           
     一是启动时间长。init进程是串行启动，只有前一个进程启动完，才会启动下一个进程
                                                                             
     二是启动脚本复杂。init进程只是执行启动脚本，不管其他事情。脚本需要自己处理各种情况，这往往使得脚本变得很长
  
### systemd

> 概述：systemd是Linux系统最新的初始化系统(init),作用是提高系统的启动速度，尽可能启动较少的进程，尽可能更多进程并发启动。
     
> systemctl 是 Systemd 的主命令，用于管理系统

    #常用命令：
    systemctl --version，查看版本。     
    systemctl list-unit-files，列出所有可用单元（服务）。
    systemctl list-units，列出所有运行中的单元。
    systemctl --failed，列出所有失败的单元。
    systemctl list-unit-files | grep enable，查看自启动的软件。
    systemctl is-enabled mysqld.service，查看某个单元是否开机启动。
    systemctl status mysqld.service，查看某个单元的状态。
    systemctl start mysqld.service，启动某个单元。
    systemctl restart mysqld.service，重启某个单元。
    systemctl stop mysqld.service，停止某个单元。
    systemctl daemon-reload，修改了某个单元的配置文件后，重载配置文件。
    systemctl reload mysqld.service，重载某个单元。
    systemctl enable mysqld.service，设置开机自启动。
    systemctl disable mysqld.service，关闭开机自启动。
    systemctl kill mysqld，杀死单元。


### 例子

 > 以Nginx为例设置开机启动，其他服务类似
    
- 在系统   服务目录里创建nginx.service文件
```
    vi /usr/lib/systemd/system/nginx.service
```
- 写入内容如下
```
    [Unit]
    Description=nginx
    After=network.target
      
    [Service]
    Type=forking
    ExecStart=/usr/local/nginx/sbin/nginx
    ExecReload=/usr/local/nginx/sbin/nginx -s reload
    ExecStop=/usr/local/nginx/sbin/nginx -s quit
    PrivateTmp=true
      
    [Install]
    WantedBy=multi-user.target
```
- 设置开机自启动
```
    systemctl enable nginx.service
```
- 查看nginx状态
```
    systemctl status nginx.service
```

### 参考资料

[https://www.jianshu.com/p/ca5ee5f7075c](https://www.jianshu.com/p/ca5ee5f7075c)

[http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)
