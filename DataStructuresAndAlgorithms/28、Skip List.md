---
title: Skip List
author: Louis
date: 2023-06-03 01:40:13
categories: 数据结构与算法
tags: [skip list, 二叉搜索树]
---
&emsp;&emsp;[SkipList](https://en.wikipedia.org/wiki/Skip_list)是一种概率数据结构，允许 $\Theta(log n)$ 平均搜索复杂度以及 $\Theta(log n)$ 在 n 个元素的有序序列中插入的平均复杂度。由William Pugh在[Skip lists: a probabilistic alternative to balanced trees](https://www.epaperpress.com/sortsearch/download/skiplist.pdf)发表。它可以获得排序数组（用于搜索）的最佳功能，同时保持允许插入的类似链表的结构，这对于静态数组来说是不可能的。通过维护子序列的链接层次结构可以实现快速搜索，每个连续的子序列跳过的元素比前一个序列少。从最稀疏的子序列开始搜索，直到找到两个连续的元素，一个小于或等于所搜索的元素，一个大于或等于所搜索的元素。通过链接的层次结构，这两个元素链接到下一个最稀疏子序列的元素，继续搜索直到最终在完整序列中搜索到。跳过的元素可以按概率或确定性进行选择，前者更为常见。

### 跳表的特点

1. 跳表维护了一个多层级的链表结构，搜索二叉树无关
2. 利用随机概率分布来使得高层索引可以无视数据规律，做到整体性能优良
3. 思想是所有有序表中最先进的
4. 结构简单就是多级单链表
5. 查询平均时间复杂度$\Theta(logn)$，插入和修改的期望时间复杂度$\Theta(logn)$，期望空间复杂度$\Theta(n)$，最坏空间复杂度$\Theta(nlogn)$，[SkipList的复杂度证明](https://oi-wiki.org/ds/skiplist/)
6. 范围查找时，跳表的时间复杂度优于红黑树

### 跳表的实现

1. 使用一个list保存整列的节点
    1. 新增
        - 抛硬币决定当前节点node的高度
        - 从上到下，从左往右查找小于key的节点pre
        - 连接pre和node节点并填充node高度以下的节点
    2. 删除
        - 判断要删除的key这个节点是否存在
        - 每一层找到最右小于key的节点pre
        - pre在当前层指向pre的下一个节点
    3. 查询
        - 从上到下，从左往右查找

2. 使用上下左右4个节点表达链表之间的关系
3. JDK中ConCurrentSkipListMap对SkipList的实现

&emsp;&emsp;Java 8 ConCurrentSkipListMap是对SkipList的一个变种的、无锁并发实现，无锁并发相关的论文Tim Harris [A pragmatic implementation of non-blocking linked lists](https://www.cl.cam.ac.uk/research/srg/netos/papers/2001-caslists.pdf)和Maged Michael [High Performance Dynamic Lock-Free Hash Tables and List-Based Sets](https://docs.rs/crate/crossbeam/0.2.4/source/hash-and-skip.pdf)，[Mikhail Fomitchev的论文](http://www.cse.yorku.ca/~ruppert/Mikhail.pdf)，[Keir Fraser的论文](https://www.cl.cam.ac.uk/techreports/UCAM-CL-TR-579.pdf), [Hakan Sundell的论文](https://research.chalmers.se/en/person/phs)

#### 子数组累加和在某个区间的数量

&emsp;&emsp;给定一个数组arr，和两个整数a和b（a<=b）。求arr中有多少个子数组，累加和在[a,b]这个范围上，返回达标的子数组数量。

> 1. 归并排序
> 2. 前缀和数组
> 3. 暴力O(N^3^)
> 4. 改写SBT

#### 滑动窗口的中位数

&emsp;&emsp;求滑动窗口每一个窗口状态的中位数

> 1. L是滑动窗口最左位置、R是滑动窗口最右位置，一开始LR都在数组左侧
> 2. 任何一步都可能R往右动，表示某个数进了窗口
> 3. 任何一步都可能L往右动，表示某个数出了窗口

#### 设计$\Theta(logN)$的数据结构

&emsp;&emsp;设计一个结构包含如下方法，要求时间复杂度都是$\Theta(logN)$

> 1. void add(int index, int num)：把num加入到index位置
> 2. int get(int index) ：取出index位置的值
> 3. void remove(int index) ：把index位置上的值删除

### 改写有序表的核心

1. 增加某个数据项可以支持题目，例如子数组累加和在某个区间的数量的问题
2. 有序表一定要保持内部参与排序的key不重复
3. 平衡性调整时，正确更新增加的数据项
4. 在搜索二叉树上实现
