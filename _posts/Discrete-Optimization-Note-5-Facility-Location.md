---
title: Discrete Optimization 学习笔记 (5) 设施选址问题
date: 2018-09-20 22:43:22
tags:
- optimization
- coursera
- ilp

mathjax: true
---

## 问题描述
[设施选址问题][fl-wiki]是一个比较具有现实意义的优化问题。问题可以通过 $N$ 个备选的供应设施位置和 $M$ 个客户来描述。选定一个候选位置建立供应设施 $f$ 的时候需要付出 $s_f$ 的建设成本，并且设施的最大供应量不能超过 $cap_f$ ；与之对应每个客户 $c$ 均有一个需求值 $d_c$ 需要被满足；设施 $f$ 和客户 $c$ 之间的距离为两者之间的欧几里得距离 $d(f, c)$ ，送货成本等于送货距离。题目要求每个客户的需求必须满足，且至多只能接收一个供应设施的送货，同时所有设施均不能超负荷运转，即供应量不应超过设施容量。优化目标是在满足所有条件的情况下求出成本最低的设施选址以及送货安排。

## 整数规划
此题直接通过整数规划就能获得理想的结果。建立规划模型时，需要分别添加两组变量，一组用来表示是否在一个备选位置上建立设施，共 $N$ 个 0-1 变量，另一组用来表示每个客户接受的供应设施，共 $N\times M$ 个 0-1 变量。设置好变量后，将设施位置成本、容量，客户需求以及每个位置和客户之间的距离设置在限制条件中直接求解即可得到最优解，详见[完整代码][solution]

[fl-wiki]: https://en.wikipedia.org/wiki/Facility_location_problem "Facility Location Problem Wiki Page"
[solution]: https://github.com/jixinfeng/discopt-soln/blob/master/week-06-facility/solver.py "Facility Location Solution"
