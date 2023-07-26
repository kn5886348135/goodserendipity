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
    - 使用两个树状数组可以用于处理区间最值，比如[Efficient Range Minimum Queries using Binary Indexed Trees](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/Olympiads%20in%20Informatics/volume9.pdf)的page39。[Efficient Range Minimum Queries using Binary Indexed Trees原文链接](https://ioi.te.lv/oi/files/volume9.pdf#page=41)。
    - 树状数组维护不可差分信息，比如维护区间最值等。代码量小，但时间复杂度O$(log^2n)$，比使用线段树的O(logN)要差。

### IndexTree

1. 支持区间查询
2. 没有线段树那么强，但是非常容易改成一维、二维、三维的结构
3. 只支持单点更新

&emsp;&emsp;树状数组能解决的问题是线段树能解决的问题的子集。但是，树状数组的代码要远比线段树短，时间效率常数也更小。
&emsp;&emsp;树状数组c总是能将数组arr的前缀[1 ${ \dots }$ n]拆分成不多于$\lceil logN \rceil$(向上取整)个已知的区间段，各个区间段的长度为$2^k$(二进制的最低位为第0位，k是n的二进制每一个1的二进制位数)。arr[left ${ \dots }$ right]的区间信息查询可以转化为arr[1 ${ \dots }$ left-1]和arr[1 ${ \dots }$ right]的前缀区间结果差分。
![树状数组工作原理](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/indextree.png)

### 树状数组与其树形态的性质

&emsp;&emsp;约定

1. l(x) = x - lowbit(x) + 1。即l(x) 是 c[x] 管辖范围的左端点。
2. 对于任意正整数 x，总能将 x 表示成 $s  \times  2^{k + 1}  + 2^k$ 的形式，其中lowbit(x) = 2^k^。
3. c[x] 和 c[y] 不交指 c[x] 的管辖范围和 c[y] 的管辖范围不相交，即 [l(x), x] 和 [l(y), y] 不相交。c[x] 包含于 c[y]等表述同理。

&emsp;&emsp;则有树状数组的性质

#### 1. 对于 x &le; y，要么有 c[x] 和 c[y] 不交，要么有 c[x] 包含于 c[y]

> 证明：假设 c[x] 和 c[y]相交，即 [l(x), x] 和 [l(y), y] 相交，则一定有 l(y) &le; x &le; y。
  将 y 表示为 $s \times 2^{k +1} + 2^k$，则 l(y) = $s \times 2^{k + 1} + 1$。
  所以，x 可以表示为 $s \times 2^{k +1} + b$，其中 1 &le; b &le; 2^k^。
  不难发现 lowbit(x) = lowbit(b)。又因为 b - lowbit(b) &ge; 0，
  所以 l(x) = x - lowbit(x) + 1 = $s \times 2^{k +1}$ + b - lowbit(b) +1 &ge; $s \times 2^{k +1} + 1$ = l(y)，即 l(y) &le; l(x) &le; x &le; y。
  所以，如果 c[x] 和 c[y] 相交，那么 c[x] 的管辖范围一定完全包含于 c[y]。

#### 2. 在 c[x] 真包含于 c[x + lowbit(x)]

> 证明：设 y = x + lowbit(x)，x = $s \times 2^{k + 1} + 2^k$，则 y = $(s + 1) \times 2^{k +1}$，l(x) = $s \times 2^{k + 1} + 1$。
不难发现 lowbit(y) &ge; $2^{k + 1}$，所以 l(y) = $(s + 1) \times 2^{k + 1}$ - lowbit(y) + 1 &le; $s \times 2^{k +1} + 1$= l(x)，即 l(y) &le; l(x) &le; x < y。
所以，c[x] 真包含于 c[x + lowbit(x)]。

#### 3. 对于任意 x &lt; y &lt; x + lowbit(x)，有 c[x] 和 c[y] 不交

> 证明：设 x = $s \times 2^{k + 1} + 2^k$，则 y = x + b = $s \times 2^{k + 1} + 2^k + b$，其中 1 &le; b &lt; $2^k$。
不难发现 lowbit(y) = lowbit(b)。又因为 b - lowbit(b) &ge; 0，
因此 l(y) = y - lowbit(y) + 1 = x + b - lowbit(b) + 1 > x，即 l(x) &le; x &lt; l(y) &le; y。
所以，c[x] 和 c[y] 不交。

&emsp;&emsp;取任意树状数组的树形态的一个节点u，fa[u]表示u的直系父亲

- u &lt; fa[u]
- u大于任何一个u的后代，小于任何一个u的祖先
- u的lowbit严格小于fa[u]的lowbit

> 设y = x + lowbit(x)，x = $s \times 2^{k + 1} + 2^k$，则y = $(s+1) \times 2^{k + 1}$，不难发现lowbit(y) &ge; $2 ^{k+1}$ &gt; lowbit(x)。

- 点 x 的高度是 $log_2{lowbit}(x)$，即 x 二进制最低位 1 的位数。

> 点 x 的高度 h(x) 满足：如果 x mod 2 = 1，则 h(x) = 0，否则 h(x) = max(h(y)) + 1，其中 y 代表 x 的所有儿子（此时 x 至少存在一个儿子 x - 1）。
也就是说，一个点的高度恰好比它最高的那个儿子再高 1。如果一个点没有儿子，它的高度是 0。
高度的概念是为了更好的解释复杂度。

- c[u] 真包含于 c[fa[u]]（性质 2）。
- c[u] 真包含于 c[v]，其中 v 是 u 的任一祖先（在上一条性质上归纳）。
- c[u] 真包含 c[v]，其中 v 是 u 的任一后代（上面那条性质 u，v 颠倒）。
- 对于任意 v' > u，若 v' 不是 u 的祖先，则 c[u] 和 c[v'] 不交。

> u 和 u 的祖先中，一定存在一个点 v 使得 v < v' < fa[v]，根据性质 3 得 c[v'] 不相交于 c[v]，而 c[v] 包含 c[u]，因此 c[v'] 不交于 c[u]。

- 对于任意 v < u，如果 v 不在 u 的子树上，则 c[u] 和 c[v] 不交（上面那条性质 u，v' 颠倒）。
- 对于任意 v > u，当且仅当 v 是 u 的祖先，c[u] 真包含于 c[v]（上面几条性质的总结）。这就是树状数组单点修改的核心原理。
- 设 u = $s \times 2^{k + 1} + 2^k$，则其儿子数量为 k = $log_2 {lowbit}(u)$，编号分别为 u - $2^t$(0 &le; t &lt; k)。
  - 举例：假设 k = 3，u 的二进制编号为 ...1000，则 u 有三个儿子，二进制编号分别为 ...0111、...0110、...0100。

> 在一个数 x 的基础上减去 $2^t$，x 二进制第 t 位会反转，而更低的位保持不变。
考虑 u 的儿子 v，有 v + lowbit(v) = u，即 v = u - $2^t$ 且 lowbit(v) = $2^t$。设 u = $s \times 2^{k + 1} + 2^k$。
考虑 0 &le; t &lt; k，u 的第 t 位及后方均为 0，所以 v = u - $2^t$ 的第 t 位变为 1，后面仍为 0，满足 lowbit(v) = $2^t$。
考虑 t = k，则 v = u - $2^k$，v 的第 k 位变为 0，不满足 lowbit(v) = $2^t$。
考虑 t &gt; k，则 v = u - $2^t$，v 的第 k 位是 1，所以 lowbit(v) = $2^k$，不满足lowbi(v) = $2^t$。

- u 的所有儿子对应 c 的管辖区间恰好拼接成 [l(u), u - 1]。
  - 举例：假设 k = 3，u 的二进制编号为 ...1000，则 u 有三个儿子，二进制编号分别为 ...0111、...0110、...0100。
  - c[...0100] 表示 a[...0001 ~ ...0100]。
  - c[...0110] 表示 a[...0101 ~ ...0110]。
  - c[...0111] 表示 a[...0111 ~ ...0111]。
  - 不难发现上面是三个管辖区间的并集恰好是 a[...0001 ~ ...0111]，即 [l(u), u - 1]

> u 的儿子总能表示成 $u - 2^t$(0 &le; t &lt; k)，不难发现，t 越小，u - $2^t$ 越大，代表的区间越靠右。我们设 f(t) = u - $2^t$，则 f(k - 1), f(k - 2), $\dots$, f(0) 分别构成 u 从左到右的儿子。
不难发现 lowbit(f(t)) = $2^t$，所以 l(f(t)) = $u - 2^t - 2^t + 1$ = $u - 2^{t + 1} + 1$。
考虑相邻的两个儿子 f(t + 1) 和 f(t)。前者管辖区间的右端点是 f(t + 1) = $u - 2^{t + 1}$，后者管辖区间的左端点是 l(f(t)) = $u - 2^{t + 1} + 1$，恰好相接。
考虑最左面的儿子 f(k - 1)，其管辖左边界 l(f(k - 1)) = $u - 2^k + 1$ 恰为 l(u)。
考虑最右面的儿子 f(0)，其管辖右边界就是 u - 1。
因此，这些儿子的管辖区间可以恰好拼成 [l(u), u - 1]。

### 单点修改

&emsp;&emsp;管辖 a[x] 的 c[y] 一定包含 c[x]（根据性质 1），所以 y 在树状数组树形态上是 x 的祖先。因此我们从 x 开始不断跳父亲，直到跳得超过了原数组长度为止。

&emsp;&emsp;设 n 表示 a 的大小，单点修改 a[x] 的过程

1. 初始令 x' = x。
2. 修改 c[x']。
3. 令 x' = x' + {lowbit}(x')，如果 x' > n 说明已经跳到尽头了，终止循环；否则回到第二步。

&emsp;&emsp;区间信息和单点修改的种类，共同决定 c[x'] 的修改方式。下面给几个例子：

1. 若 c[x'] 维护区间和，修改种类是将 a[x] 加上 p，则修改方式则是将所有 c[x'] 也加上 p。
2. 若 c[x'] 维护区间积，修改种类是将 a[x] 乘上 p，则修改方式则是将所有 c[x'] 也乘上 p。
&emsp;&emsp;然而，单点修改的自由性使得修改的种类和维护的信息不一定是同种运算，比如，若 c[x'] 维护区间和，修改种类是将 a[x] 赋值为 p，可以考虑转化为将 a[x] 加上 p - a[x]。如果是将 a[x] 乘上 p，就考虑转化为 a[x] 加上 a[x] $\times$ p - a[x]。

### 复杂度分析

&emsp;&emsp;空间复杂度显然 $\Theta$(n)。

&emsp;&emsp;时间复杂度：

- 对于区间查询操作：整个 x $\gets$ x - $\operatorname{lowbit}(x)$ 的迭代过程，可看做将 x 二进制中的所有 1，从低位到高位逐渐改成 0 的过程，拆分出的区间数等于 x 二进制中 1 的数量（即 $\operatorname{popcount}(x)$）。因此，单次查询时间复杂度是 $\Theta(\log n)$；
- 对于单点修改操作：跳父亲时，访问到的高度一直严格增加，且始终有 x $\le$ n。由于点 x 的高度是 $\log_2\operatorname{lowbit}(x)$，所以跳到的高度不会超过 $\log_2n$，所以访问到的 c 的数量是 $\log n$ 级别。因此，单次单点修改复杂度是 $\Theta(\log n)$。

### 建树

&emsp;&emsp;可以考虑拆成 n 个单点修改，$\Theta(n\log^2n)$ 建树,也有 $\Theta(n)$ 的建树方法。
&emsp;&emsp;以维护区间和为例

1. 每一个节点的值是由所有与自己直接相连的儿子的值求和得到的。因此可以倒着考虑贡献，即每次确定完儿子的值后，用自己的值更新自己的直接父亲。

2. 前面讲到 c[i] 表示的区间是 $[i-\operatorname{lowbit}(i)+1, i]$，那么我们可以先预处理一个 $\mathrm{sum}$ 前缀和数组，再计算 c 数组。

&emsp;&emsp;对付多组数据很常见的技巧。若每次输入新数据都暴力清空树状数组，就可能会造成超时。因此使用 $\mathrm{tag}$ 标记，存储当前节点上次使用时间（即最近一次是被第几组数据使用）。每次操作时判断这个位置 $\mathrm{tag}$ 中的时间和当前时间是否相同，就可以判断这个位置应该是 0 还是数组内的值。

&emsp;&emsp;树状数组的区间加区间和、二维树状数组的单点修改、子矩阵查询、子矩阵加、子矩阵和、权值树状数组及应用、单点修改、查询全局第k小、全局逆序对、树状数组维护不可差分信息等，可以参考[oi-wiki树状数组](https://oi-wiki.org/ds/fenwick/#thetan-%E5%BB%BA%E6%A0%91)。
