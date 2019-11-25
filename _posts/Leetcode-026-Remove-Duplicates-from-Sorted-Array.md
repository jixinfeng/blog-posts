---
title: Leetcode 026 Remove Duplicates from Sorted Array
date: 2017-12-10 23:35:02
tags:
- array
- two pointers
---
## [题目要求][1]：
输入一个有序，并且可能含有重复内容的数组，原位删除重复内容，空间复杂度为常数级别，并返回无重复数组长度。无重复部分之外内容不限。

## 思路：
快慢指针，快指针遍历数组，慢指针从零开始，每当遇到一个新数字时将新数字复制到当前慢指针位置并前进一位，最后返回慢指针位置加 1 。

```python
class Solution(object):
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if len(nums) <= 1:
            return len(nums)
        l = len(nums)
        slow = 0
        for fast in range(l):
            if nums[fast] != nums[slow]:
                slow += 1
            nums[slow] = nums[fast]
        return slow + 1
```

[1]:	https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/ "Remove Duplicates from Sorted Array"