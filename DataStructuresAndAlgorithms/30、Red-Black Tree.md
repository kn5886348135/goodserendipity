---
title: Red-Black Tree
author: Louis
date: 2023-08-01 14:34:17
categories:
tags:
---

&emsp;&emsp;[Red-Black Tree](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree)红黑树是一种自平衡的二叉搜索树。每个节点额外存储了一个 color 字段 ("RED" or "BLACK")，用于确保树在插入和删除时保持平衡。[红黑树的可视化](https://www.cs.usfca.edu/~galles/visualization/RedBlack.html)

### 红黑树的历史

1. 1972 年，Rudolf Bayer 提出了 [symmetric binary B-tree](https://docs.lib.purdue.edu/cgi/viewcontent.cgi?article=1457&context=cstech)，这是B 树的 4 阶情形，也就是 2-3-4树（或称为 2-4 树）。
2. 1978 年，Leonidas J. Guibas 和 Robert Sedgewick 的论文[A Dichromatic Framework for Balanced Trees](https://sedgewick.io/wp-content/themes/sedgewick/papers/1978Dichromatic.pdf)从 symmetric binary B-tree 推导出红黑树。
3. 1993 年，Arne Andersson 引入 right leaning tree(右倾树) 的概念来简化插入和删除操作，即[AA Tree](https://en.wikipedia.org/wiki/AA_tree)。
4. 1999 年，Chris Okasaki 在论文[FUNCTIONAL PEARLS -- Red-Black Trees in a Functional Setting](https://www.cs.tufts.edu/comp/150FP/archive/chris-okasaki/redblack99.pdf)中展示了如何使插入操作成为纯函数，只需处理 4 种失衡类型和 1 种默认平衡类型。
5. 2001 年，Cormen 等人在 Introduction to Algorithms, Second Edition 中将原算法的 8 个失衡类型减少为 6 个失衡类型。
6. 2008 年，Sedgewick 根据 Andersson 的思想，提出了 [Left leaning red black tree](https://sedgewick.io/wp-content/themes/sedgewick/papers/2008LLRB.pdf)(左倾红黑树)，这是 2-3 树的等价转换。

### 红黑树的定义

1. 每个节点是红色或者黑色
2. 头结点是黑色
3. 叶子节点(最底层不存放数据的节点)都是黑色，且都为空
4. 红色节点的父节点和子节点都为黑色(紅色节点不能相连)
5. 从任一节点到叶子节点的所有路径都包含相同数量的黑色节点

&emsp;&emsp;<font color="red">最长链红黑相间，最短链都是黑长度相差不超过两倍</font>

### 红黑树与2-3-4 Tree的关系

1. 红黑树的红色节点上移到父节点，形成一个2-3-4 Tree，红黑树和2-3-4Tree是等价的。
2. 红黑树的黑色节点个数=2-3-4Tree的节点个数。
3. 2-3-4树的每一个节点中:黑色节点必为父节点，红色节点为子节点(黑色在中间，红色在两边)

### 红黑树插入操作

1. 如果插入的是根节点，则为黑色
2. 其余情况插入的节点最开始一定为红色(如果是插入红色节点，仅有一种冲突情况，就是可能出现连续两个红色节点，这时候只需要旋转和变色进行调整)
3. 红黑树的插入操作分为12中情况
    1. 插入节点的父节点为黑色(4种情况)，直接插入，不做调整
    2. 叔父节点不是红色(4中情况)，变色加旋转(叔父节点不是红色，则只能是黑色的叶子节点，如果不是叶子节点则破坏了性质5)
    3. 叔父节点是红色(4种上溢情况)，变色，向上递归

![红黑树插入](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/red-black%20tree%20insert.png)

### 红黑树删除操作

&emsp;&emsp;算法(第4版)，展示了2-3树为基础的左倾红黑树。也可以2-3-4树为基础理解红黑树。1个B树节点=以黑色节点为主，若有红色节点则在其两边。B树中的删除操作，对于非叶子节点是转换为其前驱/后继节点的删除，将要删除的节点和前驱/后继节点数据交换，然后删除。

1. 红色节点可以直接删除
2. 黑色节点的删除(从任一节点到叶子节点的所有路径，都包含相同的黑色节点)
    1. 有2个红色子节点的黑色节点(4节点)，(不做考虑，会转化为对子节点的删除，待删除节点的前驱/后继节点最多只有一个子节点)
    2. 有1个红色子节点的黑色节点(3节点)，用唯一的红色子节点来替代被删除的节点(红色子节点替代、删除节点、染黑)
    3. 黑色叶子节点(2节点)
      - 删除节点为根节点，直接删除(整个红黑树只有这一个黑色节点，没有红色子节点，所以只有一个节点)
      - 删除节点的兄弟节点为黑色
        - 兄弟节点有红色子节点(借用兄弟子节点修复)
          - 红色左子节点，父节点右旋不染色
          - 红色右子节点，兄弟节点左旋，父节点右旋，父节点染黑
          - 两个红色子节点，可以选择左子节点/右子节点
        - 兄弟节点没有红色子节点(父节点向下合并)
          - 父节点是红色，父节点染黑，兄弟节点染红
          - 父节点是黑色，把父节点当做已经被删除的节点处理，向上递归染色
      - 删除节点的兄弟节点为红色(转变为兄弟节点为黑色)
        - 兄弟节点染红，父节点染黑，父节点右旋
        - 如果父节点为黑色，把父节点当做已被删除的节点处理，递归

![红黑树的删除操作](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/rbtree%20delete1.png)

&emsp;&emsp;兄弟节点没有红色子节点

![红黑树的删除操作](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/rbtree%20delete2.png)

### 红黑树的特点

1. 平衡性规定非常特别
2. 平衡性调整最为复杂
3. 优点在于每次插入删除扰动较好，但是在今天看来这个优势也极其微弱了。贪图扰动小的话，B+树、2-3-4树可能更好
4. 除此之外，红黑树并不比AVL树、SB树、跳表更加优秀
5. 数组、hashmap等扩容的时间复杂度均摊下来就是O(1)
6. 234树、B树、B+树、红黑树等平衡性相对不严格，只需要在调整平衡性的时候进行磁盘IO，用于硬盘相关，主要减少磁盘IO，而AVL Tree平衡性好，但是对磁盘IO比较敏感。
7. 插入删除、查询的时间复杂度都是O(logN)，严格证明涉及概率。

#### 子数组累加和在某个区间的数量

&emsp;&emsp;给定一个数组arr，和两个整数a和b（a<=b）。求arr中有多少个子数组，累加和在[a,b]这个范围上，返回达标的子数组数量。

> 1. 归并排序
> 2. 前缀和数组
> 3. 暴力O(N^3^)
> 4. 改写SBT

#### 滑动窗口的中位数

&emsp;&emsp;求滑动窗口每一个窗口状态的中位数
>
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
