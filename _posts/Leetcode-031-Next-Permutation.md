---
title: Leetcode 031 Next Permutation
date: 2017-12-15 22:48:44
tags:
- array
---
## [题目要求][1]：
给出 n 个数字组成的排列 `nums` ，求出按照字典顺序的下一个排列。

## 思路：
解决此题需要搜索三个位置，先用两个变量 `i-1` 和 `i` 从后向前搜索，直至找到满足 `nums[i-1] < nums[i]` 的情况；之后再用 `j` 从后向前搜索，直到出现 `nums[j] > nums[i-1]` 。之后调换 `nums[i-1]` 和 `nums[j]` 对调，再将 `nums[i]` 之后的所有元素倒序排列，即可得到 `nextPermutation()` 即下一个排列。

```python
class Solution(object):
    def nextPermutation(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        l = len(nums)
        for i in reversed(range(l)):
            if nums[i - 1] < nums[i]:
                break
        if i > 0:
            for j in reversed(range(l)):
                if nums[j] > nums[i - 1]:
                    nums[i - 1], nums[j] = nums[j], nums[i - 1]
                    nums[i:] = nums[i:][::-1]
                    break
        else:
            nums[:] = nums[::-1]


```


[1]:	https://leetcode.com/problems/next-permutation/description/ "Next Permutation"