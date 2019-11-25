---
title: Leetcode 010 Regular Expression Matching
date: 2018-01-01 22:55:53
tags:
- string
- backtracking
- dp
- regex
---
## [题目要求][1]：
通过程序检测只包含 `.` 和 `*` 的[正则表达式][2] `p`与输入的字符串 `s` 是否匹配。
* `.` 可以可以匹配除换行符之外的任意字符
* `*` 可以匹配 `*` 之前的字符重复零次或任意多次

如：
```python
isMatch("aa","a") = False
isMatch("aa","aa") = True
isMatch("aaa","aa") = False
isMatch("aa", "a*") = True
isMatch("aa", ".*") = True
isMatch("ab", ".*") = True
isMatch("aab", "c*a*b") = True
```

## 思路：
总体来说，这是一道相当难的题目，难点是对于 `*` 的处理。根据网上的整理，此题可以用动态规划和递归法解决。

### 动态规划
动态规划的细节在 {% post_link Discrete-Optimization-Note-1-Knapsack %} 这篇文章里已经做了简单的介绍。在这道题中，由于星号的特性，一个表达式可以匹配多种长度不同的字符串，因此我们同样需要一个二维的动态规划布尔数组。其中， `dp[i][j]` 为真表示 `s[0:i]` 和 `p[0:j]` 可以匹配，为假则不能匹配。

对于只有字符和 `.` 的情况，验证匹配非常简单，直接逐个字符判断即可，难点在于对于星号的处理。

以 `isMatch("aab", "c*a*b")` 为例， `dp` 是一个4行6列的二维数组，初始化 `dp[0][0]=True` 表示空字符串可以和空表达式匹配。在循环时，内循环为 `j` 外循环为 `i` 。

每次循环时，如果不涉及到星号，则只需考虑 `s[i - 1] == p[j - 1] or p[j - 1] == '.' ` 是否成立，成立则令 `dp[i][j] = dp[i - 1][j - 1]`。

如果当前字符为 `*`，则先使 `dp[i][j] = dp[i][j - 2]` ，即让 `s[0:i]` 去匹配当前星号出现之前的表达式，也就是说让该 `*` 之前的字符出现一次；另外如果 `p[j - 1] == s[i - 1]` 或者 `p[j - 1] == . ` 则说明在 `a*` 或 `.*` 之前的字符和 `s` 当前位置字符相同，可以尝试让 `*` 之前的字符出现零次再匹配一次，即 `dp[i][j] |= dp[i - 1][j] ` 

最后得到的动态规划数组为：
``` python
[[True, False, True, False, True, False]
[False, False, False, True, True, False]
[False, False, False, False, True, False]
[False, False, False, False, False, True]]
```
取 `dp[-1][-1]` ，为真，匹配成功。

代码为：
``` python
class Solution(object):
    def isMatch(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: bool
        """
        dp = [[False for i in range(len(p) + 1)] for j in range(len(s) + 1)]
        dp[0][0] = True
        for j in range(1, len(p) + 1):
            if p[j - 1] == '*':
                dp[0][j] = dp[0][j - 2]
        for i in range(1, len(s) + 1):
            for j in range(1, len(p) + 1):
                if p[j - 1] == '*':
                    dp[i][j] = dp[i][j - 2]
                    if s[i - 1] == p[j - 2] or p[j - 2] == '.':
                        dp[i][j] |= dp[i - 1][j]
                elif p[j - 1] == s[i - 1] or p[j - 1] == '.':
                    dp[i][j] = dp[i - 1][j - 1]

        return dp[-1][-1]
```

### 递归
递归只需要从前向后不断检查字当前位置的字符以及之后的尾部字符串是否匹配，最终返回真假即可

``` python
class Solution(object):
    def isMatch(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: bool
        """
        if len(p) == 0:
            return len(s) == 0
        if len(p) == 1 or p[1] != '*':
            if len(s) == 0 or (s[0] != p[0] and p[0] != '.'):
                return False
            else:
                return self.isMatch(s[1:], p[1:])
        else:
            i = -1
            while i < len(s) and (i < 0 or p[0] == '.' or p[0] == s[i]):
                if self.isMatch(s[i+1:], p[2:]):
                    return True
                i += 1
            return False 
```

## 参考：
1. [Regular Expression Matching 解题报告][3]
2. [Leetcode 分类总结 Regular Expression Matching][4]

[1]:	https://leetcode.com/problems/regular-expression-matching/description/ "Regular Expression Matching"
[2]:	https://en.wikipedia.org/wiki/Regular_expression "Regular Expression"
[3]:	http://blog.csdn.net/magicbean2/article/details/53563571 "解题报告"
[4]:	https://lefttree.gitbooks.io/leetcode-categories/DP/regularExpressionMatch.html "分类总结"