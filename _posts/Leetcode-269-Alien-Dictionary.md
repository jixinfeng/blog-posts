---
title: Leetcode 269 Alien Dictionary
date: 2017-10-31 23:40:45
categories: leetcode
tags:
- topological sorting
---
## [题目要求][1]：
一门外语由拉丁字母组成，但是他们的顺序和英语不一样。现有一个非空的单词列表，其中的单词已经根据该门外语的字母顺序排好序，尝试根据该列表推测字母顺序，输出字符串。当顺序中存在循环时输出空字符串，无法判明顺序的可以自由排列。

例如：
```
[
  "wrt",
  "wrf",
  "er",
  "ett",
  "rftt"
]
```
可以得到字母顺序：`"wertf"`

## 思路：
可以采取[拓扑排序][2]，把整个字母顺序想象成一张有向图，字母是顶点，先后顺序是边（箭头）。先找到所有入度的顶点——即顺序最靠前的字母——放入栈，每次弹出一个字母放入字母顺序表。再找出排在他们之后的字母，将他们的入度减1，如果入度变为0了就将该字母也入栈，如此循环直至队列为空。

此时如果图中仍有边，则图中存在循环，无法拓扑排序，否则排序完成，得到字母顺序表。

```python
class Solution(object):
    def alienOrder(self, words):
        """
        :type words: List[str]
        :rtype: str
        """
        if words is None or words == []:
            return ""
        chars = set().union(*map(set, words))
        outs = {c : set() for c in chars}
        indegrees = {c : 0 for c in chars}
        for i in range(len(words) - 1):
            before, after = words[i], words[i + 1]
            for j in range(min(len(before), len(after))):
                if before[j] != after[j]:
                    if after[j] not in outs[before[j]]:
                        indegrees[after[j]] += 1
                        outs[before[j]].add(after[j])
                    break

        q = [k for k, v in indegrees.items() if v == 0]
        soln = []
        while q:
            curr_char = q.pop()
            soln.append(curr_char)
            for next_char in outs[curr_char]:
                indegrees[next_char] -= 1
                if indegrees[next_char] == 0:
                    q.append(next_char)

        if max(indegrees.values()) > 0:
            return ""
        else:
            return "".join(soln)
```

[1]:	https://leetcode.com/problems/alien-dictionary/description/ "Alien Dictionary"
[2]:	https://en.wikipedia.org/wiki/Topological_sorting "拓扑排序/Topological sorting"