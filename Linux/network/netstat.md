---
title: netstat
author: Louis
date: 2023-05-31 15:33:52
categories: network
tags: [network, netstat]
---

### 简介

&emsp;&emsp;[netstat](https://zh.wikipedia.org/zh-cn/Netstat)是一个基于命令行界面的网络实用工具，可显示当前的网络状态，包括传输控制协议层的连线状况、路由表、网络接口状态和网络协议的统计消息等。netstat命令适用于类Unix系统（如macOS、Linux、Solaris和BSD）、IBM OS/2和Windows NT操作系统家族（如Windows XP、Windows Vista、Windows 7、Windows 8和Windows 10）。
&emsp;&emsp;netstat可用于查找网络中的问题，并可通过计算网络流量来度量网络性能。尽管该工具仍然被包含在许多Linux发行版中，但基本都被看作过时、应被替代的工具。
&emsp;&emsp;在Linux上，推荐使用ss替代netstat，使用ip route替代netstat -r，使用ip -s link替代netstat -i，使用ip maddr替代netstat -g。

### 输出结果

&emsp;&emsp;netstat输出结果

```Shell
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0     48 anonymous:ssh           desktop-7qe4e7u:9193    ESTABLISHED
tcp        0      0 anonymous:ssh           desktop-7qe4e7u:9195    ESTABLISHED
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  3      [ ]         DGRAM                    13329    /run/systemd/notify
unix  2      [ ]         DGRAM                    18451    /run/user/1000/systemd/notify
unix  7      [ ]         DGRAM                    13350    /run/systemd/journal/socket
unix  7      [ ]         DGRAM                    13616    /run/systemd/journal/dev-log
unix  2      [ ]         DGRAM                    13506    /run/systemd/journal/syslog
unix  2      [ ]         DGRAM                    13894
unix  3      [ ]         STREAM     CONNECTED     17379
unix  3      [ ]         DGRAM                    13331
unix  3      [ ]         STREAM     CONNECTED     18458    /var/run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     16067
unix  3      [ ]         STREAM     CONNECTED     18531
unix  3      [ ]         STREAM     CONNECTED     16071
unix  3      [ ]         DGRAM                    14410
unix  3      [ ]         STREAM     CONNECTED     16155    /run/systemd/journal/stdout
unix  2      [ ]         DGRAM                    16220
unix  3      [ ]         STREAM     CONNECTED     18457
unix  3      [ ]         STREAM     CONNECTED     16068    /run/systemd/journal/stdout
unix  3      [ ]         DGRAM                    14411
unix  2      [ ]         DGRAM                    14308
unix  3      [ ]         STREAM     CONNECTED     18536
unix  3      [ ]         STREAM     CONNECTED     14394    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     16567    /var/run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     16566
unix  2      [ ]         DGRAM                    18389
unix  3      [ ]         STREAM     CONNECTED     16154
unix  3      [ ]         STREAM     CONNECTED     18381    /run/systemd/journal/stdout
....
```

&emsp;&emsp;netstat的输出结果可以分为两个部分：

- 一个是Active Internet connections，称为有源TCP连接，其中"Recv-Q"和"Send-Q"指%0A的是接收队列和发送队列。这些数字一般都应该是0。如果不是则表示软件包正在队列中堆积。这种情况只能在非常少的情况见到。
- 另一个是Active UNIX domain sockets，称为有源Unix域套接口(和网络套接字一样，但是只能用于本机通信，性能可以提高一倍)。Proto显示连接使用的协议,RefCnt表示连接到本套接口上的进程号,Types显示套接口的类型,State显示套接口当前的状态,Path表示连接到套接口的其它进程使用的路径名。

### 参数

&emsp;&emsp;可以使用man netstat查看使用手册。netstat的参数必须以连字符（-）而非斜杠（/）为前缀。并非所有平台都支持某些参数。

|   参数   |描述|
|--- |--- |
|-a  |显示所有活动中的网络连接和电脑正在监听的TCP/UDP端口。|
|-b  |显示每个应用程序所使用的网络连接和监听端口 。（仅适用于Windows XP、Windows Server 2003或更高版本的Microsoft Windows操作系统，Windows 2000或更早版本不可用。）|
|-b  |同-i，但计算网络流量的总字节数。|
|-e  |显示以太网统计消息，例如发送和接收的字节数及数据包数。此参数可以与-s组合使用。|
|-f  |显示外部地址的完整网域名称。（仅适用于Windows Vista和更高版本的Microsoft Windows操作系统。）|
|-f  |限定显示使用特定套接字（unix、inet、inet6）的地址族。|
|-g  |显示多播组消息。（可能仅适用于较新的操作系统。）|
|-i  |显示网络接口及统计消息。|
|-m  |显示随机存取存储器统计消息。|
|-n  |显示活动中的TCP连接，但主机地址和端口号以数字形式表示，不会尝试确定实际主机名。|
|-o  |显示活动中的TCP连接，并包含每个连接的进程ID（PID）。根据PID，可在Windows任务管理器的“进程”选项卡中找到该应用程序。此参数可以与-a、-n和-p组合使用。如果安装了Windows修补程序，则此参数在Windows XP、Windows Server 2003和Windows 2000上可用。|
|-p  |显示使用指定网络协议的连接。协议可以是TCP、UDP、TCPv6或UDPv6。如果此参数与-s组合使用，则协议可以是TCP、UDP、ICMP、IP、TCPv6、UDPv6、ICMPv6或IPv6。|
|-p  |显示哪些进程正在使用哪些网络接口，类似于Microsoft Windows下的-b。（需要root权限才能执行此操作。）|
|-P  |显示使用指定网络协议的连接。协议可以是IP、IPv6、ICMP、ICMPv6、IGMP、UDP、TCP或RawIP。|
|-r  |显示路由表内容，与Microsoft Windows下route print命令相同。|
|-s  |按网络协议显示统计消息。默认情况下，显示TCP、UDP、ICMP和IP协议的统计消息。如果Windows XP安装了IPv6协议，亦可以显示TCPv6、UDPv6、ICMPv6和IPv6协议的统计消息。-p参数可用于指定一组协议。|
|-t  |仅显示TCP连接。|
|-u  |仅显示UDP连接。|
|-W  |显示完整主机名或IPv6地址。|
|-v  |与-b组合使用时，将显示所有应用程序使用的网络连接和监听端口所涉及的组件列表。|
|-h  |显示该命令的提示。|
|-?  |显示该命令的提示。|
|  /?  |显示该命令的提示。|

### 命令实例

```Shell
# 列出所有端口 (包括监听和未监听的)
netstat -a
# 列出所有 tcp 端口
netstat -at
# 列出所有 udp 端口
netstat -au

# 列出所有处于监听状态的 Sockets
# 只显示监听端口
netstat -l
# 只列出所有监听 tcp 端口
netstat -lt
# 只列出所有监听 udp 端口
netstat -lu
# 只列出所有监听 UNIX 端口
netstat -lx

# 显示每个协议的统计信息
# 显示所有端口的统计信息
netstat -s
# 显示 TCP 或 UDP 端口的统计信息
netstat -st
netstat -su

# 在 netstat 输出中显示 PID 和进程名称
netstat -p
# 可以与其它命令一起使用，就可以添加 “PID/进程名称” 到 netstat 输出中，这样 debugging 的时候可以很方便的发现特定端口运行的程序。

# 在 netstat 输出中不显示主机，端口和用户名 (host, port or user)
# 当你不想让主机，端口和用户名显示，将会使用数字代替那些名称
netstat -n
# 同样可以加速输出，因为不用进行比对查询。
netstat -an

# 如果只是不想让这三个名称中的一个被显示，使用以下命令
netsat -a --numeric-ports
netsat -a --numeric-hosts
netsat -a --numeric-users

# netstat 将每隔一秒输出网络信息
netstat -c

# 显示系统不支持的地址族 (Address Families)
netstat --verbose

# 显示核心路由信息
netstat -r
# 显示数字格式，不查询主机名称
netstat -rn

# 找出程序运行的端口，并不是所有的进程都能找到，没有权限的会不显示，使用 root 权限查看所有的信息。
netstat -ap | grep ssh

# 找出运行在指定端口的进程
 netstat -an | grep ':80'

# 显示网络接口列表
netstat -i
# 显示详细信息，像是 ifconfig
netstat -ie

# 查看连接某服务端口最多的的IP地址
netstat -nat | grep "192.168.1.15:22" | awk '{print $5}'| awk -F: '{print $1}'|sort|uniq -c|sort -nr|head -20
# TCP各种状态列表
netstat -nat |awk '{print $6}'
# 先把状态全都取出来,然后使用uniq -c统计，之后再进行排序
netstat -nat |awk '{print $6}'|sort|uniq -c
netstat -nat |awk '{print $6}'|sort|uniq -c|sort -rn

# 分析access.log获得访问前10位的ip地址
awk '{print $1}' access.log |sort|uniq -c|sort -nr|head -10
```
