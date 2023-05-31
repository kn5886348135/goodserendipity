---
title: ss
author: Louis
date: 2023-05-31 15:50:13
categories: network
tags: [linux, ss]
---

### 简介

&emsp;&emsp;ss用于转储套接字统计信息。它允许显示类似于netstat的信息，但是比其他工具显示更多的TCP和状态信息。

### ss的语法

&emsp;&emsp;ss [options] [ FILTER ]

### ss的参数

&emsp;&emsp;当没有使用options时，ss 显示已建立连接的打开的非监听套接字列表（例如 TCP/UNIX/UDP）。

&emsp;&emsp;OPTIONS列表

|序号|简写      |命令       |作用|
|--- |---      |---        |---|
|1   |-h       |--help     |显示帮助页面|
|2   |-V       |--version  |输出版本信息|
|3   |-H       |--no-header|不输出表头|
|4   |-n       |--numeric  |以数字显示，不显示服务名|
|5   |-r       |--resolve  |尝试解析IP、端口|
|6   |-a       |--all      |输出监听和非监听套接字(对TCP是已建立连接)|
|7   |-l       |--listening|输出监听套接字(默认触发)|
|8   |-o       |--options  |输出时间信息，输出格式timer:(timer_name,expire_time,retrans)|
|9   |-e       |--extended |输出详细的socket信息，<br>格式uid:<uid_number> ino:<inode_number> sk:<cookie>|
|10  |-m       |--memory   |输出套接字占用的内存|
|11  |-p       |--processes|输出使用套接字的进程|
|12  |-i       |--info     |输出内部TCP信息|
|13  |-K       |--kill     |尝试强制关闭套接字，输出成功关闭的套接字，跳过内核不支持关闭的套接字，仅支持IPv4和IPv6|
|14  |-s       |--summary  |打印汇总统计信息。 此选项不解析从各种来源获取摘要的套接字列表。 当套接字数量如此之大以至于解析 /proc/net/tcp 很痛苦时，它很有用。|
|15  |-E       |--events   |在套接字被销毁时继续显示套接字|
|16  |-Z       |--context  |作为 -p 选项，还显示进程安全上下文。|
|17  |-z       |--contexts |作为-Z 选项还显示套接字上下文。 套接字上下文取自关联的 inode，而不是内核持有的实际套接字上下文。 套接字通常标有创建过程的上下文，但是显示的上下文将反映应用的任何策略角色、类型和/或范围转换规则，因此是一个有用的参考。|
|18  |-N NSNAME|--net=NSNAME|切换到指定的网络命名空间名称。|
|19  |-b       |--bpf      |显示套接字 BPF 过滤器（只允许管理员获取这些信息）。|
|20  |-4       |--ipv4     |仅显示IPv4套接字|
|21  |-6       |--ipv6     |仅显示IPv6套接字|
|22  |-0       |--packet   |显示packet套接字|
|23  |-t       |--tcp      |显示tcp套接字|
|24  |-u       |--udp      |显示udp套接字|
|25  |-d       |--dccp     |显示DCCP套接字|
|26  |-w       |--raw      |显示RAW套接字|
|27  |-x       |--unix     |显示Unix域套接字|
|28  |-S       |--sctp     |显示SCTP套接字|
|29  |--vsock  |       |显示vsock套接字|
|30  |-f FAMILY|--family=FAMILY|显示FAMILY类型套接字，当前支持unix,inet,inet6,link,netlink,vsock|
|31  |-A QUERY |--query=QUERY, --socket=QUERY|要转储的套接字表列表，由命令分隔。 可以理解以下标识符：all、inet、tcp、udp、raw、unix、packet、netlink、unix_dgram、unix_stream、unix_seqpacket、packet_raw、packet_dgram、dccp、sctp、vsock_stream、vsock_dgram。 列表中的任何项目都可以选择以感叹号 (!) 为前缀，以排除该套接字表被转储。|
|32  |-D FILE  |--diag=FILE|不显示任何内容，只是在应用过滤器后将有关 TCP 套接字的原始信息转储到 FILE。 如果 FILE 是 - 使用标准输出。|
|33  |-F FILE  |--filter=FILE|从 FILE 中读取过滤器信息。 FILE 的每一行都被解释为单个命令行选项。 如果 FILE 是 - 使用标准输入。|

### STATE-FILTER

&emsp;&emsp;STATE-FILTER 允许构造任意一组状态来匹配。 它的语法是关键字 state 和 exclude 的序列，后跟状态标识符。
&emsp;&emsp;可用的标志符，所有标准的TCP状态established, syn-sent, syn-recv, fin-wait-1, fin-wait-2, time-wait, closed, close-wait, last-ack, listening  and  clos‐ing.

1. all - for all the states
2. connected - all the states except for listening and closed
3. synchronized - all the connected states except for syn-sent
4. bucket - states, which are maintained as minisockets, i.e.  time-wait and syn-recv
5. big - opposite to bucket

### 用例

```Shell
# Display all TCP sockets.
ss -t -a
# Display all TCP sockets with process SELinux security contexts.
ss -t -a -Z
# Display all UDP sockets.
ss -u -a
# Display all established ssh connections.
ss -o state established '( dport = :ssh or sport = :ssh )'
# Find all local processes connected to X server.
ss -x src /tmp/.X11-unix/*
# List all the tcp sockets in state FIN-WAIT-1 for our apache to network 193.233.7/24 and look at their timers.
ss -o state fin-wait-1 '( sport = :http or sport = :https )' dst 193.233.7/24
# List sockets in all states from all socket tables but TCP.
ss -a -A 'all,!tcp'
```

[TCP协议规范RFC 793](https://tools.ietf.org/rfc/rfc793.txt)
