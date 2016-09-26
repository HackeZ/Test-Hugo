+++
Categories = ["Development", "GoLang"]
Description = ""
Tags = ["hugo", "github-pages"]
date = "2016-04-13T16:55:34+08:00"
menu = ""
title = "本篇将教会你如何使用Hugo快捷地在Github中创建自己的Blog"

+++


## 安装Hugo
---
###### 
Hugo是一个使用Golang语言编写的静态Web站点生成框架，其是由Docker前员工Steve Francia进行编写的，因为其开源在Github里，所以安装非常方便，我们可以选择二进制安装包进行安装。安装完成之后可以运行以下命令查看是否正确安装：

>$ hugo version

###### 
正确安装应该会出现如下信息：

>Hugo Static Site Generator v0.14 BuildDate: 2015-05-26T09:29:16+08:00

###### 
接下来就可以愉快地开始Hugo之旅了。


## 创建Hugo项目
---
###### 
创建Hugo项目可以使用如下命令

>$ hugo new site &lt;site-name>

###### 
这样在该目录下就会出现这个项目文件夹了。
我们 cd 进入该目录，可以看到该目录下有一个名为：

>config.toml

###### 
的文件，根据其名字很容易就知道这个就是Hugo的站点的配置文件了。
该文件中仅仅只有3行代码：

>baseurl = "http://replace-this-with-your-hugo-site.com/"
>languageCode = "en-us"    
>title = "My New Hugo Site"

###### 
同样也是根据其单词我们也可以知道他们代表的是什么，我们可以对title进行一下修改，改为我们Blog的名字。

然后我们在该目录下运行命令：

>$ hugo server

###### 
这个命令会将repo转换成静态html文件放入项目的public文件夹下，然后通过访问浏览器的

>http://localhost:1313

###### 
地址，即可看到Hugo启动起来了。虽然现在站点是空白一片，但是通过添加Hugo主题，我们可以瞬间建立一个完整Blog站点。



## 选取Hugo主题
---
###### 
我们可以到Hugo官网选取自己喜欢的[主题](http://themes.gohugo.io/)
下面我以Hyde主题为例，将该主题应用到自己的Blog中。

首先在站点的根目录下创建一个 themes 文件夹。

>$ mkdir themes    
>$ cd themes    
>$ git clone https://github.com/spf13/hyde.git  #下载对应主题

###### 
然后，我们需要对根目录下的

>config.toml

###### 
进行配置，以应用下载下来的主题。

配置完成的文件如下所示：

``` toml
baseurl = "http://replace-this-with-your-hugo-site.com/"
languageCode = "en-us"    
title = "HackerZ - Blog"    
theme = "hyde"   # 指定themes

[params]    
	description = "Welcome to my personal Blog"   # hyde主题的首页描述 
	themeColor = "theme-base-08"  # 指定hyde的主题颜色
```   

###### 
这样，主题就算是配置好了，让我们再次运行

>$ hugo server

###### 
看看效果吧！

## 新建文章
---
###### 
首页以及样式都已经有了，那么下面就来看看怎么新建一篇文章吧。

在站点项目下运行

>$ hugo new welcome.md

###### 
即可看到在项目的content目录下被创建了一个 welcome.md 文件，该文件就是刚才新建出来的文章了。
我们可以往里面写点东西，注意，这是 markdown 格式的，hugo会将其编译成 html 格式放置在 public 目录下。

>welcome.md
```
+++    
Categories = ["Development", "GoLang"]    
Description = ""    
Tags = ["Development", "golang"]    
date = "2016-03-29T14:38:19+08:00"    
menu = "main"    
title = "Welcome"    

+++

### 
这是使用Hugo创建的站点中的第一篇文章。
```
###### 
然后启动Hugo服务查看效果吧。

>$ hugo server


###### 
到这里，基本的Hugo使用已经讲解完毕了，接下来就要将该静态站点迁移到我们自己的 github.io 中了。


## 使用Github Pages

###### 
要使用 github-pages 首先需要注册属于自己的 github 账号，注册完成之后，创建一个 repository，名为

>&lt;USERNAME>.github.io

###### 
这个是使用 Github Pages 的命名规定，如我自己就是

>HackeZ.github.io

###### 
创建完成之后，在我们已经写好的Hugo站点下修改配置文件中的 baseurl属性：

> config.toml
``` 
baseurl = "http://&lt;USERNAME>.github.io//"
# baseurl = "http://replace-this-with-your-hugo-site.com/"
# 原baseurl可以将其注释掉，之后本地可以进行调试
```
###### 
保存后，运行如下命令：

>$ hugo -v

###### 
该命令会将配置文件中的参数进行静态文件编译，运行完之后，可以到public下的 index.html 中看看静态文件的地址是否是有误，如果有误，将不能正确地显示出主题样式。

一切正确之后，我们以public目录为 &lt;USERNAME>.github.io 项目的主分支，将其 push 到 github仓库中，等待10分钟左右，访问属于你自己的Blog网站吧！

>http://&lt;USERNAME>.github.io


## 配置属于自己的Hugo主题

###### 
你们可以看到，我已经对原来的Hyde主题进行了修改了，那么是怎么做到的呢，我们可以直接对 themes/hyde 下的配置文件进行修改，增加自己想要的样式，相信聪明的你肯定可以很快熟悉 Hugo 的语法，创建属于自己的主题。