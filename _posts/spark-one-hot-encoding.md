---
title: Spark 微操作之 One-Hot 编码
date: 2020-01-26 15:47:09
tags:
- spark
- machine learning
- one hot encoding
- feature engineering

mathjax: true
---

## 为什么要 One-Hot 编码 （ OHE ）

在机器学习中 One-Hot 编码 （One-Hot Encoding， OHE） 是一种经常用到的特征编码方式。对一个有 $n$ 个不同可能取值的特征，可以将其编码为一个长度为 $n$ 的向量。对于第 $i$ 个取值，令编码向量第 $i$ 位取 1 ，其余位取 0 。虽然一眼望去这样和直接将特征编码为 0 至 $n$ 的顺序编码没有什么区别，但是顺序编码在处理没有顺序的特征（国家、语言、物品分类等）时会产生特征取值之间存在顺序的副作用：比如编码为 1 和 2 的特征之间的关系会比编码为 1 和 6 的特征更近。

## PySpark 和 Scikit-Learn 中 OHE 的区别

在 Scikit-Learn 和 PySpark 中， OHE 的细节存在一些区别，如果已经习惯了 Scikit-Learn 的输出格式，在用 PySpark 时会产生预料之外的输出。在 Scikit-Learn 的 [OneHotEncoder()][1] 中，每一个独立的特征会在输出中单独表达为 DataFrame 的一列。但是在 PySpark 的 [OneHotEncoderEstimator][2] 中，编码输出的则是稀疏向量 [SparseVector][3] ， 并且对于有 $n$ 个值的特征，默认输出的向量维度只有 $n-1$ ，并且最后一个特征会被编码为全零的向量。

另外， PySpark 的 OneHotEncoderEstimator 对非数值特征列进行编码前，需要先通过 [StringIndexer][4] 进行顺序编码。

## 举个🌰

用一个随机生成的数据为例，这个训练数据保存了几个员工的姓名、部门、职位、工作年数、工资以及会说的语言。我们希望对部门和岗位进行 OHE 。

```
+---+-------+----------+--------+-----+------+---------------------------+
|id |name   |department|title   |years|salary|language                   |
+---+-------+----------+--------+-----+------+---------------------------+
|0  |Arche  |IT        |Engineer|2    |65000 |[English, Spanish]         |
|1  |Beth   |Purchase  |Manager |5    |100000|[English, French, Spanish] |
|2  |Carl   |Marketing |Intern  |1    |60000 |[English, Chinese]         |
|3  |Dorian |Marketing |Manager |8    |125000|[English, French, Japanese]|
|4  |Escher |IT        |Manager |6    |10000 |[English]                  |
|5  |Faridah|IT        |Engineer|7    |80000 |[Chinese, Japanese]        |
|6  |Gustav |Marketing |Engineer|2    |75000 |[Spanish, Russian]         |
+---+-------+----------+--------+-----+------+---------------------------+
```

同时，还有一个内容稍有不同的测试数据用于测试编码器在面对新数据和空数据时的行为。

```
+---+-------+----------+----------+-----+------+---------------------------------+
|id |name   |department|title     |years|salary|language                         |
+---+-------+----------+----------+-----+------+---------------------------------+
|0  |Arche  |IT        |Engineer  |4    |70000 |[English, Spanish, Korean]       |
|1  |Beth   |Purchase  |Manager   |6    |110000|[English, French, Spanish, Hindi]|
|2  |Carl   |Marketing |Intern    |2    |65000 |[English, Chinese, German]       |
|3  |Dorian |Marketing |Manager   |9    |130000|[English, French, Japanese]      |
|4  |Escher |IT        |Manager   |7    |50000 |[English, Thai]                  |
|5  |Faridah|IT        |null      |8    |90000 |[Chinese, Japanese]              |
|6  |Gustav |null      |Researcher|3    |80000 |[Ukrainian, Vietnamese]          |
+---+-------+----------+----------+-----+------+---------------------------------+
```

### 第一次尝试：PySpark 的默认行为

如果在初始化 `StringIndexer` 的时候采用默认参数，原始特征列为 `department` ， `StringIndexer` 输出为 `department_idx` ， `OneHotEncoderEstimator` 输出为 `department_vec` 。

```python
si_department = StringIndexer(
  inputCol="department",
  outputCol="department_idx",
#   handleInvalid="error"
)
si_title = StringIndexer(
  inputCol="title",
  outputCol="title_idx",
#   handleInvalid="error"
)
ohe = OneHotEncoderEstimator(
  inputCols=[si_department.getOutputCol(), si_title.getOutputCol()], 
  outputCols=["department_vec", "title_vec"]
)
```

输出的训练数据中，可以看到编码向量的长度都是 2 （比特征元素数少 1）并且最后一个元素被编码成了零向量，即空稀疏向量。同时，由于 `handleInvalid` 设置的是 `error` ，编码器在遇到包含未知/空数据的测试数据直接报错



```
+---+-------+----------+--------------+--------------+--------+---------+-------------+
|id |name   |department|department_idx|department_vec|title   |title_idx|title_vec    |
+---+-------+----------+--------------+--------------+--------+---------+-------------+
|0  |Arche  |IT        |0.0           |(2,[0],[1.0]) |Engineer|1.0      |(2,[1],[1.0])|
|1  |Beth   |Purchase  |2.0           |(2,[],[])     |Manager |0.0      |(2,[0],[1.0])|
|2  |Carl   |Marketing |1.0           |(2,[1],[1.0]) |Intern  |2.0      |(2,[],[])    |
|3  |Dorian |Marketing |1.0           |(2,[1],[1.0]) |Manager |0.0      |(2,[0],[1.0])|
|4  |Escher |IT        |0.0           |(2,[0],[1.0]) |Manager |0.0      |(2,[0],[1.0])|
|5  |Faridah|IT        |0.0           |(2,[0],[1.0]) |Engineer|1.0      |(2,[1],[1.0])|
|6  |Gustav |Marketing |1.0           |(2,[1],[1.0]) |Engineer|1.0      |(2,[1],[1.0])|
+---+-------+----------+--------------+--------------+--------+---------+-------------+
```

### 第二次尝试：保留未知数据

以部门特征为例，当初始化 `StringIndexer` 时设置 `handleInvalid="keep"` 时，一个单独的最高编码会保留给未知数据。即当某一特征有$n$ 个不同数值时， $0$ 至 $(n-1)$ 会保留给已知特征， $n$ 会保留给未知特征。当 `OneHotEncoderEstimator` 进行编码时，未知特征则被分配给了空向量，即表中的 `(3,[],[])`

```
+---+-------+----------+--------------+--------------+--------+---------+-------------+
|id |name   |department|department_idx|department_vec|title   |title_idx|title_vec    |
+---+-------+----------+--------------+--------------+--------+---------+-------------+
|0  |Arche  |IT        |0.0           |(3,[0],[1.0]) |Engineer|1.0      |(3,[1],[1.0])|
|1  |Beth   |Purchase  |2.0           |(3,[2],[1.0]) |Manager |0.0      |(3,[0],[1.0])|
|2  |Carl   |Marketing |1.0           |(3,[1],[1.0]) |Intern  |2.0      |(3,[2],[1.0])|
|3  |Dorian |Marketing |1.0           |(3,[1],[1.0]) |Manager |0.0      |(3,[0],[1.0])|
|4  |Escher |IT        |0.0           |(3,[0],[1.0]) |Manager |0.0      |(3,[0],[1.0])|
|5  |Faridah|IT        |0.0           |(3,[0],[1.0]) |Engineer|1.0      |(3,[1],[1.0])|
|6  |Gustav |Marketing |1.0           |(3,[1],[1.0]) |Engineer|1.0      |(3,[1],[1.0])|
+---+-------+----------+--------------+--------------+--------+---------+-------------+
```

```
+---+-------+----------+--------------+--------------+----------+---------+-------------+
|id |name   |department|department_idx|department_vec|title     |title_idx|title_vec    |
+---+-------+----------+--------------+--------------+----------+---------+-------------+
|0  |Arche  |IT        |0.0           |(3,[0],[1.0]) |Engineer  |1.0      |(3,[1],[1.0])|
|1  |Beth   |Purchase  |2.0           |(3,[2],[1.0]) |Manager   |0.0      |(3,[0],[1.0])|
|2  |Carl   |Marketing |1.0           |(3,[1],[1.0]) |Intern    |2.0      |(3,[2],[1.0])|
|3  |Dorian |Marketing |1.0           |(3,[1],[1.0]) |Manager   |0.0      |(3,[0],[1.0])|
|4  |Escher |IT        |0.0           |(3,[0],[1.0]) |Manager   |0.0      |(3,[0],[1.0])|
|5  |Faridah|IT        |0.0           |(3,[0],[1.0]) |null      |3.0      |(3,[],[])    |
|6  |Gustav |null      |3.0           |(3,[],[])     |Researcher|3.0      |(3,[],[])    |
+---+-------+----------+--------------+--------------+----------+---------+-------------+
```

### 第三次尝试：丢弃未知数据

当初始化 `StringIndexer` 时设置 `handleInvalid="skip"` 时，包含未知数据的行会被跳过，即从输出数据中丢弃。但由于这样一来，没有多余的位留给未知数据， `OneHotEncoderEstimator` 的输出还是会少一位。这时就需要令 `OneHotEncoderEstimator`的 `handleInvalid="keep"`。

可以看到预测数据的编码输出只剩下了 4 行。

```python
si_department = StringIndexer(
  inputCol="department",
  outputCol="department_idx",
  handleInvalid="skip"
)
si_title = StringIndexer(
  inputCol="title",
  outputCol="title_idx",
  handleInvalid="skip"
)
ohe = OneHotEncoderEstimator(
  inputCols=[si_department.getOutputCol(), si_title.getOutputCol()], 
  outputCols=["department_vec", "title_vec"],
  handleInvalid="keep"
)
```

```
+---+-------+----------+--------------+--------------+--------+---------+-------------+
|id |name   |department|department_idx|department_vec|title   |title_idx|title_vec    |
+---+-------+----------+--------------+--------------+--------+---------+-------------+
|0  |Arche  |IT        |0.0           |(3,[0],[1.0]) |Engineer|1.0      |(3,[1],[1.0])|
|1  |Beth   |Purchase  |2.0           |(3,[2],[1.0]) |Manager |0.0      |(3,[0],[1.0])|
|2  |Carl   |Marketing |1.0           |(3,[1],[1.0]) |Intern  |2.0      |(3,[2],[1.0])|
|3  |Dorian |Marketing |1.0           |(3,[1],[1.0]) |Manager |0.0      |(3,[0],[1.0])|
|4  |Escher |IT        |0.0           |(3,[0],[1.0]) |Manager |0.0      |(3,[0],[1.0])|
|5  |Faridah|IT        |0.0           |(3,[0],[1.0]) |Engineer|1.0      |(3,[1],[1.0])|
|6  |Gustav |Marketing |1.0           |(3,[1],[1.0]) |Engineer|1.0      |(3,[1],[1.0])|
+---+-------+----------+--------------+--------------+--------+---------+-------------+
```

```
+---+------+----------+--------------+--------------+--------+---------+-------------+
|id |name  |department|department_idx|department_vec|title   |title_idx|title_vec    |
+---+------+----------+--------------+--------------+--------+---------+-------------+
|0  |Arche |IT        |0.0           |(3,[0],[1.0]) |Engineer|1.0      |(3,[1],[1.0])|
|1  |Beth  |Purchase  |2.0           |(3,[2],[1.0]) |Manager |0.0      |(3,[0],[1.0])|
|2  |Carl  |Marketing |1.0           |(3,[1],[1.0]) |Intern  |2.0      |(3,[2],[1.0])|
|3  |Dorian|Marketing |1.0           |(3,[1],[1.0]) |Manager |0.0      |(3,[0],[1.0])|
|4  |Escher|IT        |0.0           |(3,[0],[1.0]) |Manager |0.0      |(3,[0],[1.0])|
+---+------+----------+--------------+--------------+--------+---------+-------------+
```

## 后续操作

进行 One-Hot 编码之后的特征可能还需要后续操作。比如 PySpark 的机器学习模型只支持一整个向量的输入，这就需要将编码输出和其他特征组合起来。有时候还需要输出数据具有和 Scikit-Learn 一样的格式，即每个向量的维度单独存为一列。这篇文章就暂时不涉及了。




[1]: https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html "OneHotEncoder"
[2]: https://spark.apache.org/docs/latest/ml-features#onehotencoderestimator "OneHotEncoderEstimator"
[3]: https://spark.apache.org/docs/latest/api/python/pyspark.ml.html?highlight=sparsevector#pyspark.ml.linalg.SparseVector "SparseVector"
[4]: https://spark.apache.org/docs/latest/api/python/pyspark.ml.html?highlight=stringindexer#pyspark.ml.feature.StringIndexer "StringIndexer"