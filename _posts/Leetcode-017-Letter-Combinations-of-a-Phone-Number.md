---
title: Leetcode 017 Letter Combinations of a Phone Number
date: 2017-11-11 23:13:39
tags:
- string
- combination
- permutation
- recursion
- backtracking
---
## [题目要求][1]：
给定电话号码键盘上输入的一组数字字符串，求对应按键上所有可能的字母组合。

## 思路：
采用迭代方法，当数字长度为1时，返回所有字母，否则对从第二个数字开始的字符串继续应用函数，把返回的字符串分别附加到第一个数字对应的字母后，组成字符串，加到答案中并返回。

```python
class Solution(object):
    letters = {'2' : ['a', 'b', 'c'],
               '3' : ['d', 'e', 'f'],
               '4' : ['g', 'h', 'i'],
               '5' : ['j', 'k', 'l'],
               '6' : ['m', 'n', 'o'],
               '7' : ['p', 'q', 'r', 's'],
               '8' : ['t', 'u', 'v'],
               '9' : ['w', 'x', 'y', 'z']}

    def letterCombinations(self, digits):
        """
        :type digits: str
        :rtype: List[str]
        """
        if digits is None or digits == '':
            return []
        if len(digits) == 1:
            return self.letters.get(digits[0], [])
        solns = []
        if digits[0] in self.letters:
            for soln in self.letterCombinations(digits[1:]):
                for ch in self.letters[digits[0]]:
                    solns.append(ch + soln)
        else:
            for soln in self.letterCombinations(digits[1:]):
                solns.append(soln)
        return solns
```

[1]:	https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/ "Letter Combinations of a Phone Number"