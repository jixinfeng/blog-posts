---
title: Leetcode 018 4sum
date: 2017-11-11 23:28:22
tags:
- math
- array
- two pointers
- hash table
---
## [题目要求][1]：
输入整数数组和目标整数，求能使得和恰好为目标整数的所有四元组。

## 思路：
遍历两次全部二元组，第一次先创建一个所有二元组及其和的倒排索引，再第二次遍历，利用倒排索引查找相加之和等于当前二元组之和与目标之差的所有二元组，如果没有重复元素，则找到一个符合规则的四元组。

```python
class Solution(object):
    def fourSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        if nums is None:
            return []
        length = len(nums)
        solns = set()
        if length < 4:
            return []
        nums.sort()
        twoSums = {}
        for i in range(length):
            for j in range(i + 1, length):
                twoSum = nums[i] + nums[j]
                if twoSum in twoSums:
                    twoSums[twoSum].append([i, j])
                else:
                    twoSums[twoSum] = [[i, j]]
        for k in range(length):
            for l in range(k + 1, length):
                diff = target - nums[k] - nums[l]
                if diff in twoSums:
                    for ij in twoSums[diff]:
                        if ij[0] > l:
                            solns.add(tuple([nums[k], nums[l]] + 
                                            [nums[ij[0]], nums[ij[1]]]))
        return [list(soln) for soln in solns]
```

[1]:	https://leetcode.com/problems/4sum/description/ "4 Sum"