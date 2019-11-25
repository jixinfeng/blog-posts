---
title: Discrete Optimization 学习笔记 (2) 图着色问题
date: 2018-04-30 13:06:34
tags:
- optimization
- coursera
- ilp
- greedy

mathjax: true
---

## 问题描述
着色问题来自于人们绘制地图的需求。在绘制地图时，人们希望相邻的国家使用不同的颜色，并且使用尽可能少的颜色绘制整张地图。

进行数学抽象后，问题就变成了：给定一个无向图 $\mathcal{G}(\mathcal{V},\mathcal{E})$ ，其中 $\mathcal{V}$ 为顶点集合， $\mathcal{E}$ 为边集合，希望找到 $\mathcal{V}$ 的一个划分将其分成 $K$ 组，使得每个划分中的顶点均互不相邻。最小着色问题得目标即是找到满足条件的最小的 $K$ 。

与{% post_link Discrete-Optimization-Note-1-Knapsack 背包问题 %}一样，图着色问题也是 NP 完全问题，因此无法找出多项式时间的最优解法。不过通过贪心法可以快速找到高质量的次优解，并可以以此为基础通过整数规划找到最优解。

## 贪心法
用贪心法解决着色问题的基本思路非常简单，主要步骤为：

1. 通过某种贪心策略不断寻找图中的不相连点集
2. 将每次找到的点集指定一种颜色并从图中去掉这些顶点
3. 重复1、2两步直至所有定点均被着色

Python 下常用的图论分析软件包 [NetworkX][nxgreedy] 中已经提供了很多常见的贪心策略和着色函数。因此贪心法解染色问题最简单的实现就是对于给定的图 $\mathcal{G}(\mathcal{V},\mathcal{E})$ ，将所有贪心策略均尝试一遍，保留所需颜色数最小的解。贪心解法函数的 `python` 代码如下：

```python
def greedy(node_count, edges):
    graph = nx.Graph()
    graph.add_nodes_from(range(node_count))
    graph.add_edges_from(edges)

    strategies = [nx.coloring.strategy_largest_first,
                  nx.coloring.strategy_random_sequential,
                  nx.coloring.strategy_smallest_last,
                  nx.coloring.strategy_independent_set,
                  nx.coloring.strategy_connected_sequential_bfs,
                  nx.coloring.strategy_connected_sequential_dfs,
                  nx.coloring.strategy_connected_sequential,
                  nx.coloring.strategy_saturation_largest_first]

    best_color_count, best_coloring = node_count, {i: i for i in range(node_count)}
    for strategy in strategies:
        curr_coloring = nx.coloring.greedy_color(G=graph, strategy=strategy)
        curr_color_count = max(curr_coloring.values()) + 1
        if curr_color_count < best_color_count:
            best_color_count = curr_color_count
            best_coloring = curr_coloring
    return best_color_count, 0, [best_coloring[i] for i in range(node_count)]
```

## 整数规划
着色问题同样可以转换成标准 ILP 形式之后利用 gurobi 求解。构造方法可以参考一本老教材 [Applied Mathematical Programming][AMP] 中第九章 [Integer Programming][AMP-ch9] （整数规划）给出的例子。

### 标准形式
令 $N=|\mathcal{V}|$ ，设置两组变量 $y_0, y_1, \ldots, y_{N-1}$ 以及 $x_{00}, x_{01}\ldots, x_{0(N-1)},\ldots, x_{(N-1)(N-1)}$ 。其中 $y_k=1$ 表示使用第 $i$ 种颜色 $x_{ik}=1$ 表示顶点 $i$ 使用颜色 $k$ 进行着色。可以列出优化问题标准形式如下：

$$\begin{align*} 
\min  & \sum y_{i} \\
 s.t. & \sum_{k=0}^{N-1} x_{ik}=1 & i=1,\ldots, N\\
      & x_{ik} - y_k\leq 0        & i,k=1,\ldots, N \\
      & x_{ik}+x_{jk}\leq 1       & (i,j)\in\mathcal{E}\\
      & x_{ik},y_k\in\\{0,1\\}
\end{align*}$$

将题目中给定的数据按标准形式输入到 gurobi 中即可求得最优解，但是随着图规模的增加，解题所需的时间也以极快的速度增长。因此，需要对标准形式进行一些改进，进一步提高性能

### 性能优化1：去除可能的重复解
因为着色问题有对称性，因此通过标准形式可以得到很多等价的着色问题的解法。比如，有5种可能的颜色（1，2，3，4，5）可供选择时，如果最优解的着色数是3，那么选择（1，2，3）和（3，4，5）以及（1，3，5）等任意三种颜色都是等价的，但是在 ILP 中他们表示的是完全不同的解。因此需要增加一条限制：优先选择编号更小的颜色。

此外，对于一个已知的解，对调两组采用了不同颜色的顶点的着色，得到的新解和原来的解也是等价的，但是在 ILP 中对调颜色的解会被认为是不同的。因此需要增加第二条限制，编号小的颜色应该对尽可能多的顶点进行着色。

### 性能优化2：减少可选的颜色
ILP 标准形式中，由于不知道最少可以使用多少种颜色，因此在初始化时必须使用 $N$ 种颜色，即假设每个定点都需要一种不同的颜色，这样就需要 $N^2+N$ 个变量。但是根据贪心法得出的次优解可以看出：对于作业题目中给出的数据，着色所需的颜色数通常远远小于图的定点数。因此可以在初始化 ILP 之前，先使用贪心法求的次优解所需的颜色数 $m$，并按有 $m$ 个可选颜色设置 ILP ，这样可以大大减少 ILP 的规模。

### 性能优化3：用贪心法的解初始化 ILP 
在用贪心法获得了次优解之后，可以同时使用次优解的值为 ILP 各个变量赋初始值值，减少 ILP 寻找第一个可行解所需的时间。但是根据观察，这个做法带来的性能提升似乎并不明显。

完整[解题代码][solution]比较长，就不在这里贴了。

[nxgreedy]: https://networkx.github.io "NetworkX"
[AMP]: http://web.mit.edu/15.053/www/AMP.htm "Applied Mathematical Programming"
[AMP-ch9]: http://web.mit.edu/15.053/www/AMP-Chapter-09.pdf "Integer Programming"
[solution]: https://github.com/jixinfeng/discopt-soln/blob/master/week-03-coloring/solver.py "Graph Coloring Solution"
