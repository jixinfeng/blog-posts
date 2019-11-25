---
title: Leetcode 020 Valid Parentheses
date: 2017-11-12 21:26:02
tags:
- string
- stack
---
## [题目要求][1]：
输入一个完全由 `'(', ')', '{', '}', '[', ']'` 组成的字符串，判定字符串中的括号是不是有效的。

## 思路：
创建维护一个栈，遍历一遍字符串，遇到左括号时压栈，遇到右括号时，如果栈为空或者弹出的第一个字符不是对应的左括号，返回假。遍历结束没有遇到问题则返回真。

```python
class Solution(object):
    def isValid(self, s):
        """
        :type s: str
        :rtype: bool
        """
        pstack = []
        pleft = set(['(', '[', '{'])
        for c in s:
            if c in pleft:
                pstack.append(c)
            elif c == ')':
                if pstack == [] or pstack.pop() != '(':
                    return False
            elif c == ']':
                if pstack == [] or pstack.pop() != '[':
                    return False
            elif c == '}':
                if pstack == [] or pstack.pop() != '{':
                    return False
        return len(pstack) == 0

```

[1]:	https://leetcode.com/problems/valid-parentheses/description/ "Valid Parentheses"