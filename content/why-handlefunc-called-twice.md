+++
Categories = ["Development", "GoLang"]
Description = ""
Tags = ["Development", "golang"]
date = "2016-07-24T20:03:59+08:00"
menu = ""
title = "Why HandleFunc Called Twice"

+++

## Why **HandleFunc()** called twice?


### Abstract 

今天遇到之前碰见过的一个问题，但是之前忘记研究了，正好今天终于把这个问题弄清楚了，于是记录下来。

想必很多做后台的小伙伴都写过服务器了，但是有没有遇到服务器在 **通过不同的（Brower、API）方式访问** 的时候，服务器响应方法的 **次数** 是不一样的情况呢？

### Problem

先来看看Golang中的简易服务器搭建代码：

```go
func SayHello(rw http.ResponseWriter, req *http.Request) {
    io.WriteString(rw, "hello~ You are in!")
    log.Println("Oh, Here is a Guy coming in!")
}

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", SayHello)
    http.ListenAndServe(":8080", mux)
}
```

这是一个最简单的Golang服务器搭建，当http访问 `http://localhost:8080` 的时候，该服务器会对客户端返回 `hello~ You are in!` ，同时在服务器控制台中打印 `Oh, Here is a Guy coming in!` 。

有意思的部分来了：

> 通过 **Brower** 访问的时候，服务器控制台会打印出 **两行** `Oh, Here is a Guy coming in!`。

> 通过 `curl http://localhost:8080` 命令进行访问的时候，服务器控制台只会打印 **一行** `Oh, Here is a Guy coming in!`。

## Why

为什么会出现那么有趣的问题呢？[StackOver](http://stackoverflow.com/questions/33432192/handlefunc-being-called-twice)上也有人问了这个问题，原因出现在 Brower 上，通过打印 `requsets`，你会发现 Brower 还会发起二次请求去请求 `/favicon.ico`，也就是页面的小图标。

所以这就是用 `CURL` 发起请求的时候，并不会出现二次请求的原因！

## Solve

既然知道了这个问题出现在哪，剩下就好办了，既然浏览器要请求图标，那么我们就在写一个路由专门处理这个请求即可：

```go
func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", SayHello)
    // Handle /favicon.ico
    mux.HandleFunc("/favicon.ico", func(rw http.ResponseWriter, req *http.Request) {})
    http.ListenAndServe(":9000", mux)
}
```

