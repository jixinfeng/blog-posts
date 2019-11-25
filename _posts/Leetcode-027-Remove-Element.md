---
title: Leetcode 027 Remove Element
date: 2017-12-14 15:11:57
tags:
- array
- two pointers
---
## [题目要求][1]：
输入数组和一个目标数字，要求从数组中原位去掉全部目标数字，并返回新数组长度，不要使用额外的存储空间。

## 思路：
与 {% post_link Leetcode-026-Remove-Duplicates-from-Sorted-Array %} 思路一致。采用快慢指针，快指针遍历数组，慢指针从零开始，遇到非目标数字时将快指针的数值复制到慢指针处，慢指针前进一位。

``` python
class Solution(object):
    def removeElement(self, nums, val):
        """
        :type nums: List[int]
        :type val: int
        :rtype: int
        """
        if nums is None or nums == []:
            return 0
        if val not in nums:
            return len(nums)
        slow, fast = 0, 0
        for fast, fast_val in enumerate(nums):
            if fast_val == val:
                continue
            else:
                nums[slow] = nums[fast]
                slow += 1
        return slow
```

[1]:	https://leetcode.com/problems/remove-element/description/ "Remove Element"