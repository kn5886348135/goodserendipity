---
title: index tree
author: Louis
date: 2023-04-26 10:03:37
categories: 数据结构与算法
tags: [index tree, 树状数组]
---

&emsp;&emsp;前缀和数组可以很方便的获取数组arr的区间和，但仅限于arr不发生变化的场景。arr某个元素发生变化，仍然要获取某个区间和的问题可以使用[index tree](https://en.wikipedia.org/wiki/Fenwick_tree)解决。index tree可以推导二维问题，线段树二维问题则比较复杂。[树状数组](https://oi-wiki.org/ds/fenwick/)是一种支持**单点修改**和**区间查询**的，代码量小的数据结构。普通树状数组维护的信息及运算要满足**结合律**且**可差分**，如加法（和）、乘法（积）、异或等。

- 结合律：(x ${ \circ }$ y) ${ \circ }$ z = x ${ \circ }$ (y ${ \circ }$ z)，其中 ${ \circ }$ 是一个二元运算符。
- 可差分：具有逆运算的运算，即已知 x ${ \circ }$ y 和 x 可以求出 y。
  - 模意义下的乘法若要可差分，需保证每个数都存在逆元（模数为质数时一定存在）；
  - gcd，max 这些信息不可差分，所以不能用**普通树状数组**处理，但是：
    - 使用两个树状数组可以用于处理区间最值，比如[Efficient Range Minimum Queries using Binary Indexed Trees]()的page39。
    - 树状数组维护不可差分信息，比如维护区间最值等。代码量小，但时间复杂度O$(log^2n)$，比使用线段树的O(logN)要差。

#### IndexTree

1. 支持区间查询
2. 没有线段树那么强，但是非常容易改成一维、二维、三维的结构
3. 只支持单点更新

&emsp;&emsp;树状数组能解决的问题是线段树能解决的问题的子集。但是，树状数组的代码要远比线段树短，时间效率常数也更小。
&emsp;&emsp;树状数组c总是能将数组arr的前缀[1 ${ \dots }$ n]拆分成不多于$\lceil logN \rceil$(向上取整)个已知的区间段，各个区间段的长度为$2^k$(二进制的最低位为第0位，k是n的二进制每一个1的二进制位数)。arr[left ${ \dots }$ right]的区间信息查询可以转化为arr[1 ${ \dots }$ left-1]和arr[1 ${ \dots }$ right]的前缀区间结果差分。
![树状数组工作原理](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/indextree.png)

#### 树状数组与其树形态的性质

#### 建树

#### 二维树状数组
