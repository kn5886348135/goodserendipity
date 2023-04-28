---
title: LFS准备工作
author: Louis
date: 2023-04-23 12:44:08
categories: Linux
tags: [lfs]
---

#### 宿主系统硬件和软件

&emsp;&emsp;硬件要求4核8G，准备30G硬盘空间，软件要求

- Bash-3.2 (/bin/sh 必须是到 bash 的符号链接或硬连接)
- Binutils-2.13.1 (比 2.40 更新的版本未经测试，不推荐使用)
- Bison-2.7 (/usr/bin/yacc 必须是到 bison 的链接，或者是一个执行 bison 的小脚本)
- Coreutils-6.9
- Diffutils-2.8.1
- Findutils-4.2.31
- Gawk-4.0.1 (/usr/bin/awk 必须是到 gawk 的链接)
- GCC-5.1，包括 C++ 编译器 g++ (比 12.2.0 更新的版本未经测试，不推荐使用)。C 和 C++ 标准库 (包括头文件) 也必须可用，这样 C++ 编译器才能构建宿主环境的程序
- Grep-2.5.1a
- Gzip-1.3.12
- Linux Kernel-3.2 内核版本的要求是为了符合第 5 章和第 8 章中编译 glibc 时开发者推荐的配置选项。
- M4-1.4.10
- Make-4.0
- Patch-2.5.4
- Perl-5.8.8
- Python-3.4
- Sed-4.1.5
- Tar-1.22
- Texinfo-4.7
- Xz-5.0.0

&emsp;&emsp;执行检查脚本[$version-check.sh$](./version-check.sh)，直接在命令行输入下面的命令，会生成$version-check.sh$。

```Shell
cat > version-check.sh << "EOF"
#!/bin/bash
# Simple script to list version numbers of critical development tools
export LC_ALL=C
bash --version | head -n1 | cut -d" " -f2-4
MYSH=$(readlink -f /bin/sh)
echo "/bin/sh -> $MYSH"
echo $MYSH | grep -q bash || echo "ERROR: /bin/sh does not point to bash"
unset MYSH

echo -n "Binutils: "; ld --version | head -n1 | cut -d" " -f3-
bison --version | head -n1

if [ -h /usr/bin/yacc ]; then
  echo "/usr/bin/yacc -> `readlink -f /usr/bin/yacc`";
elif [ -x /usr/bin/yacc ]; then
  echo yacc is `/usr/bin/yacc --version | head -n1`
else
  echo "yacc not found"
fi

echo -n "Coreutils: "; chown --version | head -n1 | cut -d")" -f2
diff --version | head -n1
find --version | head -n1
gawk --version | head -n1

if [ -h /usr/bin/awk ]; then
  echo "/usr/bin/awk -> `readlink -f /usr/bin/awk`";
elif [ -x /usr/bin/awk ]; then
  echo awk is `/usr/bin/awk --version | head -n1`
else
  echo "awk not found"
fi

gcc --version | head -n1
g++ --version | head -n1
grep --version | head -n1
gzip --version | head -n1
cat /proc/version
m4 --version | head -n1
make --version | head -n1
patch --version | head -n1
echo Perl `perl -V:version`
python3 --version
sed --version | head -n1
tar --version | head -n1
makeinfo --version | head -n1  # texinfo version
xz --version | head -n1

echo 'int main(){}' > dummy.c && g++ -o dummy dummy.c
if [ -x dummy ]
  then echo "g++ compilation OK";
  else echo "g++ compilation failed"; fi
rm -f dummy.c dummy
EOF
```

&emsp;&emsp;Debian 10极简安装后，部分软件是缺失的，比如makeinfo(texinfo)、gcc、g++、m4、make、patch等，使用apt-get install安装即可。Debian 10使用的是dash，需要将/bin/sh 重新指向/bin/bash。

```Shell
# 切换到root
su
cd /bin
# 备份/bin/sh
mv sh shbak
ln -s bash sh
ls -la sh
# 删除备份
rm -rf shbak
```

#### 创建分区

&emsp;&emsp;使用cfdisk新建一个20G的分区sda3，lsblk查看当前设备状况。ext2适用于不经常更新的小分区，例如 /boot。ext3是 ext2 的升级版本，拥有日志系统，能够在非正常关机的情况下恢复分区的正常状态。它被广泛用于一般场合。ext4是 ext 文件系统家族的最新成员，它支持纳秒精度时间戳、创建或使用超大文件 (最大 16 TB) 支持等新功能，速度也更快。LFS 假设根文件系统 (/) 采用 ext4 文件系统。输入以下命令在 LFS 分区创建一个 ext4 文件系统

```Shell
mkfs -v -t ext4 /dev/sda3
```

&emsp;&emsp;不单独创建swap分区，使用宿主系统的swap分区。

#### 设置环境变量

&emsp;&emsp;设置root和lfs用户的环境变量\$LFS，每一步操作必须使用正确的用户，避免破坏宿主机或者没有构建到LFS系统上。编辑主目录下的.bash_profile和/root/.bash_profile，加入环境变量

```Shell
# 加入环境变量
# 主目录 切换到lfs用户，cd 回车后的目录
export LFS=/mnt/lfs
# 确认环境变量
echo $LFS
```

&emsp;&emsp;挂载文件系统，没有创建/boot、/boot/efi、/home、/usr、/opt、/tmp、/usr/src等分区。构建过程中重启宿主系统需要重新挂载，或者修改宿主系统的/etc/fstab文件。

```Shell
# 挂载分区
mkdir -pv $LFS
mount -v -t ext4 /dev/sda3 $LFS
# 修改/etc/fstab文件
/dev/sda3  /mnt/lfs ext4   defaults      1     1
```

&emsp;&emsp;创建sources目录，添加写入权限和sticky标志。“Sticky” 标志使得即使有多个用户对该目录有写入权限，也只有文件所有者能够删除其中的文件。

```Shell
# 切换到root用户，创建sources目录
mkdir -v $LFS/sources
# 添加写入权限和sticky标志
chmod -v a+wt $LFS/sources
# wget下载文件
wget --input-file=wget-list-sysv --continue --directory-prefix=$LFS/sources
# 校验下载的文件
pushd $LFS/sources
  md5sum -c md5sums
popd
# 修改文件所有者
chown root:root $LFS/sources/*
```

&emsp;&emsp;[wget-list-sysv](./wget-list-sysv)文件包含所有下载连接，[md5sums](./md5sums)是所有文件的校验和。
&emsp;&emsp;如果以非 root 用户身份下载了软件包和补丁，则下载的文件会属于该用户。文件系统使用 UID 记录文件所有者，而宿主系统中普通用户的 UID 在 LFS 中未被分配。因此，这些文件保留到最终的 LFS 系统后，会属于一个没有命名的 UID 。如果您不准备在 LFS 系统中为您的用户分配相同的 UID，现在就将这些文件的所有者改为 root，以避免这一问题。

&emsp;&emsp;以root身份创建有限目录，供编译使用。

```Shell
# 创建目录
mkdir -pv $LFS/{etc,var} $LFS/usr/{bin,lib,sbin}
# 创建连接
for i in bin lib sbin; do
  ln -sv usr/$i $LFS/$i
done
# LFS编辑团队特意决定不使用/usr/lib64，应该经常检查，确保它不存在。
# 为什么Debian 10会创建lib64这个目录？
case $(uname -m) in
  x86_64) mkdir -pv $LFS/lib64 ;;
esac
```

&emsp;&emsp;添加lfs用户

```Shell
groupadd lfs
# -s /bin/bash 设置 bash 为用户 lfs 的默认 shell。
# -g lfs       添加用户 lfs 到组 lfs。
# -m           为用户 lfs 创建一个主目录。
# -k /dev/null 将模板目录设置为空设备文件，防止从默认模板目录 (/etc/skel) 复制文件到新的主目录。
# lfs          这是新用户的名称。
useradd -s /bin/bash -g lfs -m -k /dev/null lfs
```

&emsp;&emsp;将 lfs 设为 $LFS 中所有目录的所有者，使 lfs 对它们拥有完全访问权

```Shell
chown -v lfs $LFS/{usr{,/*},lib,var,etc,bin,sbin,tools}
case $(uname -m) in
  x86_64) chown -v lfs $LFS/lib64 ;;
esac
```

&emsp;&emsp;为了配置一个良好的工作环境，我们为 bash 创建两个新的启动脚本。以 lfs 的身份，执行以下命令，创建一个新的 .bash_profile：

```Shell
cat > ~/.bash_profile << "EOF"
exec env -i HOME=$HOME TERM=$TERM PS1='\u:\w\$ ' /bin/bash
EOF
```

&emsp;&emsp;在以 lfs 用户登录或从其他用户使用带 “-” 选项的 su 命令切换到 lfs 用户时，初始的 shell 是一个登录 shell。它读取宿主系统的 /etc/profile 文件 (可能包含一些设置和环境变量)，然后读取 .bash_profile。我们在 .bash_profile 中使用 exec env -i.../bin/bash 命令，新建一个除了 HOME, TERM 以及 PS1 外没有任何环境变量的 shell 并替换当前 shell。这可以防止宿主环境中不需要和有潜在风险的环境变量进入构建环境。

&emsp;&emsp;新的 shell 实例是 非登录 shell，它不会读取和执行 /etc/profile 或者 .bash_profile 的内容，而是读取并执行 .bashrc 文件。现在我们创建一个 .bashrc 文件：

```Shell
cat > ~/.bashrc << "EOF"
set +h
umask 022
LFS=/mnt/lfs
LC_ALL=POSIX
LFS_TGT=$(uname -m)-lfs-linux-gnu
PATH=/usr/bin
if [ ! -L /bin ]; then PATH=/bin:$PATH; fi
PATH=$LFS/tools/bin:$PATH
CONFIG_SITE=$LFS/usr/share/config.site
export LFS LC_ALL LFS_TGT PATH CONFIG_SITE
EOF
```

- set +h
  - set +h命令关闭 bash 的散列功能。一般情况下，散列是很有用的 —— bash 使用一个散列表维护各个可执行文件的完整路径，这样就不用每次都在 PATH 指定的目录中搜索可执行文件。然而，在构建 LFS 时，我们希望总是使用最新安装的工具。关闭散列功能强制 shell 在运行程序时总是搜索 PATH。这样，一旦$LFS/tools/bin 中有新的工具可用，shell 就能够找到它们，而不是使用之前记忆在散列表中，由宿主发行版提供的 /usr/bin 或 /bin 中的工具。

- umask 022
  - 将用户的文件创建掩码 (umask) 设定为 022，保证只有文件所有者可以写新创建的文件和目录，但任何人都可读取、执行它们。(如果 open(2) 系统调用使用默认模式，则新文件将具有权限模式 644，而新目录具有权限模式 755)。

- LFS=/mnt/lfs
  - LFS 环境变量必须被设定为之前选择的挂载点。
- LC_ALL=POSIX
  - LC_ALL 环境变量控制某些程序的本地化行为，使得它们以特定国家的语言和惯例输出消息。将 LC_ALL 设置为 “POSIX” 或者 “C”(这两种设置是等价的) 可以保证在交叉编译环境中所有命令的行为完全符合预期，而与宿主的本地化设置无关。

- LFS_TGT=(uname -m)-lfs-linux-gnu
  - LFS_TGT变量设定了一个非默认，但与宿主系统兼容的机器描述符。该描述符被用于构建交叉编译器和交叉编译临时工具链。工具链技术说明将提供关于这个描述符的更多信息。

- PATH=/usr/bin
  - 许多现代 Linux 发行版合并了 /bin 和 /usr/bin。在这种情况下，标准 PATH 变量应该被设定为 /usr/bin。否则，后续命令将会增加 /bin 到搜索路径中。

- if [ ! -L /bin ]; then PATH=/bin:$PATH; fi
  - 如果 /bin 不是符号链接，则它需要被添加到 PATH 变量中。

- PATH=$LFS/tools/bin:$PATH
  - 我们将 $LFS/tools/bin 附加在默认的 PATH 环境变量之前，我们一旦安装了新的程序，shell 就能立刻使用它们。这与关闭散列功能相结合，降低了新程序可用时错误地使用宿主系统中旧程序的风险。

- CONFIG_SITE=$LFS/usr/share/config.site
  - 如果没有设定这个变量，configure 脚本可能会从宿主系统的 /usr/share/config.site 加载一些发行版特有的配置信息。覆盖这一默认路径，避免宿主系统可能造成的污染。

- export ...
  - 上述命令设定了一些变量，为了让所有子 shell 都能使用这些变量，需要导出它们。

&emsp;&emsp;一些商业发行版未做文档说明地将 /etc/bash.bashrc 引入 bash 初始化过程。该文件可能修改 lfs 用户的环境，并影响 LFS 关键软件包的构建。为了保证 lfs 用户环境的纯净，检查 /etc/bash.bashrc 是否存在，如果它存在就将它移走。以 root 用户身份，运行下面命令。当不再需要 lfs 用户时，可以复原 /etc/bash.bashrc 文件

```Shell
[ ! -e /etc/bash.bashrc ] || mv -v /etc/bash.bashrc /etc/bash.bashrc.NOUSE
```

&emsp;&emsp;强制生效

```Shell
source ~/.bash_profile
```

```Shell
# 设置环境变量
export MAKEFLAGS='-j4'
# 直接告诉 make 命令有多少个可用的处理器
make -j4
```

&emsp;&emsp;在运行 Binutils 和 GCC 的测试套件时，较常见的问题是伪终端 (PTY) 被耗尽。这会导致大量测试出现失败结果。这种现象有多种可能原因，但最常见的原因是宿主系统没有正确设置 devpts 文件系统。可以参考[FAQ](https://www.linuxfromscratch.org/lfs/faq.html#no-ptys)。
