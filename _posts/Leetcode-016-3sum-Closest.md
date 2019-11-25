---
title: Leetcode 016 3sum Closest
date: 2017-11-11 16:09:33
tags:
- math
- array
- two pointers
---
## [题目要求][1]：
输入一个数组和一个目标整数，求一个数组中元素组成的三元组，使得三元组之和与目标整数最接近，返回该整数。

## 思路：
采用与015类似的方法，先对数组排序，`i, j, k` 为三元组所在位置， `i` 从左至右移动， `j, k` 在之后从两边向中间移动，同时记录和目标的差距，保留最优值。

```python
class Solution(object):
    def threeSumClosest(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        MAXINT = 2 ** 31 - 1
        if nums is None:
            return MAXINT
        l = len(nums)
        if l < 3:
            return MAXINT
        nums.sort()
        minDiff = MAXINT
        minDiffSum = MAXINT
        for i in range(l - 2):
            if i and nums[i] == nums[i - 1]:
                continue
            j, k = i + 1, l - 1
            while j < k:
                test = nums[i] + nums[j] + nums[k]
                if test == target:
                    return target
                elif test < target:
                    j += 1
                else:
                    k -= 1
                if abs(target - test) < minDiff:
                    minDiff = abs(target - test)
                    minDiffSum = test
        return minDiffSum

```

[1]:	https://leetcode.com/problems/3sum-closest/description/ "3Sum Closest"