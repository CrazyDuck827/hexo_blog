---
title: TCP / IP 协议
date: 2019-08-07 11:25:00
categories: 
    - Web
tags:
    - TCP
    - Web
    - 网络基础
keywords: 
    - TCP / IP
    - TCP
    - IP
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/7fqLN8A4acX9HKs2zbqOYYvtAe6ILp06.png
---

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
   
       1. 第一次握手：Client将标志位SYN（同步，请求建立连接）置为1，随机产生一个值seq（序列号）=J，并将该数据包发送给Server，Client进入SYN_SENT状态，等待Server确认。
       2. 第二次握手： Server 收到数据包后由标志位 SYN=1 知道 Client 请求建立连接， Server 将标志位 SYN 和 ACK（确认号是否有效） 都置为 1 ， ack=J+1 ，随机产生一个值 seq=K ，并将该数据包发送给 Client 以确认连接请求， Server 进入 SYN_RCVD 状态。Server确认。
       3. Client 收到确认后，检查 ack 是否为 J+1 ， ACK 是否为 1 ，如果正确则将标志位 ACK 置为 1 ， ack=K+1 ，并将该数据包发送给 Server ， Server 检查 ack 是否为 K+1 ， ACK 是否为 1 ，如果正确则连接建立成功， Client 和 Server 进入 ESTABLISHED 状态，完成三次握手，随后 Client 与 Server 之间可以开始传输数据了。
       
       > 第一次握手：建立连接时，客户端发送syn包（syn=j）到服务器，并进入SYN_SENT状态，等待服务器确认；SYN：同步序列编号（Synchronize Sequence Numbers）。
         第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN_RECV状态；
         第三次握手：客户端收到服务器的SYN+ACK包，向服务器发送确认包ACK(ack=k+1），此包发送完毕，客户端和服务器进入ESTABLISHED（TCP连接成功）状态，完成三次握手。
       
   - 四次挥手：
   
       1. 第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态。
       2. 第二次挥手： Server 收到 FIN（终止，希望断开连接） 后，发送一个 ACK 给 Client ，确认序号为收到序号 +1 （与 SYN 相同，一个 FIN 占用一个序号）， Server 进入 CLOSE_WAIT 状态。
       3. 第三次挥手： Server 发送一个 FIN ，用来关闭 Server 到 Client 的数据传送， Server 进入 LAST_ACK 状态。
       4. 第四次挥手： Client 收到 FIN 后， Client 进入 TIME_WAIT 状态，接着发送一个 ACK 给 Server ，确认序号为收到序号 +1 ， Server 进入 CLOSED 状态，完成四次挥手。
       
       > 1）客户端进程发出连接释放报文，并且停止发送数据。释放数据报文首部，FIN=1，其序列号为seq=u（等于前面已经传送过来的数据的最后一个字节的序号加1），此时，客户端进入FIN-WAIT-1（终止等待1）状态。 TCP规定，FIN报文段即使不携带数据，也要消耗一个序号。
         2）服务器收到连接释放报文，发出确认报文，ACK=1，ack=u+1，并且带上自己的序列号seq=v，此时，服务端就进入了CLOSE-WAIT（关闭等待）状态。TCP服务器通知高层的应用进程，客户端向服务器的方向就释放了，这时候处于半关闭状态，即客户端已经没有数据要发送了，但是服务器若发送数据，客户端依然要接受。这个状态还要持续一段时间，也就是整个CLOSE-WAIT状态持续的时间。
         3）客户端收到服务器的确认请求后，此时，客户端就进入FIN-WAIT-2（终止等待2）状态，等待服务器发送连接释放报文（在这之前还需要接受服务器发送的最后的数据）。
         4）服务器将最后的数据发送完毕后，就向客户端发送连接释放报文，FIN=1，ack=u+1，由于在半关闭状态，服务器很可能又发送了一些数据，假定此时的序列号为seq=w，此时，服务器就进入了LAST-ACK（最后确认）状态，等待客户端的确认。
         5）客户端收到服务器的连接释放报文后，必须发出确认，ACK=1，ack=w+1，而自己的序列号是seq=u+1，此时，客户端就进入了TIME-WAIT（时间等待）状态。注意此时TCP连接还没有释放，必须经过2∗∗MSL（最长报文段寿命）的时间后，当客户端撤销相应的TCB后，才进入CLOSED状态。
         6）服务器只要收到了客户端发出的确认，立即进入CLOSED状态。同样，撤销TCB后，就结束了这次的TCP连接。可以看到，服务器结束TCP连接的时间要比客户端早一些。

![三次握手 图1](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp5.jpg)

![四次握手 图1](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp6.jpg)

![三次握手 图2](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp7.png)

![四次握手 图2](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/tcp8.png)


### 参考资料

[深入浅出 TCP/IP 协议栈](https://www.cnblogs.com/onepixel/p/7092302.html)

[这样的TCP/IP协议，你看懂了吗？](https://blog.csdn.net/patrick_star_cowboy/article/details/82016692)

[关于TCP/IP，必须知道的十个知识点](https://blog.csdn.net/u012371712/article/details/80795297)



