---
title: Leetcode 007 Reverse Integer
date: 2017-09-24 10:21:56
categories: leetcode
tags: 
- math
---
## [题目要求][1]：
输入 `x` 为32位有符号整数，求按位反转的整数，如遇溢出则返回0

## 思路：

* 判断是0直接结束并返回0
* 判断是负数则 `neg = True` 并令 `x = -x`
* 因为64位 Python 不用担心32位整形溢出，可以初始化答案为0并不断加上 `x` 模10后乘10，直到 `x == 0` 为止，之后再判断是否超过32位整数最大值

``` python
class Solution(object):
    def reverse(self, x):
        """
        :type x: int
        :rtype: int
        """
        if x == 0:
            return x
        if x < 0:
            neg = True
            x = -x
        else:
            neg = False
        revx = 0
        while x > 0:
            revx = revx * 10 + x % 10
            x //= 10
        if len(bin(revx)[2:]) >= 32:
            return 0
        else:
            return -revx if neg else revx
```

[1]:	https://leetcode.com/problems/reverse-integer/description/ "Reverse Integer"