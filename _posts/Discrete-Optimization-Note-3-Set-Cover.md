---
title: Discrete Optimization 学习笔记 (3) 集合覆盖问题
date: 2018-09-14 18:12:59
tags:
- optimization
- coursera
- ilp
- greedy

mathjax: true
---

## 问题描述
集合覆盖问题是一个经典的组合优化问题，问题通常可以描述为：给定全集 $\mathcal{U}$ 以及 $\mathcal{S}=\{s_i\in\mathcal{U} \mid s_1, s_2, \ldots, s_n\}$ 并且 $\cup_{i=1}^{n} s_i=\mathcal{U}$ 。集合覆盖问题的目标就是找到一个 $\mathcal{S}$ 的最小子集 $\mathcal{C}$，使得 $\mathcal{C}$ 的并集为 $\mathcal{U}$ 。

## 贪心法
贪心法可以用来求解集合覆盖问题，并且思路很简单：每次选择覆盖了最多尚未被覆盖元素的集合即可。集合覆盖问题是 $\mathcal{NP}$ 完全问题，因此贪心法只能得到 $\mathcal{O}(\log n)$ 近似的近似解。这也是多项式时间内能得到的[最好结果][greedy-scp]了。

## 整数规划
虽然时间复杂度很高，但整数规划可以求出集合覆盖的最优解，并且形式比较简单，在 gurobi 的帮助下，解决一定规模的问题需要的时间还是可以接受的。

令 $M=|\mathcal{U}|$ ，$N=|\mathcal{S}|$ ，设置变量 $x_0, x_1, \ldots, x_{N-1}$ 和系数 $a_{00}, a_{01}\ldots, a_{0(M-1)},\ldots, a_{(N-1)(M-1)}$ 。其中 $x_i=1$ 表示选择第 $i$ 个集合，反之不选，$a_{ij}=1$ 表示集合 $i$ 中包含 $\mathcal{U}$ 中第 $j$ 个元素，反之没有。为了保证 $\mathcal{U}$ 中所有元素均被覆盖，只需保证每个元素都被包含在至少一个选中的子集中，因此集合覆盖问题的标准形式就可以写出标准形式如下：

$$\begin{align*} 
\min  & \sum i_{i} \\
 s.t. & \sum_{i=0}^{N-1} a_{ij}x_i\geq 1 & j=1,\ldots, M
\end{align*}$$

完整的[解题代码][solution]在 Github 上。

[greedy-scp]: http://pages.cs.wisc.edu/~shuchi/courses/787-F07/scribe-notes/lecture02.pdf "Greedy Set Cover"
[solution]: https://github.com/jixinfeng/discopt-soln/blob/master/week-03-setcover/solver.py "Set Cover Solution"
