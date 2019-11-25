---
title: Leetcode 011 Container with Most Water
date: 2017-11-09 21:40:10
tags:
- greedy
- array
- two pointers
---
## [题目要求][1]：
输入一个整数数组 `height` ，每个元素相当于一堵墙的高度，两堵墙之间距离为1，要求输出这些墙壁中能存住最多水的两堵墙

## 思路：
使用[贪心法][2]，从左右两端开始，计算最外侧两堵墙所能存住的水体积，并且每次放弃较矮的那堵墙，选择这一侧更靠近中心的下一堵墙继续尝试，直到两堵墙相遇。

```python
class Solution(object):
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        if height is None or len(height) < 2:
            return 0
        maxVol = 0
        left, right = 0, len(height) - 1
        while left < right:
            maxVol = max(maxVol, (right - left) * min(height[left], height[right]))
            if height[left] <= height[right]:
                left += 1
            else:
                right -= 1
        return maxVol
```

[1]:	https://leetcode.com/problems/container-with-most-water/description/ "Container with Most Water"
[2]:	https://en.wikipedia.org/wiki/Greedy_algorithm "Greedy Algorithm"