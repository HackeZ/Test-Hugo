+++
Categories = ["Development", "GoLang"]
Description = ""
Tags = ["Qiniu", "golang"]
date = "2016-04-15T16:46:54+08:00"
menu = ""
title = "Qiniu Go SDK v7 Problem"

+++

# 
下载七牛Go SDK v7遇到的问题

### 
今天想要使用七牛的Go SDK时候遇到了肯定会出现的一个情况，那就是

> $ go get -u qiniupkg.com/api.v7

### 
命令出现了

> golang.org/x/net/context 

### 
不能下载的问题，首先说一下为什么肯定会报错的问题，那就是Go官方将这个包的下载地址更改了（后来翻墙到Go官网发现的），但是不知道为什么go get命令还是将这个包的下载地址设置为原来的那个。
好吧，于是我试着使用七牛提供的方法下载了那个压缩包，并将其解压在

> $GOPATH/src

### 
目录下，再次运行go get 命令，这时候出现了

> golang.org/x/net/content is not using a known version

### 
的错误。
无奈之下，只有翻墙出去Go官方网站查看文档了，然后发现Go官方将这个[net包](https://github.com/golang/net)放在了Github中，于是我在Github中下载下来这个包，然后在

> $GOPATH/src

### 
下，也就是Github.com文件夹的平级目录下手动创建了该路径

> .../golang.org/x

### 
，然后将net包放进去，再次运行

> $ go get -u qiniupkg.com/api.v7

### 
OK，成功了，接下来就可以开始愉快地玩耍了:)




