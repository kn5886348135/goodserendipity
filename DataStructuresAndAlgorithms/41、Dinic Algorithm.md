---
title: Dinic Algorithm
author: Louis
date: 2023-06-03 01:47:55
categories: [数据结构与算法]
tags: [最大网络流, dinic]
---

### dinic算法

1. 无向图是特殊的有向图
2. 最大网络流算法是解决有向图问题的，也可以解决无向图问题
3. 深度优先遍历可能会得不到正确的答案
4. dinic算法补一个反向边，不会让答案变大，但是会把错过的正确答案找回来
5. 用BFS建立高度，然后DFS，避免在同一层来回走
6. 用一个数组记录已经使用了的线路

&emsp;&emsp;最大网络流dinic算法[证明](https://oi-wiki.org/graph/flow/max-flow/)
