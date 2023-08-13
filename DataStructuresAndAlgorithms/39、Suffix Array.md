---
title: Suffix Array
author: Louis
date: 2023-08-01 14:44:05
categories: 数据结构与算法
tags: [Suffix Array, 后缀数组]
---

&emsp;&emsp;[后缀数组](https://oi-wiki.org/string/sa/)是字符串所有后缀的排序数组。 它是一种用于全文索引、数据压缩算法和文献计量学等领域的数据结构。

&emsp;&emsp;[Suffix Array](https://en.wikipedia.org/wiki/Suffix_array)由 Manber & Myers (1990)的论文[Suffix arrays a new method for on-line string searches](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/Suffix%20arrays%20a%20new%20method%20for%20on-line%20string%20searches.pdf) 引入，作为后缀树的一种简单且节省空间的替代方案。 它们是由 Gaston Gonnet 于 1987 年独立发现的，名为 PAT 阵列（Gonnet、Baeza-Yates & Snider 1992）。

&emsp;&emsp;[李志泽](https://zhizeli.github.io/)、李建和霍红卫在2016年的论文[Optimal In-Place Suffix Sorting](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/Optimal_inplace_suffix_sorting_Zhize_Li.pdf)给出了第一个时间复杂度 $\Theta(n)$ 、空间复杂度 $\Theta(1)$ 的后缀数组构造算法，该算法在时间和空间上都是最优的。

&emsp;&emsp;增强型后缀数组 (ESA) 是带有附加表的后缀数组，可重现后缀树的完整功能，同时保留相同的时间和内存复杂性。字符串所有后缀的子集的后缀数组称为稀疏后缀数组。人们已经开发了多种概率算法来最大限度地减少额外的内存使用，包括最佳时间和内存算法。

&emsp;&emsp;后缀数组（Suffix Array）主要关系到两个数组：sa 和 rk。其中，sa[i] 表示将所有后缀排序后第 i 小的后缀的编号，也是所说的后缀数组，后文也称编号数组 sa；rk[i] 表示后缀 i 的排名，是重要的辅助数组，后文也称排名数组 rk。这两个数组满足性质：$sa[rk[i]] = rk[sa[i]] = i$。
![sa和rk的示例](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/suffix%20array1.png)

&emsp;&emsp;后缀数组有几种不同时间复杂度的实现，$\Theta(n^2 \times logn)$ 、$\Theta(n \times log^{2n})$ 、$\Theta(n \times logn)$ 、$\Theta(n)$ ，$\Theta(n)$ 时间复杂度的算法有[SA-IS](https://riteme.site/blog/2016-6-19/sais.html)和DC3等。

### LeetCode1163 按字典序排在最后的子串

&emsp;&emsp;给你一个字符串 s ，找出它的所有子串并按字典序排列，返回排在最后的那个子串。

### 最大字典序

&emsp;&emsp;给定两个字符串str1和str2，想把str2整体插入到str1中的某个位置，形成最大的字典序返回字典序最大的结果。<font color="red">str1的长度远大于str2的长度?</font>

### K个正数拼接最大值

&emsp;&emsp;给两个长度分别为M和N的整型数组nums1和nums2，其中每个值都不大于9，再给定一个正数K。 你可以在nums1和nums2中挑选数字，要求一共挑选K个，并且要从左到右挑。返回所有可能的结果中，代表最大数字的结果。

### 最长公共子串

&emsp;&emsp;最长公共子串问题是面试常见题目之一，假设str1长度N，str2长度M，求最长公共子串的长度。动态规划可以达到 $\Theta(N \times M)$ 时间复杂度，但最优解后缀数组加height数组可以达到 $\Theta(N+M)$ 时间复杂度。
