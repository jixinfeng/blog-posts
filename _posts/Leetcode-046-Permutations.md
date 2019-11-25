---
title: Leetcode 046 permutations
date: 2017-11-08 21:47:23
categories: leetcode
tags: 
- recursion 
- dfs
---
## [题目要求][1]：
输入一组不重复的数字，输出全部可能的排列方式。例如，输入 `[1,2,3]` 输出
`[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]`。

## 思路：
可以采用[深度优先搜索（DFS）][2]的方式。如果输入数组长度为1，则直接返回该数组，否则，遍历数组，取出一个元素作为当前排列的第一位元素，然后对余下元素求全部排列，并附加到当前元素之后，可以用递归的方式简化程序。

```python
class Solution(object):
    def permute(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        if len(nums) <= 1:
            return [nums]
        solns = []
        for i, num in enumerate(nums):
            for tail in self.permute(nums[:i] + nums[i+1:]):
                solns.append([num] + tail)
        return solns
```

[1]:	https://leetcode.com/problems/permutations/description/ "Permutations"
[2]:	https://en.wikipedia.org/wiki/Depth-first_search "Depth-first search"