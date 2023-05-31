---
title: manacher
author: Louis
date: 2023-04-18 19:37:02
categories: 数据结构与算法
tags: [manacher]
---

&emsp;&emsp;[manacher](https://oi-wiki.org/string/manacher/)算法，利用已经获得的最右回文边界，减少回文字符串的比较。当以某个字符为中心的回文半径特别长的时候，后续的字符到最右回文边界前才可以利用这个特性。给定一个长度为 n 的字符串 s，请找到所有对 (i, j) 使得子串 s[i $\dots$ j] 为一个回文串。当 t = t$_{\text{rev}}$ 时，字符串 t 是一个回文串（t$_{\text{rev}}$ 是 t 的反转字符串），时间复杂度O(N)。[最长回文子串](https://en.wikipedia.org/wiki/Longest_palindromic_substring)(Longest palindromic substring)

#### Manacher算法涉及的概念

- 回文直径
  - 回文字符串的长度
- 回文半径
  - 回文字符串中点到结尾的长度
- 回文半径数组
  - 字符串中每个字符的回文半径
- 最右回文边界
  - 每个字符为中心的回文字符串right，取最大值
- 最右回文边界中心点
  - 最右回文边界对应的中心点

#### Manacher算法

1. 将原字符串用特殊字符\#分割，比如"d3g3h4d3as3f"处理成"#d#3#g#3#h#4#d#3#a#s#3#f#"，特殊字符可以是任意字符，特殊字符不和原字符串比较，不会混淆。
2. 当前字符下标i，最右回文边界right、中心点middle，最右回文边界的起点left
3. i &ge; right 按照朴素算法比较
4. i &lt; right，则组成left ${ \dots }$ j ${ \dots }$ middle ${ \dots }$ i ${ \dots }$ right  的结构，j是i关于middle对称的字符下标，j的回文边界left1是不确定的
    1. left1 &gt; left，i的回文字符串在right左边，i和j的回文半径arr$[2 \times middle - i]$
    2. left1 == left，i的回文半径取right-i
    3. left1 &lt; left，i的回文半径取right-1
5. 取arr$[2 \times middle - i]$和right-1的最小值，拿到当前i位置的回文半径，然后继续朴素算法比较。因为right是最右回文半径的下一个位置，所以是right-i而不是right-i+1。
![manacher算法对称点left位置](https://www.goodserendipity.com/asserts/data-structures-and-algorithms/manacher.png)

```Java
public static int manacher2(String str) {
        if (str == null || str.length() == 0) {
            return 0;
        }
        char[] chs = manacherString(str);
        // 回文半径数组
        int[] arr = new int[chs.length];
        // 最右回文边界的中心点
        int c = -1;
        // 最右回文边界的下一个位置
        int right = -1;
        int max = Integer.MIN_VALUE;
        // 0 1 2
        for (int i = 0; i < chs.length; i++) {
            // i >= right，朴素算法
            // i < right，可以利用上一个最右回文半径
            arr[i] = right > i ? Math.min(arr[2 * c - i], right - i) : 1;
            while (i + arr[i] < chs.length && i - arr[i] > -1) {
                if (chs[i + arr[i]] == chs[i - arr[i]]) {
                    arr[i]++;
                } else {
                    break;
                }
            }
            // 更新最右回文边界
            if (i + arr[i] > right) {
                right = i + arr[i];
                c = i;
            }
            max = Math.max(max, arr[i]);
        }
        return max - 1;
    }

    // 将原字符串用特殊字符#分割
    public static char[] manacherString(String str) {
        char[] chs = str.toCharArray();
        char[] ans = new char[str.length() * 2 + 1];
        int index = 0;
        for (int i = 0; i < ans.length; i++) {
            ans[i] = (i & 1) == 0 ? '#' : chs[index++];
        }
        return ans;
    }
```

---

##### 拼接最少字符变成回文字符串

&emsp;&emsp;在指定字符串后面拼接最少的字符，使整个字符串变成回文字符串。
&emsp;&emsp;最右回文边界到达字符串末尾的时候，拼接的字符数量才是最少的。
