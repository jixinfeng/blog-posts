---
title: Leetcode 006 ZigZag Conversion
date: 2017-09-23 23:33:57
categories: leetcode
tags: 
- string
---
## [题目要求][1]：
输入字符串 `s` 和行数 `numRows` ，将字符串按照给定行数进行之字形重新纵向排列后再横向重新组织成字符串。以 `s = "PAYPALISHIRING"`， `numRows = 3` 为例，之字形排列后得到
P    A   H   N
A P L S I I G
Y     I    R
重新排列后得到 `"PAHNAPLSIIGYIR"`。

## 思路：

个人认为这道题比较简单，首先判断当 `numRows == 1 or numRows>=len(s)` 时，无需处理，直接输出原始字符串即可。否则，遍历每一个字符，根据字符位置确定行号。确定方法为：

* 字符位置对 `numRows * 2 - 1` 取模 `m`，因为一个最小的 “ZigZag” 单元元素数实际上是行数两倍，少了两端的两行
* 当`m`小于行数时，表示当前 ZigZag 正处在下降阶段，直接在第 `m` 行尾附上当前字符即可
* 当 `m` 大于等于行数时，说明 ZigZag 正处在上升阶段，应在 `-(m - numRows + 2)` 即倒数 `(m - numRows + 2)` 行附加当前字符
* 将各行合并即得到最终输出

``` python
class Solution(object):
    def convert(self, s, numRows):
        """
        :type s: str
        :type numRows: int
        :rtype: str
        """
        if numRows == 1 or numRows > len(s):
            return s
        soln = [[] for i in range(numRows)]
        for i in range(len(s)):
            loc = i % (numRows * 2 - 2)
            if loc < numRows:
                soln[loc].append(s[i])
            else:
                soln[-(loc - numRows + 2)].append(s[i])
        return "".join(["".join(soln[i]) for i in range(numRows)])
```

[1]:	https://leetcode.com/problems/zigzag-conversion/description/ "ZigZag Conversion"