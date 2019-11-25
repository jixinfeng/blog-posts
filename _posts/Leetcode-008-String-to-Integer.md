---
title: Leetcode 008 String to Integer (atoi)
date: 2017-09-25 10:00:39
categories: leetcode
tags: 
- string 
- regex
- math
---
## [题目要求][1]：
实现 [`atoi`][2] 函数，注意考虑各种可能情况。

## 思路：

可以直接采用正则表达式。先去掉首尾空格，行首可以有正负号也可以没有，之后接着一个或多个数字。匹配完成后，如果超过32位整数限制，则变成32位有符号整数的最大/最小值。

``` python
import re
class Solution(object):
    def myAtoi(self, str):
        """
        :type str: str
        :rtype: int
        """
        str = str.strip()
        match = re.findall('^([+-]?\d+).*$', str)
        if match == []:
            num = 0
        else:
            num = int(match[0])
        if num >= 2 ** 31:
            num = (2 ** 31) - 1
        elif num <- 2 ** 31:
            num =- 2 ** 31
        return num
```

[1]:	https://leetcode.com/problems/string-to-integer-atoi/description/ "String to Integer"
[2]:	https://en.wikibooks.org/wiki/C_Programming/stdlib.h/atoi "atoi on Wikibooks"
