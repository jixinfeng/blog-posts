---
title: 使用Hexo架设博客步骤
date: 2017-09-09 10:29:57
tags: 
- github 
- hexo 
- node.js 
- tutorial

mathjax: true
---

## 配置环境

### Mac 用户需要先安装homebrew

详细步骤可以在[Homebrew 官网 ][1]找到

``` bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### 安装 `npm`，`git` 和 `hexo`

首先通过 `homebrew` 安装 `npm` 和 `git`

``` bash
$ brew update
$ brew install npm npv git
```

``` bash
$ npm install -g hexo-cli
```

## 创建博客

### 在 github 上创建博客 repo

`https://github.com/{github_username}/{github_username}.github.io`

注意 hexo 在部署博客的时候只会上传生成的网页，原始的 md 文件以及所在的 source 文件夹并不会被添加到 repo 中，如果希望 md 文件也能被 github 追踪，可以考虑将 source 文件夹单独创建一个独立的 repo
`https://github.com/{github_username}/{blog-posts}`
并以 submodule 的形式添加到博客 repo 中

``` bash
$ cd {repo-dir}
$ git submodule add {source-repo-url} source
```

### 创建新的 Hexo 博客

``` bash
$ git clone {repo-address}
$ hexo init tmp
$ mv ./tmp/* {repo-dir}
$ mv ./tmp/.gitignore {repo-dir}
```

### 添加自己喜欢的主题

``` bash
$ cd {repo-dir}
$ git submodule add {theme-repo-url} theme/{theme-dir}
```

``` bash
$ cd {repo-dir}
$ vim _config.yml
```

``` bash
language: zh-Han
timezone: America/New_York
```

``` bash
deploy:
type: git
repo: {blog-repo-url}
branch: master
message: "Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}"
```

## 访问博客

### 本地测试

``` bash
$ hexo generate
$ hexo server
```

### 部署到 github
``` bash
$ npm i -S hexo-deployer-git
$ hexo deploy
```

## 编辑内容
### 新建文章
``` bash
$ hexo new {title}
```

### 编辑新文章
``` bash
$ vim source/_posts/{title}.md
```

### 更新博客并重新部署
``` bash
$ hexo generate -d
```

### 添加基于 $\LaTeX$ 的数学公式
在 Hexo 中添加 LaTeX 公式很容易，在支持 MathJax 的主题，如这个博客采用的 [next][2] 中，只需在 `_config.yml` 中开启 MathJax 支持即可。

``` bash
math:
    per_page: true
    
    mathjax:
        enable: true
```

开启 `per_page` 之后，需要在文章开头处手动启用 `mathjax`

``` bash
---
title: title.html
date: 2017-01-01 12:01:30
tags:
mathjax: true
---
```

## 参考：

* [Hexo Documentations][3]
* [How to setup a blog on github with Hexo][4]
* [Using Git Submodules to Manage Your Custom Hexo Theme][5]
* [使用 submodule 管理 Hexo 主题][6]
* [Hexo 书写 LaTeX 公式时的一些问题及解决方法][7]

[1]:	https://brew.sh
[2]:	https://github.com/iissnan/hexo-theme-next "hexo-theme-next"
[3]:	https://hexo.io/docs/ "Hexo Documentations"
[4]:	https://zirho.github.io/2016/06/04/hexo/ "How to setup a blog on github with Hexo"
[5]:	http://jr0cket.co.uk/hexo/using-git-submodules-for-custom-hexo-theme.html "Using Git Submodules to Manage Your Custom Hexo Theme"
[6]:	http://www.swiftyper.com/2017/07/25/managing-hexo-theme-using-submodule/ "使用 submodule 管理 Hexo 主题"
[7]:	https://jdhao.github.io/2017/10/06/hexo-markdown-latex-equation/ "Hexo 书写 LaTeX 公式时的一些问题及解决方法"
