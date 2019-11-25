---
title: 并查集与合并-查找算法的 Python 实现
date: 2018-01-18 16:06:02
tags:
- graph
- unionfind
- tree

mathjax: true
---

## 简介
在很多和网络有关的场景中，我们常常需要进行一类操作：记录节点之间是否直接相连，然后计算两个节点之间是否能通过某些邻居而间接连接。比如：在社交网络中，我们希望能够快速知道两个人是否可以通过某些共同好友而建立联系；在地图中，我们希望能够快速知道两地之间是否存在一条通路。如果我们只需要判断这样的连接是否存在而不需要知道具体的连接路径，基于[并查集][1]的合并-查找算法 (Union Find) 就是一种非常有用的方法。

## 数据结构和方法
并查集是一种树形的数据结构，其思路就是维护一颗节点的树，当两个不直接相连的节点具有相同的根结点时，就可以认为两个节点存在连接。顾名思义， Union Find 需要用到两个方法：

* `union` 即合并，负责记录节点之间的连接关系
* `find` 即查找，负责查询输入的两个点之间是否存在相连关系

同时，在初始化时还要制定节点数 `n` 。

## Quick Find
Quick Find 可以保证在常数时间复杂度内完成查找，采用的思路是维护一颗扁平的树：
在数据结构中添加一个数组 `idx` 负责记录每个节点的父节点，根节点的 `idx` 值就是自己，即 `idx[i]=i`。在每次执行一次 `union` 操作时，如 `union(p, q)` 将 `idx[p]` 的值设置为 `q` ，并且遍历所有节点，如果 `idx[i]==p` 即 `p` 是 `i` 的父节点，则重新设置 `idx[i]=q` 。保证每个节点的父节点就是根结点。查找时，调用 `find(a, b)` ，只需判断 `idx[a]==idx[b]` 即可，复杂度为 $O(1)$。

虽然 Quick Find 可以非常高效地进行查找，但是每次添加一条新边时，都要扫描一遍所有节点的父节点，添加每个节点的连接关系时都需要 $O(N)$ 次操作，扩展性较差。

Quick Find 的代码为：

``` python
class quickFind(object):
    def __init__(self, n):
        self.nodes = n
        self.idx = list(range(n))
        self.parts = n

    def union(self, edge):
        p, q = edge[0], edge[1]
        idxp, idxq = self.idx[p], self.idx[q]
        if idxp == idxq:
            return

        for i in range(n):
            if self.idx[i] == idxp:
                self.idx[i] == idxq
        self.parts -= 1

    def find(self, p, q):
        return self.idx[p] == self.idx[q]
```

## Quick Union
Quick Find 的 union 操作效率太低，为了提升 union 效率，可以改用 Quick Union 的做法。 Quick Union 不能保证在常数时间复杂度内完成查找，但是可以快速添加新边。

Quick Union 不再保证树是扁平的，而是在每次执行 `union(p, q)` 时，找到 `p` 和 `q` 所在树的根节点 `rp` 、 `rq` ，并且将 `rq` 设置为 `rp` 的父节点，在查找时，同样找到对比两个点所在的根结点即可。 Quick Union 的时间复杂度就完全取决于树的深度，但是对树的平衡性完全没有控制，因此在最坏情况下，输入的数据是一条链形的结构，即树变成了链表，那么 `union` 和 `find` 的复杂度都会变成 $O(N)$ ，比 Quick Find 还要差。

Quick Union 的代码如下，由于经产需要查找根结点的操作，因此单独定义了 `root` 函数。
``` python
class quickUnion(object):
    def __init__(self, n):
        self.nodes = n
        self.idx = list(range(n))
        self.parts = n

    def root(self, node):
        while node != self.idx[node]:
            node = self.idx[node]
        return node

    def union(self, edge):
        p, q = edge[0], edge[1]
        rootp, rootq = self.root(p), self.root(q)
        if rootp == rootq:
            return
        else:
            self.idx[rootp] = rootq
            self.parts -= 1

    def find(self, p, q):
        return self.root(p) == self.root(q)
```

## Weighted Quick Union
Quick Union 的时间复杂度高度依赖于树的高度，但是对树的平衡性完全没有控制，会无条件地将 `rq` 设置为 `rp` 的父节点。因此，如果能让并查集的树形结构尽可能平衡，树的高度数量级就不会超过 $O(log N)$ ，可以有效降低时间复杂度。

为了实现这一点，我们需要在并查集中增加一个 `size` 数组，记录每个节点及其子节点的数量总和，这样当合并两棵树时，只需要比较两个根结点的 `size` 值，将尺寸较小树的根结点的父节点指向尺寸较大树的根结点，就可以尽量保持树的平衡性了。代码如下：

``` python
class weightedQuickUnion(object):
    def __init__(self, n):
        self.nodes = n
        self.idx = list(range(n))
        self.size = [1] * n
        self.parts = n

    def root(self, node):
        while node != self.idx[node]:
            node = self.idx[node]
        return node

    def union(self, edge):
        p, q = edge[0], edge[1]
        rootp, rootq = self.root(p), self.root(q)
        if rootp == rootq:
            return
        elif self.size[rootp] > self.size[rootq]:
            self.idx[rootq] = rootp
            self.size[rootp] += self.size[rootq]
        else:
            self.idx[rootp] = rootq
            self.size[rootq] += self.size[rootp]
        self.parts -= 1

    def find(self, p, q):
        return self.root(p) == self.root(q)
```

## Weighted Quick Union with Path Compression
Weighted Quick Union 的效果已经很好了，但是并查集的潜力还没有完全发掘出来：既然每次运行 `root` 方法时，都会找到根结点，那何不直接将途中找到的所有节点直接指向该根结点呢？这样做的代价是：由于 `root` 方法会经常被调用，频繁地生成和销毁中间节点数组带来的成本会很高。因此我们采用一种开销更低，但是效果接近的路径压缩做法：在寻找根结点时，将所有节点的父节点换成其祖父节点，这样每次执行 `root` 时，路径的长度都会被压缩一次，额外的内存开销为常数，并且额外的代码只有一行。我们只需要把 `root` 稍作修改即可：

``` python
def root(self, node):
    while node != self.idx[node]:
        # 将祖父节点变成父节点
        self.idx[node] = self.idx[self.idx[node]] 
        node = self.idx[node]
    return node
```

经过简单的修改，带来的性能提升是惊人的，经过路径压缩之后，`union` 和 `find` 复杂度的一个比较宽松的上界是 $O(log ^* N)$ ，即[迭代对数][2]，其定义为对 $N$ 反复进行对数操作，得到一个小于 1 的结果所需进行的对数运算次数，另一个更紧凑的上界是 $O(\\alpha(N))$ 即[反阿克曼函数 ][3]。这两个函数的增长速度都极为缓慢，对于我们通常能见到的数量级的输入，可以认为这两个函数的值不会超过 5 ，如 $log ^* 2^{65536}=5 $ 。也就是说，经过路径压缩的并查集，可以做到在非常接近常数复杂度的速度下实现 `union` 和 `find` 。

## 复杂度列表
| Algorithm | Constructor | Union | Find |
|:---:|:---:|:---:|:---:|
| Quick Find | $O(N)$ | $O(N)$ | $O(1)$ |
| Quick Union | $O(N)$ | $O(h)$* | $O(h)$ |
| Weighted Quick Union | $O(N)$ | $O(log N)$ | $O(log N)$ |
| Weighted Quick Union with Path Compression | $O(N)$ | $O(\\alpha(N))$ | $O(\\alpha(N))$ |
* $h$ 是树的高度

## 参考资料
[Case Study in Algorithms by Robert Sedgewick][4]

[1]:	https://en.wikipedia.org/wiki/Disjoint-set_data_structure "Union Find"
[2]:	https://en.wikipedia.org/wiki/Iterated_logarithm "Iterated logarithm"
[3]:	https://en.wikipedia.org/wiki/Ackermann_function#Inverse "Inverse Ackermann Function"
[4]:	https://algs4.cs.princeton.edu/15uf/ "Case Study"
