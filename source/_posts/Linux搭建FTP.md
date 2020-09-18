---
title: Linux搭建FTP
date: 2019-11-17 11:30:39
toc: true
categories: 
    - Linux
tags:
    - Linux
    - FTP
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/P6Cqpi8ypvau1mSLeA4NledwC281Z3s7.jpg
---

### vsftpd

- 检查是否安装了vsftpd，无则安装。命令： rpm -qa | grep vsftpd
- 安装命令： yum -y install vsftpd
- 创建ftp用户，比如ftp_test。命令： useradd -s /sbin/nologin -d /home/ftp_test ftp_test 。注意目录权限
- 为ftp_test用户创建密码。命令：passwd ftp_test
- 编辑vsftpd配置文件  命令： vim /etc/vsftpd/vsftpd.conf  具体配置如下
```
    anonymous_enable=NO             #允许匿名用户访问为了安全选择关闭
    local_enable=YES                # 允许本地用户登录
    write_enable=YES                # 是否允许写入
    local_umask=022                 # 本地用户上传文件的umask
    dirmessage_enable=YES           #为YES则进入目录时显示此目录下由message_file选项指定的文本文件(,默认为.message)的内容
    xferlog_enable=YES              #开启日志
    
    xferlog_std_format=YES          #标准格式
    connect_from_port_20=YES
    xferlog_file=/var/log/xferlog   #ftp日志目录
    
    idle_session_timeout=6000       #设置客户端连接时间
    
    data_connection_timeout=1200    #设置数据连接时间 针对上传，下载
    chroot_list_enable=YES
    chroot_list_file=/etc/vsftpd/chroot_list    #设置为YES则下面的控制有效
    chroot_list_enable=YES          #若为NO,则记录在chroot_list_file所指定的文件(默认是/etc/vsftpd.chroot_list)中的用户将被chroot在登录后所在目录中,无法离开.如果为YES,则所记录的用户将不被chroot.这里YES.
    chroot_local_user=YES
    userlist_deny=NO                #若设置为YES则记录在userlist_file选项指定文件(默认是/etc/vsftpd.user_list)中的用户将无法login,并且将检察下面的userlist_deny选项
    userlist_enable=YES             #若为NO,则仅接受记录在userlist_file选项指定文件(默认是/etc/vsftpd.user_list)中的用户的login请求.若为YES则不接受这些用户的请求.
    userlist_file=/etc/vsftpd/user_list         #白名单
    chroot_list_enable=YES
    local_root=/var/ftp/pub         #根目录
    listen=YES
    pam_service_name=vsftpd
    userlist_enable=YES
    tcp_wrappers=YES
```
- FTP服务配置文件一些常用的配置选项参数：
```
    #匿名用户常用参数：
    anonymous_enable=NO：是否允许匿名访问
    anon_umask=022：设置匿名用户所上传文件的默认权限掩码值
    anon_root=/vat/ftp：设置匿名用户的宿主目录（默认为/var/ftp）
    anon_upload_enable=YES：是否允许匿名用户上传文件
    anon_mkdir_write_enable=YES：是否允许匿名用户有创建目录的写入权限
    anon_other_write_enable=YES：是否允许匿名用户有其他写入的权限（例如改名、修改、删除等）
    本地用户常用参数：
    local_enable=YES：是否允许本地系统用户访问
    local_umask=022：设置本地用户所上传文件的默认权限掩码值 
    local_root=/var/ftp：设置本地用户的ftp根目录（默认为用户的宿主目录）
    chroot_local_user=YES：是否将ftp本地用户禁锢在宿主目录中
    local_max_rate=0：限制本地用户的最大传输速率（0为无限制），单位是字节/秒（B/s）
    allow_writeable_chroot=YES：允许被限制用户的主目录具有写权限
    全局配置常用参数：
    listen=YES：是否以独立的运行方式监听服务
    listen_address=0.0.0.0：设置监听ftp服务的IP地址
    listen_port=21：设置监听ftp服务的端口号
    write_enable=YES： 启用任何形式的写入权限（比如上传、删除文件等）都需要开启此项
    download_enable=YES：是否允许下载文件（比如建立仅限于浏览、上传的ftp服务器时可将其设置                                                                     为"NO"）
    xferlog_enable=YES：启用xferlog日志，默认记录到/var/log/xferlog
    xferlog_std_format=YES： 启用标准的xferlog日志格式
    connect_from_port_20=YES：允许服务器主动模式
    pasv_enable=NO：禁止被动模式连接；默认允许被动模式连接
    pam_service_name=vsftpd：设置用于用户认证的PAM文件位置（/etc/pam.d/目录中对应的文件名）
    userlist_enable=YES：是否启用user_list用户列表文件
    max_clients=0：最多允许多少客户端同时连接（0为无限制）
    max_per_ip=0                                         对来自同一个IP地址的客户端，最多允许多少个并发连接（0为无限制）
    tcp_wrappers=YES：是否启用TCP_Wrappers主机访问控制
    虚拟用户常用参数：
    guest_username=test：指定映射的系统用户名称；
    guest_enable=YES：是否启用虚拟用户；
    allow_writeable_chroot=允许被限制用户的主目录具有写权限（此项必须写入，否则可能会报错）
    anon_world_readable_only=NO：允许用户下载目录内容
    anon_other_write_enable=YES：允许匿名用户有其他写入权限，如重命名、覆盖及删除文件等；
    user_config_dir=/etc/vsftpd/vusers_dir：指定虚拟用户独立的配置文件目录；
```

- 启动vsftp服务。命令：systemctl start vsftpd.service
- 查看ftp服务的状态。命令：systemctl status vsftpd.service
- 开启防火墙端口

### 常见问题

- ftp vsftpd 530 login incorrect 解决办法汇总
    - 密码错误
    - 检查/etc/vsftpd/vsftpd.conf配置
    - 检查/etc/pam.d/vsftpd
        - 注释 #auth required pam_shells.so
        
- could not read chroot() list file:/etc/vsftpd.chroot_list
    - 在/etc/vsftpd下新建chroot_list,加入登陆用户的名字