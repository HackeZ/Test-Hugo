+++
Categories = ["Development", "GoLang", "Docker"]
Description = ""
Tags = ["Development", "golang", "docker"]
date = "2016-08-31T19:17:28+08:00"
menu = ""
title = "Fun in Docker Day-2"

+++

## Intro
今天来详细讲一下之前没有讲清楚的 Docker 镜像打包方法。

## Page Command
我们都知道，打包 Docker 应用有两种方式：

1. 在已经存在的 images 中 commit 修改。
2. 创建一个全新的 images 。

这两种方法各有优缺点。下面都来说一下怎么进行操作。

## Way One
在已有的 images 中修改并 commit

- 优点：
    + 这是最方便快捷的方法
    + 可以避免自己打包而导致出现的一些问题，如静态文件引用错误等
- 缺点：
    + 可定制程度低
    + 打包出来的镜像文件可能会很大，不利于存储

在 Docker 官网的文档中已经很详细操作过了，下面的是我翻译的版本。具体的官方英文[点我](https://docs.docker.com/engine/tutorials/dockerimages/#/updating-and-committing-an-image)

首先你需要一个 images 才能进行更新操作呀，所以首先：

```shell
#   获取 images
$ docker pull training/sinatra
#   运行 images 并进入到命令行中
$ docker run -t -i training/sinatra /bin/bash
root@0b2616b0e5a8:/#
```

> 记住这个被创建容器的 ID， `0b2616b0e5a8` ，一会你会用得上的。

接上面的操作...

```shell
#   首先更新一下 Ruby
root@0b2616b0e5a8:/# apt-get install -y ruby2.0-dev
#   然后安装  gem  json
root@0b2616b0e5a8:/# gem2.0 install json
```

完成了这些更改之后，你可以运行 `exit` 命令退出。

现在你可以像使用 `git` 一样更新这个镜像了：

```shell
#   '-m' '-a' 这些看起来很熟悉啦，和 git 中是一样的，就不再说了...
$ docker commit -m "Added json gem" -a "Kate Smith" \
0b2616b0e5a8 ouruser/sinatra:v2

4f177bd27a9ff0f6dc2a830403925b5360bfe0b93d476f7fc3231110e7f71b1c
```

然后运行一下 `docker images` 来看看新创建的容器吧

```shell
$ docker images

REPOSITORY          TAG     IMAGE ID       CREATED       SIZE
training/sinatra    latest  5bc342fa0b91   10 hours ago  446.7 MB
ouruser/sinatra     v2      3c59e02ddd1a   10 hours ago  446.7 MB
ouruser/sinatra     latest  5db5f8471261   10 hours ago  446.7 MB
```

## Way Two

完全创建一个新的 images 是很多人第一时间就想做的，但是官网简介中并没有太多详细标注的细节，那么我就以我的第一视角讲诉一下我是怎么创建一个全新的  images 的。

依然是 Go ，首先创建一个简单的读取文件内容的项目：

```shell
$ cd $GOPATH/src
$ mkdir File_Reader
$ vim File_Reader/main.go
```

源代码如下：

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"log"
	"os"
)

func main() {
	file, err := os.OpenFile("./file.txt", os.O_RDONLY, os.ModePerm)
	defer file.Close()

	if err != nil {
		log.Println("file.txt open false!")
	}

	fileReader := bufio.NewReader(file)

	for {
		line, err := fileReader.ReadString('\n')
		if err != nil {
			if err == io.EOF {
				break
			}
			log.Println("Read File Content Failed!", err.Error())
			return
		}
		fmt.Println(line)
	}

	fmt.Println("File Read Done!")
}
```

接下来就是在当前目录下创建一个 `file.txt` ，然后输入你想要的内容进去。

```shell
$ "xxxxxx" >> file.txt
```

接下来就需要先将源代码文件编译成二进制文件，因为如果我们希望这个 images 越小，越少的编译环境可以达到更好的效果。
命令如之前一样：

```shell
$ CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .
```

然后你可以看到当前目录下出现了一个 `main` 的可执行文件。

重要的步骤来了，当前目录下创建一个 `Dockerfile` 文件，输入如下内容：

```
FROM scratch
MAINTAINER HackerZ
ADD main /
ADD file.txt /
CMD ["/main"]
```

我来解释一下这些都是什么意思：

- FROM : Docker 用来指定该镜像是基于哪个基础镜像构建的
- MAINTAINER : 镜像创建人的名字
- ADD  : 从 Dockerfile 所在目录拷贝文件到指定路径下
- CMD  : 用来指示当运行 `docker run` 命令运行该镜像时要执行的命令

其余的还有：

- EXPOSE : 开放的网络端口号
- ENV    : 设置环境变量
- VOLUME : 可以将本地文件夹或者其他容器的文件夹挂载到该容器中。
- WORKDIR: 切换目录用，可以多次切换(相当于cd命令)，对RUN,CMD,ENTRYPOINT生效
- ONBUILD: ONBUILD 指定的命令在构建镜像时并不执行，而是在它的子镜像中执行

好了，运行 `docker build -t fileReader .` 创建全新的 images 吧。