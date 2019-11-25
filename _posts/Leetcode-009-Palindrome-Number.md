---
title: Leetcode 046 Palindrome Number
date: 2017-11-09 11:47:04
tags:
- math
- palindrome
---
## [题目要求][1]：
输入整数x，确定该整数是否为回文数。注：负数不算回文数。

## 思路：
翻转该整数，比较翻转后整数与x是否相等。可以在翻转前排出一些简单情况。

```python
class Solution(object):
    def isPalindrome(self, x):
        """
        :type x: int
        :rtype: bool
        """
        if x < 0:
            return False
        elif x < 10:
            return True
        elif x % 10 == 0:
            return False
        else:
            resx = x
            revx = 0
            while resx:
                revx = revx * 10 + resx % 10
                resx //= 10
            return revx == x
```

[1]:	https://leetcode.com/problems/palindrome-number/description/ "Palindrome Number"