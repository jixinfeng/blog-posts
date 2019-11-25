---
title: Leetcode 012 Integer to Roman
date: 2017-11-10 14:09:05
tags:
- math
- roman
- string
---
## [题目要求][1]：
输入大于0小于3999的整数，输出相对应的[罗马数字][2]。

罗马数字的拼写规则：
* 重复数次：一个罗马数字重复几次，就表示这个数的几倍
* 右加左减：
	* 在较大的罗马数字的右边记上较小的罗马数字，表示大数字加小数字
	* 在较大的罗马数字的左边记上较小的罗马数字，表示大数字减小数字
	* 左减的数字有限制，仅限于I、X、C。比如45不可以写成VL，只能是XLV
	* 但是，左减时不可跨越一个位值。比如，99不可以用IC表示，而是用XCIX表示。（等同于阿拉伯数字每位数字分别表示。）
	* 左减数字必须为一位，比如8写成VIII，而非IIX
	* 右加数字不可连续超过三位，比如14写成XIV，而非XIIII（见下方“数码限制”一项）
* 数码限制：
	* 同一数码最多只能连续出现三次，如40不可表示为XXXX，而要表示为XL
	* 例外：由于IV是古罗马神话主神朱庇特（即IVPITER，古罗马字母里没有J和U）的首字，因此有时用IIII代替IV

## 思路：
因为数字有上限3999，因此可以按1000、100、10、1为单位逐步转换，注意4和9的特殊情况即可

```python
class Solution(object):
    def intToRoman(self, num):
        """
        :type num: int
        :rtype: str
        """
        if num is None or num == 0:
            return ""
        integers = [1000, 100, 10, 1]
        romans = [['M'], ['C', 'D'], ['X', 'L'], ['I', 'V']]
        roman = []
        for i, j in enumerate(integers):
            if num < j:
                continue
            digit = num // j
            if digit < 4:
                for k in range(digit):
                    roman.append(romans[i][0])
            elif digit == 4:
                roman.append(romans[i][0])
                roman.append(romans[i][1])
            elif digit < 9:
                roman.append(romans[i][1])
                for k in range(digit - 5):
                    roman.append(romans[i][0])
            else:
                roman.append(romans[i][0])
                roman.append(romans[i - 1][0])
            num %= j
        return ''.join(roman)
```

[1]:	https://leetcode.com/problems/integer-to-roman/description/ "Integer to Roman"
[2]:	https://en.wikipedia.org/wiki/Roman_numerals "Roman Numerals"