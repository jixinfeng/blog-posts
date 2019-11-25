---
title: Leetcode 002 Add Two Numbers
date: 2017-09-13 20:16:35
categories: leetcode
tags: 
- linked list
- math
---
## [题目要求][1]：

输入为两个用单向链表表示的非负整数 `l1` 和 `l2`，整数在链表上由低位至高位进行储存，要求输出两个整数的和，同样以低位至高位的单向链表表示。

## 思路：

此题直接用手算加法的方式即可解决。输入的两个整数是由低位到高位储存的符合手算的顺序，题目中保证了输入非空也给我们提供了方便，不需要考察边界条件，计算时需维护一个“进位”的变量，并且要注意最后。

``` python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        solnHead = ListNode(None)
        carry = 0
        p = solnHead
        while l1 or l2 or carry:
            p.next = ListNode(carry)
            if l1:
                p.next.val += l1.val
                l1 = l1.next
            if l2:
                p.next.val += l2.val
                l2 = l2.next
            carry, p.next.val = divmod(p.next.val, 10)
            p = p.next
        return solnHead.next
```

[1]:	https://leetcode.com/problems/add-two-numbers/description/ "Add Two Numbers"