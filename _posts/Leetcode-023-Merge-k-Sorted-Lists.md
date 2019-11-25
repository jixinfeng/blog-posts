---
title: Leetcode 023 Merge k Sorted Lists
date: 2017-12-08 15:17:41
tags:
- linked list
- divide and conquer
- heap
---
## [题目要求][1]：
输入多个排好序的链表，合并成一个排好序的链表。

## 思路：
因为每个链表已经排好序，所以可以采用堆排序加快合并速度。首先将各个链表的表头值和链表编号放入最小堆，按表头值排序。每次取出一个最小值，将其对应的表头放入合并后的链表，并将对应编号的链表的表头向后移动一个位置，再将新表头值和编号放回最小堆。直至所有链表表头均为空，合并完毕。

```python
class Solution(object):
    def mergeKLists(self, lists):
        """
        :type lists: List[ListNode]
        :rtype: ListNode
        """
        if lists is None or lists == []:
            return None
        mergeCache = []
        p = ListNode(None)
        q = p

        for i in range(len(lists)):
            if lists[i]:
                heapq.heappush(mergeCache, (lists[i].val, i))

        while mergeCache:
            val, head_i = heapq.heappop(mergeCache)
            q.next = lists[head_i]
            q = q.next
            lists[head_i] = lists[head_i].next
            if lists[head_i]:
                heapq.heappush(mergeCache, (lists[head_i].val, head_i))

        return p.next
```

[1]:	https://leetcode.com/problems/merge-k-sorted-lists/description/ "Merge k Sorted Lists"