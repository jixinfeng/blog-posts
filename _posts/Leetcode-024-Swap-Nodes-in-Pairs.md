---
title: Leetcode 024 Swap Nodes in Pairs
date: 2017-12-10 18:05:06
tags:
- linked list
---
## [题目要求][1]：
输入一个单向链表，对相邻的两个节点进行交换。如输入 `1->2->3->4` 输出 `2->1->4->3`

## 思路：
在开始时在链表头部建立一个额外的辅助节点，用 `p` 指向它。对每一对链表节点，用 `q` 标记出来保护引用位置不至于丢失。之后交换两个节点和相应的连接即可。

```python
class Solution(object):
    def swapPairs(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if head is None or head.next is None:
            return head
        placeHolder=ListNode(0)
        placeHolder.next=head
        p=placeHolder

        while p.next and p.next.next:
            q=p.next.next
            p.next.next=q.next
            q.next=p.next
            p.next=q
            p=p.next.next
        return placeHolder.next
```

[1]:	https://leetcode.com/problems/swap-nodes-in-pairs/description/ "Swap Nodes in Pairs"