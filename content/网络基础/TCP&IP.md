---
title : TCP/IP
date : 2017-1-20 12:54:35
---

## TCP/IP

### TCP/IP 五层结构

物理层 -> 链路层 -> 网络层 -> 传输层 -> 应用层

1、物理层

数据流在网线中以0，1的数据进行传递。可以看作是连接两台机器的网线。

2、链路层

将01数据组装为帧，并且封装部分如MAC地址等报头信息

3、网络层(IP)

包含IP地址信息，建立主机与主机的通信

4、传输层(TCP/UDP)

包含端口号信息，建立端口到端口的通信

5、应用层(FTP/HTTP/WWW/DNS)

[互联网协议](http://www.ruanyifeng.com/blog/2012/05/internet_protocol_suite_part_i.html)

### IP地址的分类

A类地址 以0开头，B类地址 以10开头，C类地址，以110开头

### ARP协议工作原理

ARP的目的是根据IP地址查找MAC地址，发送方将IP地址广播给同一网络的所有主机，接受方拿到数据包后会先判断IP是否和自己一致，如果一致，则将IP和MAC返回，否则就放弃该消息。

### TCP三次握手和 四次挥手

>三次握手：
>第一次握手：客户端发送syn包(syn=x)到服务器，并进入SYN_SEND状态，等待服务器确认；
>第二次握手：服务器收到syn包，必须确认客户的SYN（ack=x+1），同时自己也发送一个SYN包（syn=y），即SYN+ACK包，此时服务器进入SYN_RECV状态；
>第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=y+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。
>握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始传送数据。理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连接之前，TCP 连接都将被一直保持下去。
>
>
>四次挥手
>与建立连接的“三次握手”类似，断开一个TCP连接则需要“四次握手”。
>第一次挥手：主动关闭方发送一个FIN，用来关闭主动方到被动关闭方的数据传送，也就是主动关闭方告诉被动关闭方：我已经不 会再给你发数据了(当然，在fin包之前发送出去的数据，如果没有收到对应的ack确认报文，主动关闭方依然会重发这些数据)，但是，此时主动关闭方还可 以接受数据。
>第二次挥手：被动关闭方收到FIN包后，发送一个ACK给对方，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号）。
>第三次挥手：被动关闭方发送一个FIN，用来关闭被动关闭方到主动关闭方的数据传送，也就是告诉主动关闭方，我的数据也发送完了，不会再给你发数据了。
>第四次挥手：主动关闭方收到FIN后，发送一个ACK给被动关闭方，确认序号为收到序号+1，至此，完成四次挥手。
>
>

### 地址栏输入www.google.com后发生的事

1、通过DNS获取对方IP

2、封装数据，通过ARP协议获取网关IP和MAC地址，发送数据报给网关

3、网关进行数据转发


### TCP拥塞控制

两台主机在数据传输时，当接收方网络拥堵时，发送方发送的包可能会迟迟没有收到ACK，而选择重传，这会导致网络更加拥挤，所以需要做拥塞控制。

A和B在建立连接后，先以指数的方式，递增增加每次请求发送的数据包。达到阈值后，再线性增加每次传输的数据包。我们称指数增长阶段为*慢启动*，线性增长阶段为*拥塞避免*

如果在指数阶段或线性阶段，出现了丢包，那每次发送数据包的窗口值就会减半，然后再重传。

*快速重传、快速恢复*：在数据包传输发生丢失时，比如说发送方发送了M1,M2,M3,M4,M5这四个包，但是接收方都返回的M1,M2,M2,M2,M2，当发送方连续收到三个相同的M2包时，就会意识到M3包丢失，此时发送方马上重传M3包，并且会将发送阈值减半，然后线性增长。

[拥塞控制、慢启动、拥塞避免](https://zhuanlan.zhihu.com/p/97709686)

[更详细的版本](https://blog.csdn.net/qq_38623623/article/details/81290265)