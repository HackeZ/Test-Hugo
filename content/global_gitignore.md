+++
Categories = ["Development", "Git", "Linux"]
Description = ""
Tags = ["Development", "golang"]
date = "2016-08-18T16:03:05+08:00"
menu = ""
title = "Global Ignore File in Git"

+++

## Intro

自从换了 OSX 进行开发，就发现每当修改了项目文件， OSX 在项目目录都会生成一个 `.DS_Store` 的隐藏文件，该文件用于记录当前目录下文件的 Meta 信息。

对于这样的情况，我不可能在每个项目的根目录都配置一个 `.gitignore` 文件，这样可复用性太地了，于是我便想能不能配置一个 Git 的 `.gitignore_global` 文件，统一忽略掉所有我不需要上传的文件呢。

## Solve

Git 还真有这样的方法，它提供了一个 **忽略规则** ，我们可以通过编写一个忽略规则文件，然后通过如下的命令配置进 Git :

```shell
$ git config --global core.excludesfile '忽略文件完整路径'
```

即可。

## Global GitIgnore

新建一个 `.gitignore_global` 文件，并往里面编写忽略文件语法，该语法符合正则表达式， `#` 号为注释，每一行为一个忽略规则：

```shell
# OSX
.DS_Store
.DS_Store*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# Python
*.pyc

# C
*.[ao]

# Package
*.7z
*.dmg
*.gz
*.iso
*.jar
*.rar
*.tar
*.zip
```
