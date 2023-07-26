---
title: hash
author: Louis
date: 2023-04-28 14:57:41
categories: 数据结构与算法
tags: [hash]
---

#### hash函数的特点

1、输入域无限、输出域相对有限，比如SHA256，可以输入任意字符串，输出定长字符串
2、没有任何随机，同一个输入、输出必定相同
3、存在hash碰撞(因为输入无限、输出有限)
4、输出域是均匀离散的

&emsp;&emsp;哈希函数作用,可以把数据根据不同值，几乎均匀的分开，比如java.util.HashMap。

#### 布谷鸟过滤器

&emsp;&emsp;[布谷鸟过滤器](https://en.wikipedia.org/wiki/Cuckoo_filter)(Cuckoo Filter)相对于布隆过滤器查询性能好、空间利用效率高、支持删除操作和计数，但是严格要求同一个元素不能被插入多次(k ${ \times }$ b + 1)。所以布谷鸟过滤器是有条件支持删除操作的，实际使用场景非常有限。

#### 布隆过滤器

&emsp;&emsp;字符串str，经过三个hash函数得出3个结果，每个结果对mod取模结果index，bit[index]的结果都为1则证明str可能存在布隆过滤器中，只要有一个不为1，则str一定不在布隆过滤器中。

1. 利用哈希函数的性质
2. 每一条数据提取特征
3. 加入描黑库

#### 布隆过滤器的公式

1. 假设数据量为n，预期的失误率为p（布隆过滤器大小和每个样本的大小无关，因为将样本hash以后的大小是固定的，取模后映射到bit数组上）
2. 根据n和p，算出Bloom Filter一共需要多少个bit位，向上取整，记为m
3. 根据m和n，算出Bloom Filter需要多少个哈希函数，向上取整，记为k
4. 根据修正公式，算出真实的失误率p

$$\begin{array}{l}
    m=-\frac{n \times lnp}{(ln2)^2} \hspace{12cm} \\
    k=ln2\times\frac{m}{n}=0.7\times\frac{m}{n} \\
    p=(1-e^-(\frac{n \times k}{m}))^k \\
  \end{array}$$

&emsp;&emsp;k个hash函数可以由i ${ \times }$ a + b得到，a、b是两个不同的hash函数，1 &le; i &le; k。a、b没有任何要求，hash结果是字符串，可以使用guava的BloomFilter拿到取模后的结果在redis进行bitmap的getbit操作，setbit操作要保证原子性。也可以让redis加载[RedisBloom](https://github.com/RedisBloom/RedisBloom)，使用spring-data-redis客户端实现BloomFilter。redis的bitMap部分操作会阻塞，如果有必要可以进行缓存预热。某些场景可以将比较大的bitmap拆分成几个小的bitmap，并使用lua脚本进行操作。

#### 一致性hash

&emsp;&emsp;分布式存储结构最常见的结构

1. 哈希域变成环的设计
2. 虚拟节点技术

&emsp;&emsp;选择合适的hash key，将数据路由到对应的redis或者mysql上。hash key一定是要求均匀的，
中国的姓氏是不均匀的，如果以姓氏作为hash key，那么路由到大姓(李、王)的服务器数据一定是最多的。

&emsp;&emsp;当服务器数量发生变化的时候，需要重新hash，迁移全量的数据。一致性hash可以极大的减少迁移的数据量。

&emsp;&emsp;比如数据范围为0 - 2^64^-1，则想象有一个环，服务器的ip经过hash后放在环上。服务器hash后会发生冲突，但是概率极小，万一发生冲突了，冲突的机器都拿到一份数据/换一个ip。请求的hash key落在环上，路由到顺时针的第一台服务器上。扩容，在server5、server6中间增加server7，则将server6中hash值在server5、server7的值迁移到server7。缩容同理。
&emsp;&emsp;使用虚拟节点保证server均匀分布在环上。每个server使用1000个虚拟节点，各个server的虚拟节点可以相互交叉，保存hashkey和server、server和虚拟节点的映射。虚拟节点因为数量多，所以分布相对均匀。数据迁移的时候每个server都要迁移部分数据。
