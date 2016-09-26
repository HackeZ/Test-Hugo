+++
Categories = ["Development", "GoLang"]
Description = ""
Tags = ["Development", "golang"]
date = "2016-08-07T11:11:25+08:00"
menu = ""
title = "Golang Package Dependency Management Tool"

+++

# Golang Package Dependency Management Tool

## Intro
Golang一直以来被外界诟病的一个问题就是包的依赖管理问题。那么今天就来讲一个：

> Golang包依赖管理工具 —— gb

gb 在其官网中定义自己为：

> A project based build tool for the Go programming language.

一个Golang的项目工程通常由 `bin`、`pkg`、`src`三个子目录构成：

+ bin : 存放编译后生成的可执行文件
+ pkg : 编译后生成的文件（如：.a）
+ src : 存放源代码（如：.go .c .h .s等）

而 `gb` 在这个概念的基础上新增了一个 `vendor` 目录来存放项目依赖的第三方包（如 *beego* ，*gracehttp* 等）

## gb action

#### Install

gb ==> [首页](https://getgb.io/)  ==>  [Github](https://github.com/constabulary/gb/)

根据说明，使用

```shell
$ go get github.com/constabulary/gb/...
```

命令即可安装 gb。

当该命令运行完毕，请检查 `env` 下的第一个 `$GOPATH` 的 `bin` 目录下是否生成了 `gb` 以及 `gb-vendor` 两个可执行文件。

> 如安装报错，请检查你是否正确配置了 $GOPATH 等环境变量。

#### Use

下面试着使用 `gb` 来构建一个基于第三方包 `gracehttp` 的简易 Golang Web 项目，来体验一下 `gb` 的魅力。

首先初始化 `hellogb` 项目目录结构：

```shell
$ cd $GOPATH/src/hellogb
$ mkdir -p src/hellogb
$ mkdir -p vendor/src
```

编写 Web 程序：

```go
// vim src/hellogb/main.go
package main

import (
    "fmt"
    "net/http"

    "github.com/tabalt/gracehttp"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "hello gb")
    })

    err := gracehttp.ListenAndServe(":8080", nil)
    if err != nil {
        fmt.Println(err)
    }
}
```

使用 `gb` 添加依赖的 `gracehttp` 第三方包：

```shell
$ gb vendor fetch github.com/tabalt/gracehttp
```

最终整个项目目录结构为：

```
./
|-- src
|   `-- hellogb
|       `-- main.go
`-- vendor
    |-- manifest
    `-- src
        `-- github.com
            `-- tabalt
                `-- gracehttp
                    |-- README.md
                    |-- connection.go
                    |-- gracehttpdemo
                    |   `-- main.go
                    |-- listener.go
                    `-- server.go
```

编译执行程序：

```shell
$ gb build hellogb
$ ./bin/hellogb
```

最后访问 `http://127.0.0.1:8080/` 即可访问 Web 服务。

#### Command

##### gb Command
| Command     | 功能   |
|:------------| :-----:|
|build        |编译包|
|vendor       |调用 gb-vendor |
|doc	      |显示文档|
|env	      |打印项目的环境变量|
|generate     |处理源代码生成Go文件|
|info	      |显示项目的信息|
|list	      |显示项目下的所有包|
|test  	      |执行测试|
 
---
##### gb vendor Parameter
| Parameter   | 功能   |
|:------------| :-----:|
|fetch	      |获取一个远程依赖|
|update	      |更新一个本地依赖|
|list	      |每行一个列出所有依赖|
|delete	      |删除一个本地依赖|
|purge	      |清除所有未引用的依赖|
|restore	  |从manifest清单文件还原依赖|

> 本文参考 tabalt 的 [Golang包依赖管理工具gb](http://tabalt.net/blog/golang-package-dependency-management-tool-gb/) 一文。

