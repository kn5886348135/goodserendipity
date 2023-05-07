---
title: 避免10大nginx配置错误
author: Louis
date: 2023-05-06 17:59:31
categories: nginx
tags: [nginx配置]
---

&emsp;&emsp;nginx官方博客，[避免10大nginx配置错误](https://www.nginx.com/blog/avoiding-top-10-nginx-configuration-mistakes/)指出nginx使用中10个最常见的配置错误。

### 没有足够的文件描述符

&emsp;&emsp;worker_connections指令限制每个worker进程最大的连接数，最大连接数也受操作系统的文件描述符限制。通常的配置错误是将文件描述符增加到worker_connections的两倍，正确的操作是在主上下文配置[worker_rlimit_nofile](https://nginx.org/en/docs/ngx_core_module.html#worker_rlimit_nofile)。每个worker进程对客户端连接或者upstream消耗一个文件描述符、代理文件消耗若干个文件描述符，另外有保存临时响应的文件、写日志、和主进程交流等。根据nginx的启动方式可以使用不同的方式设置文件描述符，但是worker_connections指令不关心nginx的启动方式。

- 如果nginx使用shell启动，使用ulimit指令，
- 如果nginx作为系统服务启动，使用init脚本或者systemd服务列表变量
- 使用/etc/security/limits.conf文件

&emsp;&emsp;可以使用sysctl fs.file-max指令临时修改系统的最大文件描述符，修改Debian系统的/proc/sys/fs/file-max文件可以永久修改系统的文件描述符数量。系统的文件描述符一般很大，但还是要确保worker_rlimit_nofile ${ \times }$ worker_processes明显小于fs.file-max，避免服务器被DDOS攻击的时候用完所有的文件描述符而无法登陆。

### 关闭error_log指令

&emsp;&emsp;error_log不接受off参数，error_log off指令会在默认目录/etc/nginx创建一个名为off的日志文件。nginx不建议关闭error_log，因为它是nginx调试问题的重要信息。

### 没有启用upstream的keepalive

&emsp;&emsp;默认情况下，nginx为每一个新进来的请求打开一个新的到upstream的连接。这是安全的，但效率低下，因为nginx和服务器必须经过3次握手建立连接，3次或者4次握手断开连接。
&emsp;&emsp;在高流量下，为每一个请求打开一个新的连接会耗尽系统资源并导致根本无法打开连接。每个连接的4元组，源ip、源端口、目标ip、目标端口都是唯一的，从nginx到upstream服务器的每个连接，源ip、目标ip、目标端口都是固定的，只有源端口是变量。当连接关闭的时候，Linux的socket保持TIME-WAIT状态并持续2分钟，在高流量的时候增加了耗尽可用源端口池的可能。发生这种情况后，nginx就不能打开到upstream的新链接。
&emsp;&emsp;解决方案就是开启nginx到upstream的keepalive，而不是请求完成时关闭连接，让连接保持建立状态用于额外的请求。这样既能减少耗尽源端口的可能，也能提升性能。
&emsp;&emsp;在每个upstream块中添加keepalive指令，设置保留在每个worker进程缓存中的与upstream服务器的空闲的、活跃的连接数。keepalive不限制nginx的每个worker进程可以打开的总的连接数，所以keepalive不必设置成很大的值。nginx官方建议设置为upstream块中服务器数量的2倍。如果在upstream块中指定了负载均衡策略，比如hash、ip_hash、least_conn、least_time、random等，则keepalive必须在负载均衡策略下面。
&emsp;&emsp;转发请求到upstream服务器的location块，包含proxy_pass、proxy_http_version、proxy_set_header指令。nginx默认为每个到upstream的连接使用HTTP/1.0，并且对应地添加请求头Connection: close。结果就是每个请求完成后都会关闭，尽管upstream块中有keepalive指令。

```Shell
# 告诉nginx使用HTTP/1.1
proxy_http_version 1.1;
# 移除Connection请求头的close值
proxy_set_header   "Connection" "";
```

### 忘记指令继承工作原理

&emsp;&emsp;nginx指令是向下继承的，比如http{}中的server{}、location{}会继承http{}的指令值，server{}中的指令会完全被location{}继承，相同的指令值会被内层块覆盖。数组指令不仅在多个上下文中被替换，在指定的上下文中也会被替换多次，比如proxy_set_header、add_header。

```Shell
http {
    add_header X-HTTP-LEVEL-HEADER 1;
    add_header X-ANOTHER-HTTP-LEVEL-HEADER 1;

    # 继承http块的X-HTTP-LEVEL-HEADER和X-ANOTHER-HTTP-LEVEL-HEADER
    server {
        listen 8080;
        location / {
            return 200 "OK";
        }
    }

    server {
        listen 8081;
        add_header X-SERVER-LEVEL-HEADER 1;
        
        # 覆盖http块的header，只剩下X-SERVER-LEVEL-HEADER
        location / {
            return 200 "OK";
        }
        
        # 覆盖server块的header，只剩下X-LOCATION-LEVEL-HEADER
        location /test {
            add_header X-LOCATION-LEVEL-HEADER 1;
            return 200 "OK";
        }

        # 覆盖server块的header，要显示4个header，必须全部添加
        location /correct {
            add_header X-HTTP-LEVEL-HEADER 1;
            add_header X-ANOTHER-HTTP-LEVEL-HEADER 1;

            add_header X-SERVER-LEVEL-HEADER 1;
            add_header X-LOCATION-LEVEL-HEADER 1;
            return 200 "OK";
        }
    }
}
```

### 关闭proxy_buffering指令

&emsp;&emsp;nginx默认开启proxy_buffering指令，开启proxy_buffering表示nginx会将服务器的响应保存在内部缓冲区，直到整个响应缓冲完才向客户端发送数据。缓冲区帮助优化慢客户端，nginx尽可能长时间地缓存响应直到客户端获取所有响应，被代理的服务器可以尽快返回响应并处理其他请求。当关闭proxy_buffering后，nginx仅在发送数据到客户端前缓冲第一部分响应，通常是一个内存页的大小(根据操作系统可能是4KB或者8KB)。这通常只够响应头的空间，nginx同步将收到的数据发送响应到客户端，强制服务器保持空闲，直到nginx可以接收下一个响应片段。
&emsp;&emsp;关闭proxy_buffering对客户端的响应延迟提升效果微乎其微，但是副作用却很明显，比如即使配置了速率限制和缓存也不会生效，影响性能等。只有很少场景关闭proxy_buffering是有意义的，比如长轮询。nginx官方强烈反对修改默认值。

### 错误使用if指令

&emsp;&emsp;if指令很难使用，尤其是在location块中。它通常不会实现你所期望的功能，并且还会引起段错误。实际上因为if特别难使用，nginx wiki上有一篇文章[if是恶魔](https://www.nginx.com/resources/wiki/start/topics/depth/ifisevil/)。通常可以在if块中安全使用的指令是return和rewrite。

### 太多健康检查

&emsp;&emsp;配置多个虚拟服务器以将请求代理到同一个上游组是很常见的（换句话说，在多个 server{} 块中包含相同的 proxy_pass 指令）。 这种情况下的错误是在每个 server{} 块中包含一个 health_check 指令。这只会在上游服务器上增加负载，而不会产生任何额外信息。解决方法是为每个上游{} 块定义一次健康检查。可以参考[nginx负载均衡](https://docs.nginx.com/nginx/admin-guide/load-balancer/)。

### 不安全地访问统计指标

&emsp;&emsp;nginx操作的基础统计指标可以通过Stub Status模块获取，NGINX plus可以通过NGINX Plus API收集更多监控信息。在server{}或者location{}块中添加stub_status或者api指令可以开启监控信息收集功能。某些监控信息包含敏感数据，可能被用来攻击网站或者App，通常用户配置并没有限制对应的URL访问。

- 使用HTTP Basic Authentication
- 使用allow和deny指令
- 结合上面两种方式

### 当所有流量都来自同一/24 CIDR子网段时使用ip_hash

&emsp;&emsp;ip_hash负载均衡基于客户端的IP地址，hash key是IPv4地址的前3个8位字节或者整个IPv6地址。该方法会建立持久性会话，同一个客户端ip会被转发到同一个服务器，直到服务器不可用。假设nginx前有各种防火墙、路由器、4层负载均衡、网关等，并且在同一个子网(10.10.0.0/24)，客户端来源于内部网络、合作方网络、互联网等，那么nginx会认为所有请求都来自10.10.0.0/24这个子网。ip_hash会将所有请求分发到同一个服务器。
&emsp;&emsp;解决方案是使用带$binary_remote_addr 的hash，将完整的客户端IP作为散列键，用consistent参数使用[ketama](https://www.metabrew.com/article/libketama-consistent-hashing-algo-memcached-clients)替代默认的哈希算法，在服务器列表发生变化时，这会大幅度减少重新映射到不同upstream服务器的key的数量，这会提高缓存命中率。

### 没有使用Upstream

&emsp;&emsp;upstream块不仅提供负载均衡功能，还解锁了很多其他的功能，比如提升性能等。
&emsp;&emsp;zone指令建立了一个共享内存区域，主机上的所有 NGINX worker进程都可以访问upstream服务器的配置和状态信息，几个upstream组可以共享该区域。
&emsp;&emsp;server指令有几个参数可用于调整服务器行为。比如max_fails=1，fail_timeout=2s表示每 2 秒内失败一次（而不是默认的每10 秒一次），就认为服务器不健康，并因此没有资格接受请求。
&emsp;&emsp;proxy_next_upstream表示NGINX认为失败的通信尝试，把请求转发给upstream组的下一个服务器。在默认的错误和超时条件中，添加http_500，以便NGINX认为来自upstream服务器的HTTP 500（内部服务器错误）代码代表一个失败的尝试。

```Shell
http {

    upstream node_backend {
        zone upstreams 64K;
        server 127.0.0.1:3000 max_fails=1 fail_timeout=2s;
        keepalive 2;
    }

    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_set_header Host $host;
            proxy_pass http://node_backend/;
            proxy_next_upstream error timeout http_500;
        }
    }
}
```
