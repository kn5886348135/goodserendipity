---
title: kmp
author: Louis
date: 2023-04-18 19:36:31
categories: 数据结构与算法
tags: [kmp]
---

&emsp;&emsp;字符串匹配算法有[kmp](https://zh.wikipedia.org/wiki/KMP%E7%AE%97%E6%B3%95)(Knuth-Morris-Pratt)、[BF算法](https://oi-wiki.org/string/match/)(Brute Force)、[BM](https://oi-wiki.org/string/bm/)(Boyer-Moore)、[AC自动机](https://oi-wiki.org/string/ac-automaton/)等，其中BF算法是暴力匹配，每次匹配失败时，模式串都回退到第一个字符。kmp算法利用前缀匹配信息，BM算法是通过后缀匹配获得比前缀匹配更多的信息来实现更快的字符跳转，AC自动机是以trie的结构为基础，结合kmp建立的自动机。

#### kmp算法定义

&emsp;&emsp;kmp算法是一个字符串查找算法，在字符串str中查找target第一次出现的位置。匹配失败的时候利用next数组往后移动，加快了匹配速度，时间复杂度O(N)。
&emsp;&emsp;假设字符串str长度为n，字符串target长度为m，m &le; n。想确定str中是否有某个子串是等于target的。
&emsp;&emsp;如果是挨个字符串匹配，当str[5] != target[5]时，从str[1]和target[0]重新开始匹配，时间复杂度是$O( N \times M )$。kmp算法则是将target的匹配位置来到next[5] = 2的位置，这样就减少了重复操作。减少的操作就是 target$[ 0  { \dots }  i ]$ 的真前缀和真后缀不相等的那一部分。
![kmp匹配字符串](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/kmp1.png)

&emsp;&emsp;字符串的前缀以字符串第一个字符开始的子串，真前缀不包含末位字符(子串不包含字符串本身)。字符串的后缀以字符串最后一个字符结束的子串，真后缀不包含首个字符(子串不包含字符串本身)。

#### next数组

&emsp;&emsp;next数组是用来决定不匹配的时候，target字符串右移位置。next[i]表示target$[0 { \dots } i]$范围上最长的真前缀和真后缀长度。规定next[0] = -1，next[1] = 0，因为target[0]和target[1]位置是没有前缀和后缀的。next[i] = k 表示target$[0 \dots k-1]$和target$[i-k \dots i-1]$相等，k是target$[0 \dots i]$的真前缀和真后缀相等的最大长度，可以使用next[i-1]推导出next[i]。
![next数组的推导](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/kmp%E7%9A%84next%E6%95%B0%E7%BB%84.png)
&emsp;&emsp;假设next[i] = 6，cnd=5，则next[i+1]取决于target[6]和target[i]是否相等

- target[6] == target[i]，则next[i+1] &ge; cnd + 1，可以用反证法证明next[i+1] &gt; cnd + 1不成立
  - 假设next[i+1] = 8，next[i] = 7与假设矛盾
- target[6] ！= target[i]，问题转变成target$[0 \dots i]$的最长真前缀和真后缀，并且真前缀的后一个字符与target[i]相等，而target$[0 \dots i]$的最长真前缀和真后缀就是next[i]，并且target[6] ！= target[i]，所以cnd = next[cnd]，递归直到相等或者cnd左移到首位。

#### kmp

&emsp;&emsp;str和target逐个字符比较，利用next数组加速右移。

- 拿到next数组
- str[index1] == target[index2]，index1、index2都右移
- next[index2] == -1(index2是首个字符)，index1右移
- next[index2] != -1，index2左移
- index1越界但index2没有越界，target不存在，index2越界则target存在

```Java
    // kmp算法
    public static int kmp(String str1, String str2) {
        if (str1 == null || str2 == null || str2.length() < 1 || str1.length() < str2.length()) {
            return -1;
        }
        char[] chs1 = str1.toCharArray();
        char[] chs2 = str2.toCharArray();
        int index1 = 0;
        int index2 = 0;
        // O(M) m <= n
        int[] next = getNextArray(chs2);
        // O(N)
        while (index1 < chs1.length && index2 < chs2.length) {
            // 一起右移
            if (chs1[index1] == chs2[index2]) {
                index1++;
                index2++;
            } else if (next[index2] == -1) {
                // str1右移，str2在首位
                // y == 0
                index1++;
            } else {
                // str2左移直到匹配成功或者来到首位
                index2 = next[index2];
            }
        }
        // index2越界证明str2已经匹配完
        // index2没有越界但退出了循环，则是index1越界了
        return index2 == chs2.length ? index1 - index2 : -1;
    }

    // 优化后的获取next数组 O(m)
    // 动态规划
    // 利用已经求好的i-1的next数组推倒i的next数组
    // 在线算法，数据到达时处理
    public static int[] getNextArray(char[] str2) {
        if (str2.length == 1) {
            return new int[]{-1};
        }
        int[] next = new int[str2.length];
        next[0] = -1;
        next[1] = 0;
        // next数组的下标
        int i = 2;
        // 真前缀的末位字符
        int cnd = 0;
        while (i < next.length) {
            // 真前缀的末位和真后缀的末位比较
            // 配成功的时候
            if (str2[i - 1] == str2[cnd]) {
                // next[i++] >= cnd，如果next[i++] = cnd + 2，则表示next[i-1] = cnd + 1，与假设矛盾
                // 所以next[i] = cnd + 1
                next[i++] = ++cnd;
            } else if (cnd > 0) {
                // 真前缀的末位和真后缀的末位不相等
                // cnd > 0表示两个字符串不为空，还是有一部分相同的
                // cnd在前一步已经++，此时next[cnd]就是上一个匹配的字符位置
                // 反证法 如果cnd = cnd - 1 && cnd - 1 > next[cnd]，那么next[cnd]就是错误的
                cnd = next[cnd];
            } else {
                // 真前缀左移到首位，真前缀和真后缀没有重合
                next[i++] = 0;
            }
        }
        return next;
    }
```

---

##### 判断str1和str2是否是旋转字符串

&emsp;&emsp;str1 + str2如果包含str2则是旋转字符串。

##### 判断head1是否包含head2

&emsp;&emsp;给定两棵二叉树的头节点head1和head2，想知道head1中是否有某个子树的结构和head2完全一样。
&emsp;&emsp;拿到两棵树的先序遍历字符串数组，使用kmp算法比较两个数组是否包含。
