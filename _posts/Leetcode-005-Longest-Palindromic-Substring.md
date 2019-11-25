---
title: Leetcode 005 Longest Palindromic Substring
date: 2017-09-20 23:43:40
categories: leetcode
tags: 
- string 
- palindrome
---
## [题目要求][1]
输入字符串 `s` 要求返回其中包含的长度最长的回文子字符串

## 思路：

朴素解法亦暴力解法即遍历所有子字符串，找到最长的回文字符串，效率低，不可取

## 优化：

* 验证回文字符串时不需要检验整个字符串，只需从两边向中间，发现不一样的字母即可判断不是回文；
* 因为只需返回最长的回文字符串，因此比当前最长回文字符串短的字符串均不需检验；
* 需要考虑长度为奇数/偶数的回文检验。

程序总体思路为：从左至右循环，每次检验到第 `i` 个字符为止的比当前最长回文字符串长度多 1 和 2 的字符串，如果是回文就更新当前最长回文及其长度，最后返回结果。

``` python
class Solution(object):
    def longestPalindrome(self, s):
        """
        :type s: str
        :rtype: str
        """
        if s is None or s == "":
            return ""
        if len(s) < 2:
            return s

        currLen = 0
        currStr = ""
        for i in range(len(s)):
            if self.isPalindrome(s, i - currLen - 1, i):
                currStr = s[i - currLen - 1 : i + 1]
                currLen += 2
            elif self.isPalindrome(s, i - currLen, i):
                currStr = s[i - currLen : i + 1]
                currLen += 1
                
        return currStr

    def isPalindrome(self, s, begin, end):
        if begin < 0:
            return False

        while begin < end:
            if s[begin] != s[end]:
                return False
            else:
                begin, end = begin + 1, end - 1

        return True
```

[1]:	https://leetcode.com/problems/longest-palindromic-substring/description/ "Longest Palindromic Substring"