---
title: Discrete Optimization 学习笔记 (1) 背包问题
date: 2017-12-06 00:08:07
tags:
- optimization
- coursera
- dp
- ilp
- greedy
---
Coursera 是由斯坦福大学的教授吴恩达（[Andrew Ng][1]）和达芙妮·科勒联（[Daphne Koller][2]）合创建的一个营利性的教育科技公司，与很多大学都有合作。经过几个月的尝试，我终于在昨天晚上通过了[Coursera][3] 上由墨尔本大学提供的[Discrete Optimization][4]这一门课，拿到了[课程认证][5]。虽然最终的成绩离满分还很远，但是我觉得把我在解决作业问题中的思路记录下来是有一定意义的。我的[作业代码][6]保存在了 github 上，欢迎 star 、 fork 、发 pull request 。

## 背包问题的描述
假设我们有一个容量为 $K$ 的背包，以及 $n$ 个物品，每个物品有重量 $w\_i$ 和价格 $v\_i$ ，题目的目的是找到一个背包可以装下的物品组合（总体积不超过背包容量），并且总价格最高。

在作业中，输入数据为背包总容量 `cap` 和物品列表 `items` 每个物品有 `value` 、 `weight` 、 `density` 三个属性。输出数据为背包总价值 `value` 以及长度等于 `items` 的数组 `taken` ，其中元素为 1 则表示对应物品放入背包中，否则不放。

背包问题是[NP完全问题][7]，不存在“高效”的多项式时间的最优解法，但是可以通过动态规划实现比较高效的解法，或者用整数规划软件包高速求解。

## 朴素解法
求解背包问题最朴素的办法就是尝试所有可能的物品组合，找到符合要求的最优解。由于每个物品有两种状态，即放进背包和不放进背包，因此朴素解法需要尝试 $2^n$ 种不同的组合才能保证找到最优解，效率极低。

## 贪心法 （Greedy Algorithm）
[贪心法][8]的原理就是在每一步的时候都采取当前状态下最优的选择，并期望最终能达到比较好的结果。这样做的有点是速度快，对于背包问题来讲，无论是基于物品价格的贪心还是价格密度的贪心，只要计算完成就可以不断向背包中添加物品直至无法添加任何一件新物品。缺点则是并不能达到最优，并且性能没有很好的保障，只能得到近似解或次优解。这里以密度贪心算法举例：

```python
Item = namedtuple("Item", ['index', 'value', 'weight', 'density'])

def greedy(cap, items):
    n = len(items)
    taken = [0] * n
    filled = 0
    value = 0
    for item in sorted(items, key=attrgetter('density')):
        if filled + item.weight <= cap:
            taken[item.index] = 1
            value += item.value
            filled += item.weight
    return value, 0, taken
```

## 动态规划 （Dynamic Programming, DP）
[动态规划][9]可以比朴素算法更快地得到背包问题的最优解，但是对于规模较大的问题仍然性能极低并且有极高的内存占用。适用于动态规划的问题需要有这样的性质：
1. **最优子结构性质**：如果问题的最优解所包含的子问题的解也是最优的，我们就称该问题具有最优子结构性质（即满足最优化原理）
2. **无后效性**：即子问题的解一旦确定，就不再改变，不受在这之后、包含它的更大的问题的求解决策影响
3. **子问题重叠性质**：子问题重叠性质是指在用递归算法自顶向下对问题进行求解时，每次产生的子问题并不总是新问题，有些子问题会被重复计算多次。动态规划算法正是利用了这种子问题的重叠性质，对每一个子问题只计算一次，然后将其计算结果保存在一个表格中，当再次需要计算已经计算过的子问题时，只是在表格中简单地查看一下结果，从而获得较高的效率

用动态规划求解背包问题的思路是：

* 令 $S(k, j)$ 为求容量为 $k$ ，有 $j$ 个可选物品的背包问题最优解的函数
* 假设已知 $S(k, j-1)$ 的值，即已经解决了容量为 $k$ ，有 $j-1$ 个可选物品的背包问题。在引入第 $j$ 个物品后：
	* 如果 $w\_j\>k$ 则背包无法放下新物品， $S(k, j)=S(k, j-1)$
	* 否则，比较选择放入 $j$ 和不放 $j$ 的函数值，即 $$S(k, j)=\max(S(k, j-1), v\_j+S(k-w\_j, j-1))$$ 其中放入 $j$ 时的总价值计算思路是先找 $S(k-w\_j, j-1)$ ，该问题已经求解可以直接查表，再加上 $v\_j$ 即可
* 初始化 $S(k, 0)=0$
* 当 $k=K, j=n$ 即所有物品已经放完，开始从 $S(K, n)$ 开始反向查找确定每个物品是否被选中：
	* 如果 $S(k, j)=S(k, j-1)$ ，说明第 $j$ 个物品没有被选中，从 $S(k, j-1)$ 继续查找
	* 否则，说明被选中，从 $S(k-w\_j, j-1))$ 继续查找

这里用一组简单的数据举例：
* 背包容量 9
* 三个物品，重量分别为 4、5、2，价值分别为5，6，3

|k\\j|0|1|2|3|
|:---:|:---:|:---:|:---:|:---:|
|0|0|0|0|0|
|1|0|0|0|0|
|2|0|0|0|3|
|3|0|0|0|3|
|4|0|**5**|5|5|
|5|0|5|**6**|6|
|6|0|5|6|8|
|7|0|5|6|9|
|8|0|5|6|9|
|9|0|5|**11**|**11**|

经过列动态规划表之后，可以看出，最优解选择了前两个物品，包中物品总价值为 11

解题代码如下：
```python
Item = namedtuple("Item", ['index', 'value', 'weight', 'density'])

def dp(cap, items):
    n = len(items)
    taken = [0] * n
    values = [[0 for j in range(cap + 1)] for i in range(n + 1)]
    for i in range(n + 1):
        if i > 0:
            value = items[i - 1].value
            weight = items[i - 1].weight
        for j in range(cap + 1):
            if i == 0 or j == 0:
                continue
            elif weight > j:
                values[i][j] = values[i - 1][j]
            else:
                vTake = values[i - 1][j - weight] + value
                vKeep = values[i - 1][j]
                values[i][j] = max(vTake, vKeep)

    totalWeight = cap
    for i in reversed(range(n)):
        if values[i][totalWeight] == values[i + 1][totalWeight]:
            continue
        else:
            taken[i] = 1
            totalWeight -= items[i].weight

    return values[-1][-1], 1, taken
```


## 整数线性规划 （Integer Linear Programming, ILP）
背包问题的设定非常适合转换成 ILP 的标准形式之后用软件包直接求解。在上课的过程中，我发现了 [Gurobi][10] 这个数值优化神器，性能极高并且可以让在校生免费使用，并且具有非常简单易用的 `python` 接口。接下来我们只要根据输入数据对问题进行转换即可。

令 $x\_i$ 为优化变量， $x\_i=1$ 表示选择第 $i$ 个物品， $x\_i=0$ 表示不选择第 $i$ 个物品，可以列出优化问题标准形式如下：
$$\begin{align\*} 
\max & \sum x\_i v\_i \\\\
 s.t. & \sum w\_i\leq K\\\\
& x\_i\in\{0,1\}
\end{align\*}$$

解题时，将问题数据转换成 Gurobi 所需的格式即可直接求解：

```python
def mip(cap, items, verbose=False, num_threads=None):
    item_count = len(items)
    values = [item.value for item in items]
    weights = [item.weight for item in items]

    m = Model("knapsack")
    m.setParam('OutputFlag', verbose)
    if num_threads:
        m.setParam("Threads", num_threads)
    else:
        m.setParam("Threads", cpu_count())

    x = m.addVars(item_count, vtype=GRB.BINARY, name="items")
    m.setObjective(LinExpr(values, [x[i] for i in range(item_count)]), GRB.MAXIMIZE)
    m.addConstr(LinExpr(weights, [x[i] for i in range(item_count)]), GRB.LESS_EQUAL, cap, name="capacity")

    m.update()
    m.optimize()

    if m.status == 2:
        opt = 1
    else:
        opt = 0
    return int(m.objVal), opt, [int(var.x) for var in m.getVars()]
```

## TODO
* 增加贪心法性能边界的讨论
* 分析常见的 ILP 解法思路

[1]:	https://en.wikipedia.org/wiki/Andrew_Ng "Andrew Ng"
[2]:	https://en.wikipedia.org/wiki/Daphne_Koller "Daphne Koller"
[3]:	https://www.coursera.org/ "Coursera"
[4]:	https://www.coursera.org/learn/discrete-optimization "Discrete Optimization"
[5]:	https://www.coursera.org/account/accomplishments/certificate/L6ANQK3YG8C9 "Course Certificate"
[6]:	https://github.com/jixinfeng/discopt-soln "Assignment Code"
[7]:	https://en.wikipedia.org/wiki/NP-completeness "NP-Complete"
[8]:	https://en.wikipedia.org/wiki/Greedy_algorithm "Greedy Algorithm"
[9]:	https://en.wikipedia.org/wiki/Dynamic_programming "Dynamic Programming"
[10]:	http://www.gurobi.com "Gurobi"
