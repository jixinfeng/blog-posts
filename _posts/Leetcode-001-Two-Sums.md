---
title: Leetcode 001 Two Sums
date: 2017-09-10 16:50:05
categories: leetcode
tags: 
- array
- hash table
---

## [题目要求][1]：
输入为一个整数数组 `nums` 和一个整数 `target` ，要求返回 `nums` 中相加得到 `target` 的两个数的位置。可以假定输入数据存在唯一解。

## 思路：

朴素解法就是双重 for 循环，复杂度 O(n^2) 。

## 优化：
* 对于 `nums` 建立索引，key 是数值，value 是位置
* 对于 `nums` 中每个数求与 `target` 的差，如果差存在于索引中就可获得答案
* 要排除位置相同的数，如输入 `[2, 1, 3], 4` 则不应返回 `[0, 0]` 而应返回 `[1, 2]`
* Python 中的 dict 按 key 查询是 O(1) 操作，因此整个算法复杂度为 O(N)

代码如下：
``` python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        index = {}
        for i, j in enumerate(nums):
            index[j] = i
        
        for i, j in enumerate(nums):
            if target - j in index and index[target - j] != i:
                return sorted([i, index[target - j]])
```

[1]:	https://leetcode.com/problems/two-sum/description/ "Two Sum"