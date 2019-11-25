---
title: Leetcode 003 Longest Substring Without Repeating Characters
date: 2017-09-13 21:15:55
categories: leetcode
tags: 
- hash table
- dp
- two pointers
- string
---
## [题目要求][1]：
输入字符串 `s` 要求返回输入中不含重复字符的最长子字符串

## 思路：

朴素解法亦暴力解法即遍历所有子字符串，找到最长的无重复字符串，一共需要考察 O(n^2) 个字符串，效率较低。

## 优化：

采取动态规划策略。遍历一次字符串并且维护一个字典，记录每个遇到的字母最后一次出现的位置；以及两个变量，分别记录当前无重复字符串的开始位置和当前最长无重复字符串的位置。因为要求字符串无重复、无间断，因此遇到出现过的字母时，当前字符串的起始位置要变到上一次出现的位置之后。并且每遍历一个字母都更新一次当前、最大无重复字符串的长度。最后返回最大字符串长度。

``` python
class Solution(object):
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        l = len(s)
        if l < 2:
            return l
        lastCharPos = {}
        maxStrLen = 0
        currStrStart = 0
        for i, ch in enumerate(s):
            if ch in lastCharPos and lastCharPos[ch] >= currStrStart:
                currStrStart = lastCharPos[ch] + 1
            lastCharPos[ch] = i
            maxStrLen = max(maxStrLen, i - currStrStart + 1)
        return maxStrLen
```

[1]:	https://leetcode.com/problems/longest-substring-without-repeating-characters/description/ "Longest Substring Without Repeating Characters"