---
title: segmenttree
author: Louis
date: 2023-04-18 19:59:07
categories: 数据结构与算法
tags: [线段树, segment tree]
---

&emsp;&emsp;[线段树](https://oi-wiki.org/ds/seg/)可以在 O(logN) 的时间复杂度内实现单点修改、区间修改、区间查询（区间求和，求区间最大值，求区间最小值）等操作。[线段树](https://en.wikipedia.org/wiki/Segment_tree)可以用递归实现，也可以将递归改成迭代方式。线段树的递归深度可控，可以用于工程实践。
&emsp;&emsp;线段树适用于可以根据左右节点的信息加工，并且加工的时间复杂度O(1)，不需要知道左右子树的具体信息，解决区间查询、更新类的问题的场景。
&emsp;&emsp;任意数组arr的长度为n，存在k，满足2^k^ - 1 &le; n  &le; 2^k+1^ - 1。则这个数组的所有元素可以填充在高度为k+1的满二叉树叶子节点上，并且满二叉树的最下面一层最右边填充n - 2^k^ + 1个空节点。
![segment tree](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/segmenttree.svg)
&emsp;&emsp;lazy数组 懒更新数组，i节点task1任务的preLeft、preRight包含$i \times 2$或者$(i \times 2+1)$节点的left、right，即preLeft &le; left &amp;&amp; preRight &ge; right，则任务不再继续下发，否则任务下发。如果task2任务来到i节点并且i节点的task1任务没有下发，则先下发task1任务再执行task2。

---

### 线段树

&emsp;&emsp;给定一个数组arr，实现如下三个方法，要求时间复杂度都是O(logN)

1. void add(int L, int R, int V) :  让数组arr[L…R]上每个数都加上V
2. void update(int L, int R, int V) :  让数组arr[L…R]上每个数都变成V
3. int sum(int L, int R) :让返回arr[L…R]这个范围整体的累加和

### 积方块问题 LeetCode699

&emsp;&emsp;简化版的俄罗斯方块游戏，X轴是积木最终下落到底的轴线

1. 只会下落正方形积木
2. [a,b] -> 代表一个边长为b的正方形积木，积木左边缘沿着X = a这条线从上方掉落
3. 认为整个X轴都可能接住积木，也就是说简化版游戏是没有整体的左右边界的
4. 没有整体的左右边界，所以简化版游戏不会消除积木，因为不会有哪一层被填满。

&emsp;&emsp;给定一个N ${ \times }$ 2的二维数组matrix，可以代表N个积木依次掉落，返回每一次掉落之后的最大高度。

### 刷房子

&emsp;&emsp;1-n有n个房子，随时可以将left-right的房子刷成56中颜色的一种，问粉刷几次后left-right有多少种颜色？
&emsp;&emsp;long类型的二进制位表示56中颜色，左子树|右子树就是当前节点的颜色数量。
