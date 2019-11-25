---
title: Leetcode 028 Implement strStr()
date: 2017-12-14 16:02:03
tags:
- string
- two pointers
---
## [题目要求][1]：
输入两个字符串名叫 `needle` 和 `haystack` ，找到 `needle` 出现在 `haystack` 中的位置，如果没有出现则返回 -1 。字符串取名出自英语的 *Needle in a haystack* 即“大海捞针”。

## 思路：
对于这样的问题，高级算法如 [KMP][2] 可以把复杂度控制在 $O(m+n)$ 。但是 KMP 极难理解，也很难在面试中一次写出不出 bug 。因此，解决这个问题时用比较朴素的比较算法即可。

``` python
class Solution(object):
    def strStr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype: int
        """
        if haystack is None or needle is None:
            return -1
        l1 = len(needle)
        l2 = len(haystack)
        if l1 == 0 or haystack == needle:
            return 0
        elif l1 > l2:
            return -1
        for i in range(l2 - l1 + 1):
            if haystack[i:i + l1] == needle:
                return i
        return -1
```

[1]:	https://leetcode.com/problems/implement-strstr/description/ "Implement strStr()"
[2]:	https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm "KMP Algorithm"