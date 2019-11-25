---
title: Leetcode 013 Roman to Integer
date: 2017-11-10 17:33:46
tags:
- math
- roman
- string
---
## [题目要求][1]：
输入大于0小于3999的罗马数字，输出相对应的整数。

## 思路：
结果初始化为0，从左至右每一位罗马数依次转换为整数，加至待输出的整数。除第一位以外，当前一位小于当前位时，再从结果中减去两倍的前一位数。

``` python
class Solution(object):
    romans = {'I' : 1,
              'V' : 5,
              'X' : 10,
              'L' : 50,
              'C' : 100,
              'D' : 500,
              'M' : 1000}
    def romanToInt(self, s):
        """
        :type s: str
        :rtype: int
        """
        rlen = len(s)
        if rlen == 0:
            return 0
        intlst = []
        for i in range(rlen):
            intlst.append(self.romans[s[i]])
        if rlen == 1:
            return intlst[0]
        num = 0
        for i in range(rlen):
            if i == 0:
                num += intlst[0]
                continue
            if intlst[i] <= intlst[i-1]:
                num += intlst[i]
            else:
                num += intlst[i] - 2 * intlst[i - 1]
        return num
```

[1]:	https://leetcode.com/problems/roman-to-integer/description/ "Roman to Integer"