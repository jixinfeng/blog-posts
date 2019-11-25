---
title: Leetcode 019 Remove Nth Node From End of List
date: 2017-11-12 20:22:57
tags:
- linked list
- two pointers
---
## [题目要求][1]：
输入链表的起始节点和整数n，需要去掉从链表结尾起第n个节点，然后返回新链表的起始节点。

## 思路：
用双指针方法确定位置，第一个指针先从头至尾移动n个节点，第二个指针开始移动，两个指针同步移动直至第一个指针到达链表尾部，第二个指针就到达了距离尾部距离n的节点，删除即可。

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def removeNthFromEnd(self, head, n):
        """
        :type head: ListNode
        :type n: int
        :rtype: ListNode
        """
        placeHolder = ListNode(0)
        placeHolder.next = head
        p = q = placeHolder
        for i in range(n):
            p = p.next
        while p.next:
            p, q = p.next, q.next
        q.next = q.next.next
        return placeHolder.next
```

[1]:	https://leetcode.com/problems/remove-nth-node-from-end-of-list/description/ "Remove Nth Node From End of List"