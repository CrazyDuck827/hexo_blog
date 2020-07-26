---
title: TCP / IP 协议
date: 2019-08-07 11:25:00
categories: 
    - Web
tags:
    - TCP
    - Web
    - 网络基础
---

# TCP / IP 协议

### 概念
- TCP/IP（Transmission Control Protocol/Internet Protocol，传输控制协议/网际协议）是指能够在多个不同网络间实现信息传输的协议簇。
- 基于TCP/IP的参考模型将协议分成四个层次，它们分别是链路层、网络层、传输层和应用层。下图表示TCP/IP模型与OSI模型各层的对照关系。

![Osi 九层/四层 图1](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp1.jpg)

![Osi 九层/四层 图2](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp2.jpg)

### 分层

   -   链路层：
        > 对电信号进行分组并形成具有特定意义的数据帧，然后以广播的形式通过物理介质发送给接收方。
   -   网络层：
        > 定义网络地址，区分网段，子网内MAC寻址，对于不同子网的数据包进行路由。（ 网络层协议包括 IP 协议（网际协议），ICMP 协议（ Internet 互联网控制报文协议），以及 IGMP 协议（ Internet 组管理协议, ARP协议等。）
   -   传输层：
        > 定义端口，标识应用程序身份，实现端口到端口的通信，TCP协议可以保证数据传输的可靠性。
   -   应用层：
        > 定义数据格式并按照对应的格式解读数据。
   -   全流程：
       - 链路层：对0和1进行分组，定义数据帧，确认主机的物理地址，传输数据；
       - 网络层：定义IP地址，确认主机所在的网络位置，并通过IP进行MAC寻址，对外网数据包进行路由转发；
       - 传输层：定义端口，确认主机上应用程序的身份，并将数据包交给对应的应用程序；
       - 应用层：定义数据格式，并按照对应的格式解读数据。
       - 用一句通俗易懂的话讲就是：
            > 当你输入一个网址并按下回车键的时候，首先，应用层协议对该请求包做了格式定义；紧接着传输层协议加上了双方的端口号，确认了双方通信的应用程序；然后网络协议加上了双方的IP地址，确认了双方的网络位置；最后链路层协议加上了双方的MAC地址，确认了双方的物理位置，同时将数据进行分组，形成数据帧，采用广播方式，通过传输介质发送给对方主机。而对于不同网段，该数据包首先会转发给网关路由器，经过多次转发后，最终被发送到目标主机。目标机接收到数据包后，采用对应的协议，对帧数据进行组装，然后再通过一层一层的协议进行解析，最终被应用层的协议解析并交给服务器处理。

### 数据包
> 在 TCP / IP　协议中数据先由上往下将数据装包，然后由下往上拆包.在装包的时候，每一层都会增加一些信息用于传输，这部分信息就叫报头，当上层的数据到达本层的时候，会将数据加上本层的报头打包在一起，继续往下传递．
         
![tcp/ip 流程图1](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp3.png)

![tcp/ip 流程图2](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp4.png)

### TCP 三次握手、四次挥手
   
   - 三次握手：
   
       1. 第一次握手：Client将标志位SYN置为1，随机产生一个值seq=J，并将该数据包发送给Server，Client进入SYN_SENT状态，等待Server确认。
       2. 第二次握手： Server 收到数据包后由标志位 SYN=1 知道 Client 请求建立连接， Server 将标志位 SYN 和 ACK 都置为 1 ， ack=J+1 ，随机产生一个值 seq=K ，并将该数据包发送给 Client 以确认连接请求， Server 进入 SYN_RCVD 状态。Server确认。
       3. Client 收到确认后，检查 ack 是否为 J+1 ， ACK 是否为 1 ，如果正确则将标志位 ACK 置为 1 ， ack=K+1 ，并将该数据包发送给 Server ， Server 检查 ack 是否为 K+1 ， ACK 是否为 1 ，如果正确则连接建立成功， Client 和 Server 进入 ESTABLISHED 状态，完成三次握手，随后 Client 与 Server 之间可以开始传输数据了。
   - 四次挥手：
   
       1. 第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态。
       2. 第二次挥手： Server 收到 FIN 后，发送一个 ACK 给 Client ，确认序号为收到序号 +1 （与 SYN 相同，一个 FIN 占用一个序号）， Server 进入 CLOSE_WAIT 状态。
       3. 第三次挥手： Server 发送一个 FIN ，用来关闭 Server 到 Client 的数据传送， Server 进入 LAST_ACK 状态。
       4. 第四次挥手： Client 收到 FIN 后， Client 进入 TIME_WAIT 状态，接着发送一个 ACK 给 Server ，确认序号为收到序号 +1 ， Server 进入 CLOSED 状态，完成四次挥手。

![三次握手 图1](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp5.jpg)

![四次握手 图1](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp6.jpg)

![三次握手 图2](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp7.png)

![四次握手 图2](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp8.png)


### 参考资料

[深入浅出 TCP/IP 协议栈](https://www.cnblogs.com/onepixel/p/7092302.html)

[这样的TCP/IP协议，你看懂了吗？](https://blog.csdn.net/patrick_star_cowboy/article/details/82016692)

[关于TCP/IP，必须知道的十个知识点](https://blog.csdn.net/u012371712/article/details/80795297)



