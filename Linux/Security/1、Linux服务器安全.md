---
title: Linux服务器安全
author: Louis
date: 2023-05-07 15:43:34
categories: 安全
tags: [Linux, 安全, Security]
---

&emsp;&emsp;安全涉及各个层面，防护策略也不尽相同。安全防护只是提高攻击成本，并不能绝对保证安全。常见的Linux服务器安全防护措施可以屏蔽绝大多数扫描，但并不能抵御有针对性的攻击。可以参考[CIS安全基线](https://downloads.cisecurity.org/#/)、[CVE](https://cve.mitre.org/)、[OWASP](https://owasp.org/)等。Google搜索Linux server hardening 、security baseline

### ssh

&emsp;&emsp;修改默认的22端口，使用密钥登录，禁止root远程登录。

### 黑白名单

&emsp;&emsp;避免使用8080、3306等各种常见的默认端口，仅开放特定的端口暴露服务。将特定IP拉黑，比如国外IP，经常访问失败的IP等。

### fail2ban

&emsp;&emsp;[fail2ban](https://www.fail2ban.org/wiki/index.php/Main_Page)通过扫描日志并禁用有恶意访问记录的IP，比如太多的密码错误、寻找漏洞等。[fail2ban的github地址](https://github.com/fail2ban/fail2ban)。

### 容器化

&emsp;&emsp;容器化比如docker，可以将服务隔离，将攻击范围限制在当前容器中，而不是整个服务器。

### Cloudflare Tunnel

&emsp;&emsp;[Cloudflare Tunnel](https://www.cloudflare-cn.com/products/tunnel/)是一个内网穿透工具，可以暴露服务，但网络不稳定，不支持HTTP3/QUIC/UDP，需要托管域名。[github仓库](https://github.com/cloudflare/cloudflared)。
