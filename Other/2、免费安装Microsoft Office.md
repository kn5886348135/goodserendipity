---
title: 免费安装Microsoft Office
author: Louis
date: 2023-09-03 19:27:04
categories: [其它]
tags: [Microsoft Office]
---

&emsp;&emsp;微软官方给出了免费激活Microsoft 365的[教程](https://learn.microsoft.com/zh-cn/deployoffice/overview-licensing-activation-microsoft-365-apps)，这是一个简化的、可操作的教程。

- 1、下载[Office Depolyment Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49117)，放在任意位置，比如桌面上的office文件夹。安装完成后是可以删除的。
- 2、双击运行Office Depolyment Tool，解压缩出来5个文件，其中4个配置文件和一个setup.exe文件。我们需要另外自定义配置文件并运行setup.exe，暂时不用关心。
- 3、打开[Microsoft Office自定义工具](https://config.office.com/deploymentsettings)页面制作配置文件，选择32位/64为操作系统、标准版批量许可证、需要安装的软件，比如Word、Excel、PowerPoint等，其余保持默认。导出配置文件到桌面的office文件夹，选择xml格式，并命名为config。
- 用管理员身份打开命令行，根据32位/64为操作系统执行下面的命令

```DOC
# 32位操作系统
cd C:\Program Files(x86)\Microsoft Office\Office16
cscript ospp.vbs /sethst:kms.03k.org
cscript ospp.vbs /act

# 64位操作系统
cd C:\Program Files(x86)\Microsoft Office\Office16
cscript ospp.vbs /sethst:kms.03k.org
cscript ospp.vbs /act

# 如果激活失败，可以考虑更换kms域名
```
