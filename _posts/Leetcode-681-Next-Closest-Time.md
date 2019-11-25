---
title: Leetcode 681 Next Closest Time
date: 2017-10-29 22:58:31
categories: leetcode
tags: 
- timeprocess 
- string
---
## [题目要求][1]：
输入为 `HH:MM` 格式表示的时间a，要求返回用组成时间a的数字组成的，a之后第一个时间b，可以重复使用数字，比a小的b被认为是第二天的时间。

## 思路：
利用 python 的 `set` 结构，获得组成时间的全部数字，并且不断对时间进行 +1 操作，直到遇到第一个组成数字是时间a的组成数字子集的时间b。一天只有 `24 * 60` 分钟因此程序运行时间可以保证。

``` python
class Solution(object):
    def nextClosestTime(self, time):
        """
        :type time: str
        :rtype: str
        """
        digits = set(time)
        while True:
            time = self.nextMinute(time)
            if set(time) <= digits:
                return time

    def nextMinute(self, time):
        h, m = map(int, time.split(':'))
        carry, m = divmod(m + 1, 60)
        h = (h + carry) % 24
        return "{:02d}:{:02d}".format(h, m)
```




[1]:	https://leetcode.com/problems/next-closest-time/description/ "Next Closest Time"