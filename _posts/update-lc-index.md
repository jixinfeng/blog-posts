---
title: （半）自动更新 LeetCode 解题进度
date: 2018-05-01 17:30:53
tags:
- regex
- map
- filter
- markdown
---
我在博客的关于页面放了一个 LeetCode 的进度表，分别标记了每道题是否已经写出代码，以及是否写了解析。手动更新这些内容非常费时费力，同时也容易出错，因此今天试着写了一小段 Python 程序以自动化更新解题进度，主要用到了简单的正则表达式匹配和 python 自带的 `filter` 和 `map` 命令。

```python
import re
from os import listdir

src_list = listdir('SRC_DIR')
src_list = filter(lambda filename: re.match('\d\d\d', filename), src_list)
src_nums = set(map(lambda filename: int(filename.split('_')[0]), src_list))

post_list = listdir('POST_DIR')
post_list = filter(lambda x: x[:8] == "Leetcode", post_list)
post_nums = set(map(lambda filename: int(filename.split('-')[1]), post_list))

lines = []
with open("index.md", "r") as index:
    for line in index:
        if re.match('\|(\d)+\|', line):
            items = line.split('|')
            idx = int(items[1])
            if idx in src_nums:
                items[4] = u"\U00002713"
            if idx in post_nums:
                items[5] = u"\U00002713"
            lines.append('|'.join(items))
        else:
            lines.append(line)

for line in lines:
    print(line, end='')

```

我的 LeetCode 代码文件名格式为：`###_name.py` ， Hexo 上解析文章的文件名为： 'Leetcode-###-name.md' 。因此我才用的方式为：遍历两个文件夹，获得已经保存的代码和文章对应的题号，保存成 set 。然后再逐行遍历 `index.md` ，遇到表格第一列是数字的行，就取得题号并判断该题号在 `src_nums` 及 `post_nums` 中是否存在。判断完成后将对应的表格列改成 `u"\U00002713"` 即 ✓ 。

之所以称之为半自动，是因为 `index.md` 中的题目列表还只能手动添加，目前只添加了前700题，希望以后能有办法把这一步也自动化。