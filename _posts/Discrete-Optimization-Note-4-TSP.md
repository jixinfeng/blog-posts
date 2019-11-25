---
title: Discrete Optimization 学习笔记 (4) 旅行商问题
date: 2018-09-19 18:40:09
tags:
- optimization
- coursera
- tsp
- greedy
---

## 问题描述
[旅行商问题][tsp-wiki]，又称旅行推销员问题，简称 TSP ，在很多[科普读物][tsp-book]、[流行文化][tsp-xkcd]中都出现过这一问题问题的影子。旅行商问题的基本内容非常简单：一位推销员要去一些城市推销商品，已知城市的位置和两两之间的距离，希望能寻找到一条能便利所有城市，并且每个城市只去一次，最后回到出发城市的路径，以节约时间和旅行成本。

即使不经过任何转化，原始形式的旅行商问题就可以直接应用在路径规划、后勤学、芯片电路设计等场景中，稍加改动，又可以用来解决 DNA 测序等问题的子问题，因此旅行商问题是优化领域中被研究的最多的问题之一。

## 问题求解
旅行商问题是 NP 困难问题，因此不存在多项式复杂度的最优解法。对于规模较大的问题，可以采用近似解法。

### 贪心法
用贪心法解 TSP 只需选取一个城市作为起点，然后每一步都选择距离当前位置最近的城市。贪心法通常无法找到非常优秀的解，找到解平均长度比最优解长 25% 左右。但是，存在一些巧妙设计的旅行商问题，使得贪心法反而会找到最差的路径，因此贪心法的使用范围有限。

### 局部搜索法
局部搜索也可以用来求解 TSP ，最常见的方法是 2-opt ，简单来说就是对路线进行“拧麻花”式的优化。
![TSP 2-opt 优化思路][image-1]
正如图中上半部分那样， $be$ 和 $df$ 两条路线有交叉，如果能将 $cde$ 部分调转重新接入路径，则 TSP 的总距离可以减少。如果能尽可能多地尝试可能的翻转方式，如果翻转之后总路径能缩短则保留翻转并继续搜索，直至无法通过翻转部分路径缩短总距离为止，可以得到一条比较优秀的遍历所有城市的旅行路线。

很多时候， 2-opt “拧麻花” 的效果还不够好，还可以采用同时翻转更多段路径的 [k-opt][tsp-kopt] 以及可以动态调整翻转段落数的 [v-opt][tsp-vopt] 。这些搜索方法的效果更好，但复杂度也更高。 2-opt 的代码在[这里][solution]。

[tsp-wiki]: https://en.wikipedia.org/wiki/Travelling_salesman_problem "TSP Wikipedia Page"
[tsp-book]: https://www.amazon.com/Pursuit-Traveling-Salesman-Mathematics-Computation/dp/0691152705 "TSP Book"
[tsp-xkcd]: https://xkcd.com/399/ "XKCD: Travelling Salesman Problem"
[image-1]:	/images/tsp-2-opt_wiki.png "TSP 2-opt 优化思路"
[tsp-kopt]: https://en.wikipedia.org/wiki/Travelling_salesman_problem#k-opt_heuristic,_or_Lin–Kernighan_heuristics "TSP k-opt"
[tsp-vopt]: https://en.wikipedia.org/wiki/Travelling_salesman_problem#V-opt_heuristic "TSP V-opt"
[solution]: https://github.com/jixinfeng/discopt-soln/tree/master/week-04-tsp "TSP Solution"
