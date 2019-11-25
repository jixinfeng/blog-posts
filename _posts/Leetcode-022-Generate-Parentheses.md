---
title: Leetcode 022 Generate Parentheses
date: 2017-12-05 23:23:17
tags:
- string
- backtracking
- dfs
---
## [题目要求][1]：
输入整数 `n` 要求输出所有合理的由 `n` 对括号组成的字符串。

## 思路：
采用深度优先搜索（DFS），初始化时左右括号剩余数量均为 `n` ，搜索中，如果左右括号剩余数均为0，则找到一组解，记录在答案中；如果还剩余左括号，则在当前搜索字符串尾增加一个左括号并继续搜索；同时如果剩余左括号数量小于剩余右括号数量，则在当前搜索字符串尾增加一个右括号并继续搜索，直至所有情况搜索完成，返回答案

```python
class Solution(object):
    def generateParenthesis(self, n):
        """
        :type n: int
        :rtype: List[str]
        """
        self.soln = []
        if n == 0:
            return self.soln
        self.dfs(n, n, '')
        return self.soln

    def dfs(self, left, right, s):
        if left == 0 and right == 0:
            self.soln.append(s)
        if left > 0:
            self.dfs(left - 1, right, s + '(')
        if left < right:
            self.dfs(left, right - 1, s+ ')')
```

[1]:	https://leetcode.com/problems/generate-parentheses/description/ "Generate Parentheses"