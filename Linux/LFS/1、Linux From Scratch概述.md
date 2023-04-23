---
title: Linux From Scratch
date: 2023-04-23 08:11:05
categories: Linux
tags: [LFS]
---

&emsp;&emsp;[Linux From Scratch](https://www.linuxfromscratch.org/)从零开始编译Linux。文章参考LFS book 11.3，宿主系统为虚拟机安装的Debian。LFS systemd book、开发版、安全漏洞等暂时忽略，仅关注编译LFS系统。LFS book主要内容是准备工作、构建LFS交叉工具链和临时工具、构建LFS系统。准备工作，包括分区、下载软件包、编译临时工具链等。LFS系统的构建需要逐个安装和编译所有需要的软件包，设定引导脚本，以及安装内核。得到的 Linux 系统是一个基本系统，在它之上可以继续编译其他软件，以扩展系统，更好地满足需求。附录给出了一个便于使用的引用列表，包括本书中安装的所有软件、库和其他重要文件。
![LFS book 目录](https://www.goodserendipity.com/asserts/linux/lfs/LFSbook.png)

&emsp;&emsp;[Software-Building-HOWTO](https://tldp.org/HOWTO/Software-Building-HOWTO.html)是一份关于在Linux环境编译和安装常规的Unix软件包的详细指南，虽然这份文档比较老，但是它较好的总结了编译和安装软件的基本技巧。[Beginner's Guide to Installing from Source](https://moi.vonos.net/linux/beginners-installing-from-source/)很好地总结了从源代码编译软件的基本技能和技巧。[How To Ask Questions The Smart Way](http://catb.org/~esr/faqs/smart-questions.html)。
&emsp;&emsp;LFS的结构尽可能遵循Linux的各项标准，比如[POSIX.1-2008.](https://pubs.opengroup.org/onlinepubs/9699919799/)、[Filesystem Hierarchy Standard (FHS) Version 3.0](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html)、[Linux Standard Base (LSB) Version 5.0 (2015)](https://refspecs.linuxfoundation.org/lsb.shtml)等。但LSB并不被广泛接受，搭建一个通过LSB认证测试的完整系统是可行的，但是需要安装大量超过LFS的额外软件包，在BLFS中可以找到这些软件包。
&emsp;&emsp;LFS要求安装的一些软件包的作用

- Acl
这个软件包包含管理访问控制列表 (ACL) 的工具，用来对文件和目录提供细粒度的访问权限控制。

- Attr
这个软件包包含管理文件系统对象的扩展属性的程序。

- Autoconf
这个软件包提供能根据软件开发者提供的模板，自动生成配置源代码的 shell 脚本的程序。如果修改了软件包的构建过程，一般需要该软件包的支持才能重新构建被修改的软件包。

- Automake
这个软件包包含能根据软件开发者提供的模板，自动生成 Makefile 的程序。如果修改了软件包的构建过程，一般需要该软件包的支持才能重新构建被修改的软件包。

- Bash
这个软件包为系统提供一个 LSB core 要求的 Bourne Shell 接口。它是较为常用的 shell 软件包，且具有一定的的扩展能力，因此在各种 shell 软件包中选择了它。

- Bc
这个软件包提供了一个任意精度数值处理语言。在编译 Linux 内核时需要该软件包。

- Binutils
该软件包提供链接器、汇编器，以及其他处理目标文件的工具。编译 LFS 系统中的大多数软件包都需要这些程序。

- Bison
这个软件包包含 yacc (Yet Another Compiler Compiler) 的 GNU 版本。一些 LFS 程序的编译过程需要该软件包。

- Bzip2
这个软件包包含用于压缩和解压缩文件的程序。许多 LFS 软件包的解压需要该软件包。

- Check
这个软件包提供其他程序使用的测试环境。

- Coreutils
这个软件包包含一些用于查看和操作文件和目录的基本程序。这些程序被用于在命令行下管理文件，以及每个 LFS 软件包的安装过程。

- DejaGNU
这个软件包提供用于测试其他程序的框架。

- Diffutils
这个软件包包含用于显示文件或目录之间的差异的程序。这些程序可以被用于创建补丁，很多软件包的编译过程也需要该软件包。

- E2fsprogs
这个软件包提供用于处理 ext2, ext3 和 ext4 文件系统的工具。它们是 Linux 支持的最常用且久经考验的文件系统。

- Eudev
这个软件包是一个设备管理器，它随着系统中硬件设备的增加或移除，动态地控制 /dev 目录中设备节点的所有权，访问权限，文件名，以及符号链接。

- Expat
这个软件包提供一个相对轻量级的 XML 解析库。Perl 模块 XML::Parser 需要该软件包。

- Expect
这个软件包包含一个自动和其他交互程序交互的脚本执行程序。一般用它测试其他程序。

- File
这个软件包包含用于判定给定文件类型的工具。一些软件包的构建脚本需要它。

- Findutils
这个软件包提供用于在文件系统中寻找文件的程序。它被许多软件包的编译脚本使用。

- Flex
这个软件包包含用于生成词法分析器的程序。它是 lex (lexical analyzer) 程序的 GNU 版本。许多 LFS 软件包的编译过程需要该软件包。

- Gawk
这个软件包提供用于操作文本文件的程序。它是 awk (Aho-Weinberg-Kernighan) 的 GNU 版本。它被许多其他软件包的构建脚本使用。

- GCC
这是 GNU 编译器的集合。它包含 C 和 C++ 编译器，以及其他一些在 LFS 中不会涉及的编译器。

- GDBM
这个软件包包含 GNU 数据库管理库。LFS 的另一个软件包 Man-DB 需要该软件包。

- Gettext
这个软件包提供用于许多其他软件包的国际化和本地化的工具和库。

- Glibc
这个软件包包含主要的 C 语言库。Linux 程序没有该软件包的支持根本无法运行。

- GMP
这个软件包提供数学库，这些库支持用于任意精度算术的函数。编译 GCC 需要该软件包。

- Gperf
这个软件包提供一个能够根据键值集合生成完美散列函数的程序。Eudev 需要该软件包。

- Grep
这个软件包包含在文本中搜索指定模式的程序。它被多数软件包的编译脚本所使用。

- Groff
这个软件包提供用于处理和格式化文本的程序。它们的一项重要功能是格式化 man 页面。

- GRUB
这个软件包是 Grand Unified Boot Loader。Linux 可以使用其他引导加载器，但 GRUB 最灵活。

- Gzip
这个软件包包含用于压缩和解压缩文件的程序。许多 LFS 软件包的解压需要该软件包。

- Iana-etc
这个软件包包含网络服务和协议的描述数据。网络功能的正确运作需要该软件包。

- Inetutils
这个软件包提供基本网络管理程序。

- Intltool
这个软件包提供能够从源代码中提取可翻译字符串的工具。

- IProute2
这个软件包提供了用于 IPv4 和 IPv6 网络的基础和高级管理程序。和另一个常见的网络工具包 net-tools 相比，它具有管理 IPv6 网络的能力。

- Kbd
这个软件包提供键盘映射文件，用于非美式键盘的键盘工具，以及一些控制台字体。

- Kmod
这个软件包提供用于管理 Linux 内核模块的程序。

- Less
这个软件包包含一个很好的文本文件查看器，它支持在查看文件时上下滚动。许多软件包使用它对输出进行分页。

- Libcap
这个软件包实现了用于访问 Linux 内核中 POSIX 1003.1e 权能字功能的用户空间接口。

- Libelf
Elfutils 项目提供了用于 ELF 文件和 DWARF 数据的工具和库。该软件包的大多数工具已经由其他软件包提供，但使用默认 (也是最高效的) 配置构建 Linux 内核时，需要使用该软件包的库。

- Libffi
这个软件包实现了一个可移植的高级编程接口，用于处理不同的调用惯例。某些程序在编译时并不知道如何向函数传递参数，例如解释器在运行时才得到函数的参数个数和类型信息。它们可以使用 libffi 作为解释语言和编译语言之间的桥梁。

- Libpipeline
Libpipeline 提供一个能够灵活、方便地操作子进程流水线的库。Man-DB 软件包需要这个库。

- Libtool
这个软件包包含 GNU 通用库支持脚本。它将共享库的使用封装成一个一致、可移植的接口。在其他 LFS 软件包的测试套件中需要该软件包。

- Linux Kernel
这个软件包就是操作系统。我们平常说的 “GNU/Linux” 环境中的 “Linux” 就指的是它。

- M4
这个软件包提供通用的文本宏处理器。它被其他程序用作构建工具。

- Make
这个软件包包含用于指导软件包编译过程的程序。LFS 中几乎每个软件包都需要它。

- Man-DB
这个软件包包含用于查找和浏览 man 页面的程序。与 man 软件包相比，该软件包的国际化功能更为强大。该软件包提供了 man 程序。

- Man-pages
这个软件包提供基本的 Linux man 页面的实际内容。

- Meson
这个软件包一个自动化软件构建过程的工具。它的设计目标是最小化软件开发者不得不用于配置构建系统的时间。该软件包在构建 Systemd 和很多 BLFS 软件包时是必要的。

- MPC
这个软件包提供用于复数算术的函数。GCC 需要该软件包。

- MPFR
这个软件包包含用于多精度算术的函数。GCC 需要该软件包。

- Ninja
这个软件包提供一个注重执行速度的小型构建系统。它被设计为读取高级构建系统生成的输入文件，并以尽量高的速度运行。Meson 需要该软件包。

- Ncurses
这个软件包包含用于处理字符界面的不依赖特定终端的库。它一般被用于为菜单系统提供光标控制。一些 LFS 软件包需要该软件包。

- Openssl
这个软件包包含关于密码学的管理工具和库，它们为 Linux 内核等其他软件包提供密码学功能。

- Patch
这个软件包包含一个通过 补丁 文件修改或创建文件的程序。补丁文件通常由 diff 程序创建。一些 LFS 软件包的编译过程需要该软件包。

- Perl
这个软件包是运行时语言 PERL 的解释器。几个 LFS 软件包的安装和测试过程需要该软件包。

- Pkg-config
这个软件包提供一个查询已经安装的库和软件包的元数据信息的程序。

- Procps-NG
这个软件包包含用于监控系统进程的程序，对系统管理非常有用。另外 LFS 引导脚本也需要该软件包。

- Psmisc
这个软件包提供一些显示当前运行的系统进程信息的程序。这些程序对系统管理非常有用。

- Python 3
这个软件包提供了一种在设计时强调代码可读性的解释性语言支持。

- Readline
这个软件包是一组库，提供命令行编辑和历史记录支持。Bash 需要该软件包。

- Sed
这个软件包可以在没有文本编辑器的情况下编辑文本文件。另外，许多 LFS 软件包的配置脚本需要该软件包。

- Shadow
这个软件包包含用于安全地处理密码的程序。

- Sysklogd
这个软件包提供用于记录系统消息的程序，这些消息包括内核或者守护进程在异常事件发生时的提示。

- Sysvinit
这个软件包提供init程序，在 Linux 系统中它是其他所有进程的祖先。

- Tar
这个软件包提供存档和提取功能，几乎每个 LFS 软件包都需要它才能被提取。

- Tcl
这个软件包包含在测试套件中广泛使用的工具控制语言 (Tool Command Language)。

- Texinfo
这个软件包提供用于阅读、编写和转换 info 页面的程序。许多 LFS 软件包的安装过程需要使用它。

- Util-linux
这个软件包包含许多工具程序，其中有处理文件系统、终端、分区和消息的工具。

- Vim
这个软件包提供一个编辑器。由于它与经典的 vi 编辑器相兼容，且拥有许多强大的功能，我们选择这个编辑器。编辑器的选择是非常主观的。如果希望的话，读者可以用其他编辑器替代它。

- Wheel
该软件包提供一个 Python 模块，该模块是 Python wheel 软件包标准格式的参考实现。

- XML::Parser
这个软件包是和 Expat 交互的 Perl 模块。

- XZ Utils
这个软件包包含用于压缩和解压缩文件的程序。在所有这类程序中，该软件包提供了最高的压缩率。该软件包被用于解压 XZ 或 LZMA 格式的压缩文件。

- Zlib
这个软件包包含一些程序使用的压缩和解压缩子程序。

- Zstd
这个软件包提供一些程序使用的压缩和解压缩子程序。它具有较高的压缩比，以及很宽的压缩比/速度权衡范围。
