---
title: Shell问题合集
date: 2019-11-29 19:30:39
toc: true
categories: 
    - Linux
tags:
    - Linux
    - Shell
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/DC4FrMGO2aTaA9QHvXl7XX0Tm7spEwj0.png
---

### shell脚本"syntax error near unexpected token 'fi'"

#### 原因分析
- windows和linux的编码模式不一致导致

#### 解决方案
- 统一编码模式
    - vim shell文件
    - 命令行执行 :set ff 发现出来的是dos
    - 命令行执行 :set ff=unix , :wq
    - 再次执行，正常使用

#### 参考资料
[解决shell脚本syntax error near unexpected token fi的问题](https://blog.csdn.net/jsqfengbao/article/details/95597260)

-----------------------

### nohup: ignoring input and appending output to ‘nohup.out’

#### 原因分析
- 没有写入权限

#### 解决方案
- 重定向错误信息
    - nohup ./program >/dev/null 2>&1 & ，/dev/null是linux系统的一个空洞
    - nohup ./program >/其他有权限的目录 2>&1 &
    - 可以用命令： tail -f nohup.out 查看具体报错信息

#### 参考资料
[nohup 命令（设置后台进程）： appending output to ‘nohup.out’ 问题](https://www.cnblogs.com/klb561/p/10153834.html)
[使用Linux重定向解决nohup.out无写权限问题](https://www.cnblogs.com/quchunhui/p/5582371.html)
[nohup: ignoring input and appending output to 'nohup.out'解决方法](https://www.vpslala.com/t/19)
