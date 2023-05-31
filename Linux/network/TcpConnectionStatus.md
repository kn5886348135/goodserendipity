---
title: tcp connection status
author: Louis
date: 2023-05-31 17:36:47
categories: network
tags: [network, tcp connection status]
---

&emsp;&emsp;[原文连接](https://www.ibm.com/docs/en/zos/2.1.0?topic=SSLTBW_2.1.0/com.ibm.zos.v2r1.halu101/constatus.html)。
&emsp;&emsp;[TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) 连接在其生命周期中会经历一系列状态。下图说明了 TCP 连接的可能状态以及状态如何根据来自网络或本地 TCP 套接字应用程序的各种事件进行转换。
![tcp连接状态](https://www.goodserendipity.com/asserts/linux/network/dwgl0004.gif)

---

- SYN: (同步序列编号,Synchronize Sequence Numbers)该标志仅在三次握手建立TCP连接时有效。表示一个新的TCP连接请求。
- ACK: (确认编号,Acknowledgement Number)是对TCP请求的确认标志,同时提示对端系统已经成功接收所有数据。
- FIN: (结束标志,FINish)用来结束一个TCP回话.但对应端口仍处于开放状态,准备接收后续数据。

### TCP连接状态描述

|TCP  连接状态|MVS™ 控制台缩写|TSO或者UNIX shell缩写|Description|
|------------|---------------|--------------------|-----------|
|LISTEN|Listen|Listen|等待来自远程 TCP 应用程序的连接请求。这是您可以找到本地 TCP 服务器的侦听套接字的状态。|
|SYN-SENT|SynSent|SynSent|发送连接请求后等待来自远程端点的确认, 三次 TCP 握手的第 1 步后的结果。|
|SYN-RECEIVED|SynRcvd|SynRcvd|此端点已收到连接请求并发送确认，此端点正在等待其他端点确实收到此端点对原始连接请求的确认的最终确认。 三次 TCP 握手的第 2 步后的结果。|
|ESTABLISHED|Estblsh|Establsh|代表一个完全建立的连接； 这是连接数据传输阶段的正常状态。|
|FIN-WAIT-1|FinWt1|FinWait1|等待连接终止请求的确认或来自远程 TCP 的同步连接终止请求。 这种状态通常持续时间很短。|
|FIN-WAIT-2|FinWt2|FinWait2|在此端点发送其连接终止请求后，等待来自远程 TCP 的连接终止请求。 此状态通常持续时间很短，但如果远程套接字端点在收到此套接字端点关闭连接的信息后不久没有关闭其套接字，则它可能会持续一段时间。 过多的 FIN-WAIT-2 状态可能表示远程应用程序的编码错误。|
|CLOSE-WAIT|ClosWt|ClosWait|此端点已收到来自远程端点的关闭请求，此 TCP 现在正在等待来自本地应用程序的连接终止请求。|
|CLOSING|Closing|Closing|等待来自远程 TCP 的连接终止请求确认。 当此端点收到来自本地应用程序的关闭请求，向远程端点发送终止请求，并在收到来自远程端点的确认之前收到终止请求时，进入此状态。|
|LAST-ACK|LastAck|LastAck|等待对先前发送到远程 TCP 的连接终止请求的确认。 当此端点在发送其终止请求之前收到终止请求时，将进入此状态。|
|TIME-WAIT|TimeWt|TimeWait|等待足够的时间以确保远程 TCP 收到其连接终止请求的确认。|
|CLOSED|Closed|Closed|表示完全没有连接状态|

&emsp;&emsp;TCP三次握手、四次挥手
![tcp连接数据包交换](https://www.goodserendipity.com/asserts/linux/network/packet-exchange-for-tcp-connection.webp)

### Linux系统TCP相关参数

- tcp_keepalive_time
整型，开启keepalive后，TCP发送保活消息的频率，默认2小时
- tcp_keepalive_probes
整型，TCP 发送了多少个保活探针，直到它决定连接断开，默认9个。
- tcp_keepalive_intvl
整型，发送探针的频率。探针开启后，到乘以探针数量的时间内，杀死没有响应的连接。默认75秒，尝试11分钟后会终止。
