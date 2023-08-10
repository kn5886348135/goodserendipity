---
title: B+Tree
author: Louis
date: 2023-08-01 14:35:05
categories: 数据结构与算法
tags: [B+Tree]
---
&emsp;&emsp;[B+ 树](https://oi-wiki.org/ds/bplus-tree/)([B+Tree](https://en.wikipedia.org/wiki/B-tree))是一种 m 叉排序树，每个节点的子节点数量可变，但通常有大量子节点。B+ 树由根、内部节点和叶子组成。根可以是叶子，也可以是具有两个或更多子节点的节点。[B+Tree的C++实现bplustree](https://github.com/begeekmyfriend/bplustree)。

&emsp;&emsp;B+ 树可以被视为 B 树，但每个节点仅包含键（而不是键值对），并且在底部添加了具有链接叶子节点的指针。

&emsp;&emsp;B+ 树的主要价值在于存储数据，以便在面向块的存储上下文（特别是文件系统）中进行高效检索。 这主要是因为与二叉搜索树不同，B+ 树具有非常高的扇出（节点中指向子节点的指针数量，通常约为 100 或更多），这减少了在树中找到一个元素所需的 I/O 操作次数。B+ 树比 B 树更适合实际应用中操作系统的文件索引和数据库索引。目前现代关系型数据库最广泛的支持索引结构就是 B+ 树。

&emsp;&emsp;B+ 树的特点是能够保持数据稳定有序，其插入与修改拥有较稳定的对数时间复杂度。B+ 树元素自底向上插入，这与二叉树恰好相反。

### B+Tree的性质

1. 有 n 棵子树的节点中含有 n-1 个关键字（即将区间分为 n 个子区间，每个子区间对应一棵子树）。
2. 所有叶子节点中包含了全部关键字的信息，及指向含这些关键字记录的指针，且叶子节点本身依关键字的大小自小而大顺序链接。
3. 所有的非叶子节点可以看成是索引部分，节点中仅含有其子树（根节点）中的最大（或最小）关键字。
4. 除根节点外，其他所有节点中所含关键字的个数最少有 $ \lceil \dfrac{m}{2} \rceil $（注意：B 树中除根以外的所有非叶子节点至少有 $ \lceil \dfrac{m}{2} \rceil $ 棵子树）。
5. B+ 树为了方便范围查询，叶子节点之间还用指针串联起来。

### B+ 树相比于 B 树的优势

1. 由于索引节点上只有索引而没有数据，所以索引节点上能存储比 B 树更多的索引，这样树的高度就会更矮。树的高度越矮，磁盘寻道的次数就会越少。
2. 因为数据都集中在叶子节点，而所有叶子节点的高度相同，那么可以在叶子节点中增加前后指针，指向同一个父节点的相邻兄弟节点，这样可以更好地支持查询一个值的前驱或后继，使连续访问更容易实现。

&emsp;&emsp;比如这样的 SQL 语句：select * from tbl where t > 10，如果使用 B+ 树存储数据的话，可以首先定位到数据为 10 的节点，再沿着它的 next 指针一路找到所有在该叶子节点右边的叶子节点，返回这些节点包含的数据。而如果使用 B 树结构，由于数据既可以存储在内部节点也可以存储在叶子节点，连续访问的实现会更加繁琐（需要在树的内部结构中进行移动）。

### B+ Tree的查找

// TODO

### B+ Tree的遍历

// TODO

### B+ Tree的插入

// TODO

### B+ Tree的删除

// TODO

### B+Tree的并发控制

1. 正确的读操作：
    1. 不会读到一个处于中间状态的键值对：读操作访问中的键值对正在被另一个写操作修改
    2. 不会找不到一个存在的键值对：读操作正在访问某个树节点，这个树节点上的键值对同时被另一个写操作（分裂/合并操作）移动到另一个树节点，导致读操作没有找到目标键值对
2. 正确的写操作：两个写操作不会同时修改同一个键值对
3. 无死锁：不会出现死锁：两个或多个线程发生永久堵塞（等待），每个线程都在等待被其他线程占用并堵塞了的资源

### LSM

### NV-Tree

&emsp;&emsp;传统B+树在NVM(non-volatile memory)上维护一致性的开销过高（相当一部分的原因是由 mfence 以及clflush 这两个指令造成的）。论文[NV-Tree：Reducing Consistency Cost for NVM-based Single Level Systems](https://www.usenix.org/system/files/conference/fast15/fast15-paper-yang.pdf)提出了一种新的B+Tree 结构：NV-Tree 以减少维护树结构consistency时系统的开销。
&emsp;&emsp;NV-Tree的节点被分成了三种类型，第一种是leaf node（LN），存储的是kv信息，也就是实实在在的数据，第二种是internal node（IN），存储的是重构信息，第三种是PLN，LN的父节点。主要操作有定位（locating）、插入、删除、更新、查找、分裂，替换以及合并（split，replace and merge）

&emsp;&emsp;NV-Tree的设计思路

1. 选择性的数据一致性
2. LN中无序的entry
3. 优化INs在NVM中的组织结构

### 适应性哈希索引

[参考](https://zhuanlan.zhihu.com/p/384939600)

### Bw-Tree

&emsp;&emsp;Bw-tree 是 2013 年微软发表的论文[The Bw-Tree: A B-Tree for New Hardware Platforms](https://15721.courses.cs.cmu.edu/spring2017/papers/08-oltpindexes2/bwtree-icde2013.pdf)提出的数据结构。考虑到多核机器和 SSD 日趋普及，结合两大存储引擎 B+-tree 和 LSM-tree 特点，提出了这种 latch-free、delta update、log structured 的 B族树 —— Bw-tree。[代码实现](https://github.com/wangziqi2013/BwTree)

Bw tree 的主要特点

1. 总体分三层：bwtree 索引层，缓存控制层和 Flash 存储层。
2. bwtree 在整体上是一棵 B+ 树，同时借鉴了 B-link 树的思想，每个节点存在一个指向右兄弟的 side pointer。
3. bwtree 在单个树节点上表现为类似 LSM-tree 的 Log-Structure，每个逻辑节点由 a base node + a delta records chain 组成。
4. bwtree 实现 latch-free 的核心数据结构叫 Mapping Table，通过 CAS 进行 installing 操作，修改一个 mapping entry 可以同时完成多个逻辑指针的修改。
5. bwtree flash 层也使用 Log-Structure Store （append only）对逻辑页的物理存储（base page 和 delta record）进行管理。
结构设计、基础操作、并发控制等参考[微软提出的无锁 B 族树 —— Bw-tree](https://zhuanlan.zhihu.com/p/422561790)或者上面的论文。

### uTree

&emsp;&emsp;B+Tree的优化多数着重于减少对NVM的flush次数，uTree从另一个角度对B+Tree进行了优化。将B+Tree保存在DRAM(Dynamic random access memory)，实际数据保存在PM(Persistent memory)中，断电后利用PM的数据在DRAM中重构B+Tree。uTree的结构设计、增删改查操作、并发控制等可以参考论文[uTree: a Persistent B+-Tree with Low Tail Latency](http://storage.cs.tsinghua.edu.cn/papers/vldb20-utree.pdf/)。
