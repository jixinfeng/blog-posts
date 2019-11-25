---
title: Leetcode 015 3 Sum
date: 2017-11-11 15:13:18
tags:
- math
- array
- two pointers
---
## [题目要求][1]：
输入数组和，求出数组中所有不重复的三元组，使得三元组只和为0。

## 思路：
先对全部数组排序，假设三元组的对应的位置是 `i, j, k` ，令 `i` 从左至右循环，`j, k` 从 `i` 右侧的两边向中间循环。遇到和为0的三个元素则记录下并将 `j, k`继续向中间循环直至遇到不重复的元素，如和小于零则向右移动 `j`，否则向左移动 `k`。

```python
class Solution(object):
    def threeSum(self, nums):
        if nums is None or nums == []:
            return []
        if len(nums) < 3:
            return []
        nums.sort()
        solns = []
        l = len(nums)
        for i in range(0, l - 2):
            if i and nums[i] == nums[i - 1]:
                continue
            j, k= i + 1, l - 1
            while j < k:
                test = nums[i] + nums[j] + nums[k]
                if test == 0:
                    soln = [nums[i], nums[j], nums[k]]
                    solns.append(soln)
                    j += 1
                    while j < k and nums[j] == nums[j - 1]:
                        j += 1
                    k -= 1
                    while j < k and nums[k] == nums[k + 1]:
                        k -= 1
                elif test > 0:
                    k -= 1
                else:
                    j += 1
        return solns
```

[1]:	https://leetcode.com/problems/3sum/description/ "3 Sum"