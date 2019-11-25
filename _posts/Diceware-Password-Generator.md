---
title: 利用 Diceware 生成好记的密码
date: 2017-12-07 14:17:23
tags:
- diceware
- xkcd


mathjax: true
---
![XKCD comic about password strength][image-1]
[Diceware][1]是一种生成便于记忆并难以通过暴力穷举密码的方式。很长时间以来，网站都在建议人们使用更加复杂的密码，如：
* 混合使用大小写字母和数字
* 使用标点符号
* 避免出现常见单词的拼写组合
* 在不同的网站使用不同的密码以避免被撞库

几种常见的密码组合方式的组合数分别为：

|密码成分|8位组合数|12位组合数|16位组合数|
|:---:|:---:|:---:|:---:|
|a-z| $10^\{11.3\}$ | $10^\{17.0\}$ | $10^\{22.6\}$ |
|a-z0-9| $10^\{12.5\}$ | $10^\{18.7\}$ | $10^\{24.9\}$ |
|a-zA-Z| $10^\{13.7\}$ | $10^\{20.6\}$ | $10^\{27.5\}$ |
|a-zA-Z0-9| $10^\{14.7\}$ | $10^\{22.0\}$ | $10^\{29.3\}$ |
| a-zA-Z0-9&| $10^\{15.8\}$ | $10^\{23.7\}$ | $10^\{31.6\}$ |

这样的确可以生成复杂度极高的密码，但是这样的密码对用户非常不友好，难以记忆，所以事实上真正这样做的人并不多。毕竟，没有多少人真得能牢记形如 `z28>+J]pFF22c+tj` 、 `?WtdCW(a9j9u2+jw` 、 `@^xtE9am#(eFC9H8` 、 `T3sY]7]tnRc28U>}` 、 `i949re{NCjje%/*9` 的密码，并且能分清分别对应哪个网站、哪个账户。

用 Diceware 生成密码可以在一定程度上解决这个问题。 Diceware 的原理是：对字典中的每一个单词用长度一定（如5位）的6进制数字编号，生成密码时，掷一枚均匀的六面骰子5次，得到一个5位的6进制数字，根据编号找到并记录这个单词。重复这样的操作3到5次，即可得到长度为3至5个单词的diceware密码。可以针对这几个单词编一个故事便于记忆。

电子前线基金会（EFF）在网站上给出了一个已经完成编号的[单词列表][2]，其中包含了 $6^5=7776$ 个单词，另外，在 github 上还可以找到包含近50万单词的[词汇列表][3]。根据 EFF 的词典生成的随机密码可以具有相当高的复杂度：
* 随机选取3个单词一共有 $(6^5)^3=10^\{11.7\}$ 种不同的组合
* 随机选取4个单词一共有 $(6^5)^4=10^\{15.6\}$ 种不同的组合
* 随机选取5个单词一共有 $(6^5)^5=10^\{19.5\}$ 种不同的组合

相对于记住十几位毫无规律的随机字符串，通过编故事记住几个形如 `aloft-applied-embody` 、 `mug-gown-getaway` 、 `idiom-eggnog-sadden` 的单词组合应该会容易的多。

利用 Python ，写一个生成 Diceware 密码的程序非常简单：

```python
import collections
import random

diceware = collections.defaultdict()

with open('eff_large_wordlist.txt') as fp:
    for line in fp:
        words = line.split()
        diceware[words[0]] = words[1]

length = 3
sep = '-'

words = []
for j in range(length):
    index = "".join([str(random.randint(1,6)) for i in range(5)])
    words.append(diceware[index])
print(sep.join(words))
```

生成密码的部分还可以分别一行写完
```python
print(sep.join([diceware["".join([str(random.randint(1,6)) for i in range(5)])] for j in range(length)]))
```

另外在实际应用中，要使用[密码学安全伪随机数生成器（CSPRNG）][4]

[1]:	https://en.wikipedia.org/wiki/Diceware "Diceware"
[2]:	https://www.eff.org/files/2016/07/18/eff_large_wordlist.txt "EFF Diceware Word List"
[3]:	https://github.com/dwyl/english-words "479k English words"
[4]:	https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator "Cryptographically secure pseudorandom number generator"

[image-1]:	https://imgs.xkcd.com/comics/password_strength.png "XKCD #936 Password Strength"
