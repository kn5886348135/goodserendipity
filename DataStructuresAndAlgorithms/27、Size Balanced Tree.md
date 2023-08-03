---
title: Size Balanced Tree
author: Louis
date: 2023-06-03 01:36:47
categories: 数据结构与算法
tags: [Size Balanced Tree, SBT, 二叉搜索树]
---

&emsp;&emsp;[Size Balanced Tree](http://wcipeg.com/wiki/Size_Balanced_Tree) (SBT) 是由中国 OI 选手陈启峰在 2007 年提出的一种自平衡二叉搜索树 (Self-Balanced Binary Search Tree, SBBST), 通过检查子树的节点数量进行自身的平衡维护。相比于红黑树，AVL 等主流自平衡二叉搜索树而言，Size Balanced Tree 支持在 $\Theta(\log n)$ 的时间复杂度内查询某个键值在树中的排名 (rank)。

### Size Balanced Tree定义

&emsp;&emsp;Size Balance Tree是一颗二叉搜索树，每个节点 Node 仅需要多维护一个整数字段 size, 用于储存以 Node 为根的子树中节点的个数。

### Size Balance Tree性质

1. 每一个叔叔节点为头的树，节点个数都不少于其任何一个侄子节点
2. 从底层被影响节点开始向上做路径上每个节点检查
3. 与AVL树一样，有四种违规类型：LL(一次右旋)、RR(一次左旋)、LR(左旋右旋)、RL(右旋左旋)
4. 与AVL树不同的是，每轮经过调整后，检查子节点发生变化的节点

|序号|类型  |解释            |操作|
|----|-----|----------------|----|
|1   |LL   |size(cur.left.left) &gt; size(cur.right)|cur右旋并递归检查cur,cur.left是否违规|
|2   |LR   |size(cur.left.right)&gt;size(cur.right)|cur.left左旋，cur.left.right右旋，并递归检查cur、cur.left、cur.left.right|
|3   |RR   |size(cur.right.right)&gt;size(cur.left)|cur左旋并递归检查cur,cur.right|
|4   |RL   |size(cur.right.left)&gt;size(cur.left)|cur.right右旋，cur.right.left左旋，并递归检查cur、cur.right、cur.right.left|

### Size Balance Tree的平衡性

> T.left = A, T.right = B
  size(T.left) &ge; size(T.right.left), size(T.right.right)
  size(T.right) &ge; size(T.left.left), size(T.left.right)

&emsp;&emsp;假设size(A)=n，B节点的两个子节点数都小于n，则size(B) &le; 2n。则整颗树的高度仍然是logN的水平，没有avl tree的平衡性好。
&emsp;&emsp;Size Balanced tree只能保证两个子树的size不超过2倍，时间复杂度$\Theta(logN)$有严格的数学证明，

### SB树在使用时候的改进

1. 删除时候可以不用检查平衡性
    - 假设size-balanced tree的节点数是N，删除所有的左节点或者右节点后退化成链表时，高度为logN，是最差时间复杂度。后续如果增加节点，马上递归调整成平衡状态。
2. 把平衡性的调整放在插入的时候
3. 子节点发生变化就需要递归检查的特性，AVL Tree，Red-Black Tree等没有
4. 可以在节点上封装别的数据项，来增加功能
