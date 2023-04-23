---
title: LFS准备工作
author: Louis
date: 2023-04-23 12:44:08
categories: Linux
tags: [lfs]
---

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

&emsp;&emsp;执行检查脚本$version-check.sh$，直接在命令行输入下面的命令，会生成$version-check.sh$。

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

&emsp;&emsp;设置root和lfs用户的环境变量\$LFS，每一步操作必须使用正确的用户，避免破坏宿主机或者没有构建到LFS系统上。
