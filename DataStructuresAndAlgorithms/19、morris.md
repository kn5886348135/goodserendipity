---
title: morris
author: Louis
date: 2023-04-18 19:58:18
categories: 数据结构与算法
tags: [morris]
---

#### Morris遍历算法

&emsp;&emsp;一种遍历二叉树的方式，并且时间复杂度O(N)，额外空间复杂度O(1)。通过利用原树中大量空闲指针的方式，达到节省空间的目的。如果要求获得左树和右树的信息，必须使用二叉树的递归解决，否则可以使用morris遍历解决。第一次遍历的时候将左子树的最右子节点指向当前节点，第二次遍历的时候将左子树的最右子节点指向null。morris遍历的实质是对于没有左子树的节点只到达一次，对于有左子树的节点会到达两次。

#### Morris遍历细节

&emsp;&emsp;假设来到当前节点cur，开始时cur来到头节点位置

1. 如果cur没有左孩子，cur向右移动(cur = cur.right)
2. 如果cur有左孩子，找到左子树上最右的节点mostRight
    - a.如果mostRight的右指针指向空，让其指向cur，然后cur向左移动(cur = cur.left)
    - b.如果mostRight的右指针指向cur，让其指向null，然后cur向右移动(cur = cur.right)
3. cur为空时遍历停止

---

##### 二叉树最小深度

&emsp;&emsp;给定一棵二叉树的头节点head，求以head为头的树中，最小深度是多少？
