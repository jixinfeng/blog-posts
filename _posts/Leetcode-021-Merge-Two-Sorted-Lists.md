---
title: Leetcode 021 Merge Two Sorted Lists
date: 2017-11-12 22:44:53
tags:
- linked list
---
## [题目要求][1]：
给定两个排好序的链表，要求合并成一个新链表，元素来自于两个输入链表并且完成排序。

## 思路：
用两个指针遍历两个链表，同时新建一个链表，每次对比两个指针所在节点的数字大小，将较小的那个添加到新链表中。当一个链表遍历结束后，直接将另一个链表的全部节点附加到新链表结尾即可。

```python
class Solution(object):
    def mergeTwoLists(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        if not l1 and not l2:
            return None
        elif not l1:
            return l2
        elif not l2:
            return l1
        h = ListNode(None)
        p = h
        while l1 and l2:
            if l1.val < l2.val:
                p.next = ListNode(l1.val)
                l1 = l1.next
            else:
                p.next = ListNode(l2.val)
                l2 = l2.next
            p = p.next
        if not l1:
            p.next = l2
        else:
            p.next = l1
        return h.next
```

[1]:	https://leetcode.com/problems/merge-two-sorted-lists/description/ "Merge Two Sorted Lists"