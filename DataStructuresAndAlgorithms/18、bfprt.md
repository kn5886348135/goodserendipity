---
title: bfprt
author: Louis
date: 2023-04-18 19:37:33
categories: 数据结构与算法
tags: [bfprt]
---

#### bfprt算法

&emsp;&emsp;[中位数的中位数](https://en.wikipedia.org/wiki/Median_of_medians) 也叫bfprt算法，利用中位数的中位数确定荷兰国旗问题的pivot，用于求解第k大数的问题，时间复杂度为O(N)。

1. 将整个序列划分为 $\frac{N}{5}$ 组，每组元素数不超过5个；
2. 寻找每组元素的中位数（因为元素个数较少，可以直接使用插入排序 等算法）；
3. 找出这组元素中位数中的中位数。将该元素作为每次partition时的分界值pivot；
4. 按照荷兰国旗问题的partition过程确定k。

---

##### 在无序数组中求第K小的数

- 大根堆 O(N)
  - 数组的前k个元素入堆
  - 遍历数组，堆顶比当前元素小则弹出堆顶，并且当前元素入堆
  - 返回堆顶
- 改写快排 O(N)
  - 在left ${ \dots }$ right区间随机获取pivot
  - 根据pivot进行荷兰国旗问题partition
  - k在小于区域则递归对左边进行partition
  - k在大于区域则递归对右边进行partition
  - k在等于区域则返回等于区域边界
  - 时间复杂度的证明涉及计算概率期望值
- bfprt算法 O(N)
  - 和改写快排相同，但是使用中位数的中位数作为pivot；
  - 获取中位数的中位数的过程中，不改变原数组，只有在partition过程中才产生交换。bfprt每次交换后都会排除 $\frac{3 \times N}{10}$ 个元素；
  - 时间复杂度的[证明](https://next.oi-wiki.org/basic/quick-sort/#%E6%94%B9%E8%BF%9B%E4%B8%AD%E4%BD%8D%E6%95%B0%E4%B8%AD%E7%9A%84%E4%B8%AD%E4%BD%8D%E6%95%B0)。

##### topK

&emsp;&emsp;给定一个无序数组arr中，长度为N，给定一个正数k，返回top k个最大的数。

- O(N ${ \times }$ logN)&emsp;&emsp;&emsp;升序排序，取最大的k个
- O(N + K ${ \times }$ logN)&emsp;自定义大根堆，从希望上建立大根堆，将最大的k个数交换到数组末尾
- O(n + k ${ \times }$ logk)&emsp;&emsp;拿到第len-k小的数num，遍历数组收集大于num的数，补齐等于num的数
