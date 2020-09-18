---
title: Mysql主从复制
categories: 
    - Mysql
tags:
    - Mysql
    - 主从复制
keywords: 
    - Mysql
    - 主从复制
    - Mysql主从复制
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/fr4gvYvkxCpevCPx3vny9lvpJsZGmFvs.jpg
---

### 配置

#### 主服务my.ini配置
```
    log-bin=master-bin  # 表示开启二进制日志，并把二进制日志前缀改为master-bin 数据库备份文件名称，可自由取，生成后会带文件序列号，服务每重启一次就会产生一个新文件
    server-id = 1
    binlog-do-db=test # 表示需要备份的数据库为test
    binlog-ignore-db=mysql # 表示不需要备份的数据库为mysql
    # 如果两个都不设的话，从服务器会复制主服务器的全部数据库 （本人就是此设置）
```

#### 主服务登录mysql
```
    mysql -u root -p
    grant replication slave on *.* to 'liuliuyan'@'%' identified by '123456';
    注：
    @后面的ip地址为允许连接的客户端的ip地址，如果改为 ‘%’，就表示客户端没有ip地址的限制
    然后查看主服务master的状态： （每重启一次mysql服务，mysql-bin的日志就会新建一个，所以重启主服务，从服务必须先停止再重新配置后启动）
    flush privileges;
    show master status; # 查看主库的当前状态
```

#### 从服务my.ini配置
```
    log-bin=master-bin  # 表示开启二进制日志，并把二进制日志前缀改为master-bin 数据库备份文件名称，可自由取，生成后会带文件序列号，服务每重启一次就会产生一个新文件
    server-id = 1
    binlog-do-db=test # 表示需要备份的数据库为test
    binlog-ignore-db=mysql # 表示不需要备份的数据库为mysql
    # 如果两个都不设的话，从服务器会复制主服务器的全部数据库 （本人就是此设置）
```

#### 从服务登录mysql
```
    mysql -u root -p
    change master to master_host='localhost',master_user='liuliuyan',master_password='123456',master_port=3305,master_log_file='master-bin.000003',master_log_pos=1245;

    参数详解：
    master_host： 主服务器的IP
    master_user： 主服务器上新创建的用户名
    master_password： 用户的密码
    master_port： 主服务器的端口，如果未曾修改，默认即可。
    master_log_file： 主服务器二进制日志文件的名称，填写查看主服务器的master状态时显示的File的值

    start slave; # 启动从服务的slave复制功能
    show slave status \G;   # 查从服务器的slave状态
    # Slave_IO_Running 和 Slave_SQL_Running 的值都为 Yes，则说明主从复制的所有配置已成功
```

### Windows安装两个Mysql

- phpstudy下的mysql文件夹复制一份

- 修改my.ini
```
    [client]
        port=3307
    [mysqld]
        port=3307
        basedir="……"
        datadir="……"
```

- 安装服务
```
    #进入mysql/bin文件夹
    .\mysqld --install mysql2  # 注册服务
    net start mysql2  # 开启服务
    net stop mysql2  # 停止服务
    .\mysqld --remove mysql2  # 删除服务
    mysqld --console  # 启动服务报错查询具体问题
```

### 常见问题

#### 1. Last_IO_Errno: 1593 server-uuid重复导致slave报错

##### 场景

- 在两个库上分别查看
    ```
    mysql> SHOW VARIABLES LIKE '%server_%';
    +---------------------------------+--------------------------------------+
    | Variable_name                   | Value                                |
    +---------------------------------+--------------------------------------+
    | innodb_ft_server_stopword_table |                                      |
    | server_id                       | 11                                   |
    | server_id_bits                  | 32                                   |
    | server_uuid                     | feac17c0-d55e-11e5-b426-525400079dc4 |
    +---------------------------------+--------------------------------------+
    4 rows in set (0.00 sec)
  
    mysql> SHOW VARIABLES LIKE '%server_%';
    +---------------------------------+--------------------------------------+
    | Variable_name                   | Value                                |
    +---------------------------------+--------------------------------------+
    | innodb_ft_server_stopword_table |                                      |
    | server_id                       | 22                                   |
    | server_id_bits                  | 32                                   |
    | server_uuid                     | feac17c0-d55e-11e5-b426-525400079dc4 |
    +---------------------------------+--------------------------------------+
    4 rows in set (0.00 sec)
    
    # server_uuid相同
    ```

##### 解决方案

- 修改期中一个uuid
    ```
    SELECT uuid();
    修改auto.cnf
    重启Mysql
    ```

- 删除auto.cnf
    ```
    rm -rf mysql_dir/bin/auto.cnf
    重启Mysql
    ```