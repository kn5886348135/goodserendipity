---
title: AVL tree
author: Louis
date: 2023-06-03 01:33:10
categories: 数据结构与算法
tags: [AVL tree, 自平衡，二叉搜索树]
---

&emsp;&emsp;[AVL 树](https://en.wikipedia.org/wiki/AVL_tree)，是一种自平衡的[二叉搜索树](https://en.wikipedia.org/wiki/Binary_search_tree)，两个子树的高度差不超过1。经典的搜索二叉树，新增、删除、修改节点后没有平衡功能，在数据状况很差的时候退化成链表，同时性能退化。树上没有重复的用来排序的key值，如果有重复节点的需求，可以在一个节点内部增加数据项。

### 搜索二叉树查询

1. 如果当前节点的value==key，返回true
2. 如果当前节点的value &lt; key，当前节点向左移动
3. 如果当前节点的value &gt; key，当前节点向右移动
4. 如果当前节点变成null，返回false

### 搜索二叉树新增

&emsp;&emsp;和查询过程一样，但当前节点滑到空的时候，就插入在这里

### 搜索二叉树删除

1. 先找到key所在的节点
2. 如果该节点没有左孩子、没有右孩子，直接删除即可
3. 如果该节点有左孩子、没有右孩子，直接用左孩子顶替该节点
4. 如果该节点没有左孩子、有右孩子，直接用右孩子顶替该节点
5. 如果该节点有左孩子、有右孩子，用该节点后继节点(中序遍历该节点的下一个节点)或者前驱节点顶替该节点(右节点的最左节点替换当前节点或者左节点的最右节点替换当前节点)

### AVL树的定义

1. 左右子树的高度差不超过1
2. 根节点为空
3. 左子树、右子树都是AVL树

### AVL树、Size-Balanced树、红黑树的相同点

1. 都是搜索二叉树
2. 插入、删除、查询操作同经典搜索二叉树
3. 使用调整的基本动作都只有左旋、右旋
4. 插入、删除、修改时，从最底层被影响到的节点开始，对往上路径的节点做平衡性检查
5. 因为只对一条向上路径的每个节点做$\Theta(1)$的检查和调整，所以可以做到时间复杂度$\Theta(logN)$

### AVL树、SB树、红黑树的不同点

1. 平衡性的约束不同
    - AVL树最严格(每次添加、删除节点都会检查)
    - SB树稍宽松(删除节点时可以不调整平衡性，只在增加节点的时候递归调整平衡性)
    - 红黑树最宽松(任何平衡都不会超过3次调整)
    - <font color="red">查找密集型的数据结构更适合使用AVL树?</font>
2. 插入、删除和经典搜索二叉树一样，但是各自的平衡性定义不同，左旋右旋的判断条件不同。
3. 红黑树最多旋转3次达到平衡，SB树在删除时可以不需要调整平衡性，AVL不会控制旋转次数

### 应用场景

1. Windows对进程地址空间的管理，Windows NT内核，solaris
2. 红黑树用于C++的STL中map、set的实现，Java的TreeMap、TreeSet，Java8的HashMap，CFS(Completely Fair Scheduler)进程调度算法中的vruntime，高分辨率计时器，ext3文件系统跟踪红黑树中的目录条目，虚拟内存的结构管理(VMA)，多路复用技术的Epoll的核心结构是红黑树和双向链表，加密秘钥和网络数据包均由红黑树跟踪。nginx用红黑树管理timer。
3. B/B+树用于磁盘文件数据索引和数据库索引
4. Trie树不是平衡树，用于词频统计和排序大量的字符串。搜索引擎的suggest、IDE的自动补全、通讯录的自动补全、IP路由、DNA序列匹配

### AVL树新增、删除、修改

1. 最严格的平衡性，任何节点左树高度和右树高度差不超过1
2. 往上沿途检查每个节点时，都去检查四种违规情况：LL、RR、LR、RL
3. 不同情况虽然看起来复杂，但是核心点是：LL（做一次右旋）、RR（做一次左旋）、LR和RL（利用旋转让底层那个上到顶部）,某一个节点不平衡都是LL、LR、RR、RL四种破坏平衡性的类型之一，所有父节点全部检查一次

AVL Tree新增节点

|序号|类型  |解释            |操作|
|----|-----|----------------|----|
|1   |LL   |左子树的左子树太长|当前节点右旋|
|2   |LR   |左子树的右子树太长|当前节点的左子树左旋，然后右旋，使左节点的右节点来到当前节点|
|3   |RR   |右子树的右子树太长|当前节点左旋|
|4   |RL   |右子树的左子树太长|当前节点的右子树右旋，然后左旋，使右节点的左节点来到当前节点|
|5   |LL+LR|                 |按照LL处理，直接右旋，按照LR处理可能高度差仍然大于2|
|6   |RR+RL|                 |按照RR处理，直接左旋|

AVL Tree删除节点

|序号|类型    |解释            |
|----|-------|----------------|
|1   |没有左右|父节点往上都检查|
|2   |有左无右|父节点指向左节点X，X往上都检查|
|3   |有右无左|父节点指向右节点X，X往上都检查|
|4   |有左有右|右节点的最左节点X替换当前节点，X的父节点往上检查或者左节点的最右节点X替换当前节点，X的父节点往上检查|

&emsp;&emsp;[可持久化线段树](https://zh.wikipedia.org/wiki/%E5%8F%AF%E6%8C%81%E4%B9%85%E5%8C%96%E7%BA%BF%E6%AE%B5%E6%A0%91)(在中国信息学奥林匹克竞赛中，由于引入者黄嘉泰姓名的缩写与前中共中央总书记、国家主席胡锦涛(H.J.T.)相同，因此这种数据结构也可被称为总书记树或主席树)、[234树](https://zh.wikipedia.org/zh-hans/2-3-4%E6%A0%91)、[Size-Balanced树](http://wcipeg.com/wiki/Size_Balanced_Tree)、[红黑树](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree)、[AVL树](https://en.wikipedia.org/wiki/AVL_tree)的平衡性不一样，时间复杂度都是$\Theta(log_2N)$。
&emsp;&emsp;[伸展树](https://zh.wikipedia.org/zh-hans/%E4%BC%B8%E5%B1%95%E6%A0%91)(Splay Tree)是一种能够自我平衡的二叉查找树，它能在均摊$\Theta(logn)$的时间内完成基于伸展（Splay）操作的插入、查找、修改和删除操作。它是由丹尼尔·斯立特（Daniel Sleator）和罗伯特·塔扬在1985年发明的。
&emsp;&emsp;[Patricia Trie](https://github.com/krareT/trkdb/wiki/Dynamic-Patricia-Trie)
&emsp;&emsp;mysql等关系数据库用到的有序表比如B+Tree无法拆分成分布式结构，所有都是单实例的。[mysql8的分布式功能](https://dev.mysql.com/doc/refman/8.0/en/group-replication.html)怎么理解？
&emsp;&emsp;[<font color="red">AVL Tree的优化</font>](https://zhuanlan.zhihu.com/p/454080520)(待验证)
