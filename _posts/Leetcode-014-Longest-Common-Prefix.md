---
title: Leetcode 014 Longest Common Prefix
date: 2017-11-10 17:49:45
tags:
- string
- prefix
---
## [题目要求][1]：
从给定的一组字符串中，确定最长的公共前缀（LCP）。

## 思路：
先找出最短的字符串，然后从头开始测试每个字符，如果出现了最短字符串中的字符在其他字符串中相应位置没有出现的情况，该字符之前就是 LCP ，否则该最短字符串就是 LCP。

``` python
class Solution(object):
    def longestCommonPrefix(self, strs):
        """
        :type strs: List[str]
        :rtype: str
        """
        if not strs:
            return ""
        shortest = min(strs, key=len)
        for i, ch in enumerate(shortest):
            for other in strs:
                if other[i] != ch:
                    return shortest[:i]
        return shortest
```

[1]:	https://leetcode.com/problems/longest-common-prefix/description/ "Longest Common Prefix"