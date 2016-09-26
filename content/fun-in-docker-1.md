+++
Categories = ["Development", "GoLang", "Docker"]
Description = ""
Tags = ["Development", "golang", "docker"]
date = "2016-08-20T16:17:42+08:00"
menu = ""
title = "Fun in Docker Day-1"

+++

## Intro
今天心血来潮，想在 OSX 中重新体验一下 `Docker`，结果因为 `Docker` 是基于 `Linux`，在 OSX 中实在是 Fun 不起来，于是便纪录下来这天的过程。


## Install

这部分没什么好说的， `Docker` 官方已经出了 [OSX](https://docs.docker.com/docker-for-mac/) 的安装包，直接下载拖进 `Application/` 即可完成安装。

安装完成之后，可以直接在命令行中运行：

```shell
$ docker --version  # Docker 主体
$ docker-compose --version  # 定义和管理复杂 Docker 应用的工具
$ docker-machine --version  # 简化 Docker 安装的工具
```
查看所有的安装工具是否能够正确启动。

之后，便可以尝试运行 `Hello World` 和 `nginx` 玩一下了。

```shell
# Hello World
$ docker run hello-world

# nginx
$ docker run -d -p 80:80 --name webserver nginx
```

## Package

以我的开源项目 [getMeizi](https://www.github.com/HackeZ/getMeizi) 为例子尝试打包 `Golang应用` 。

#### First Try

首先我在项目的根目录下编写了一个 `Dockerfile` 文件，其内容为：

```
FROM golang:onbuild
```

然后通过 `$ docker build -t getmeizi .` 来构建一个镜像。

但是这样构建的镜像会将 `Golang` 的整个环境都打包进去，生成的镜像大小为 `832.5 MB` 。

很显然我们会更加愿意得到一个更灵活小巧的镜像，于是，我的目光转向了 `scratch` 。

#### Second Try

修改 `Dockerfile` 文件内容为：

```
FROM scratch
ADD main /
CMD ["/main"]
```

然后先将 `getMeizi` 应用编译完：

```
$ CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .
```

最后使用 `$ docker build -t getmeizi .`

构建即可生成一个仅有 `5.83 MB` 大小的镜像。

## Push

最后将打包好的镜像发布到 **Docker.io** 中：

```shell
$ docker images
# REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
# getmeizi            latest              7ddcbed63a17        2 minutes ago         5.839 MB

$ docker tag 7ddcbed63a17 hackerz/getmeizi

$ docker images
# REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
# getmeizi            latest              7ddcbed63a17        3 minutes ago         5.839 MB
# hackerz/getmeizi    latest              7ddcbed63a17        3 minutes ago         5.839 MB

$ docker login
# ***
# Login Succeeded

$ docker push hackerz/getmeizi
```

打开 [hackerz/getmeizi](https://hub.docker.com/r/hackerz/getmeizi/) 查看并编辑镜像描述。
