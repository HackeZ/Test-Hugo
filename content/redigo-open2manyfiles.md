+++
Categories = ["Development", "GoLang", "Redis"]
Description = ""
Tags = ["Development", "golang", "redis"]
date = "2016-07-27T13:29:11+08:00"
menu = ""
title = "redigo : open too many files"

+++

# Redigo - panic error : open too many files.

### Abstract 

今天对 [getAcFunPage](https://www.github.com/HackeZ/getAcFunPage) 项目做 Benchmark 的时候发现了 Redis 会频繁报一个 `socket: too many open files` 的错误，后来发现并不是代码的问题，而是 Linux 的设置问题。
下面就来说说我是这么解决这个问题的。

### Problem

Benchmark 时报错内容截取如下：
```
http: panic serving 127.0.0.1:53512: dial tcp :6379: socket: too many open files
goroutine 5322 [running]:
net/http.(*conn).serve.func1(0xc820f87f80)
    /usr/local/go/src/net/http/server.go:1389 +0xc1
panic(0x797240, 0xc820b12050)
    /usr/local/go/src/runtime/panic.go:426 +0x4e9
main.GetPageAndJSON(0x0, 0x0)
    /home/hackerzgz/workspace/golang/src/getAcFunPage/main.go:130 +0x20a
main.HandleGetResp(0x7f2103407500, 0xc8212fb450, 0xc8210a68c0)
    /home/hackerzgz/workspace/golang/src/getAcFunPage/main.go:82 +0x18
net/http.HandlerFunc.ServeHTTP(0x8902f0, 0x7f2103407500, 0xc8212fb450, 0xc8210a68c0)
    /usr/local/go/src/net/http/server.go:1618 +0x3a
net/http.(*ServeMux).ServeHTTP(0xc820015740, 0x7f2103407500, 0xc8212fb450, 0xc8210a68c0)
    /usr/local/go/src/net/http/server.go:1910 +0x17d
net/http.serverHandler.ServeHTTP(0xc82008a680, 0x7f2103407500, 0xc8212fb450, 0xc8210a68c0)
    /usr/local/go/src/net/http/server.go:2081 +0x19e
net/http.(*conn).serve(0xc820f87f80)
    /usr/local/go/src/net/http/server.go:1472 +0xf2e
created by net/http.(*Server).Serve
    /usr/local/go/src/net/http/server.go:2137 +0x44e
```
出现这个错误的时候， `webbench` 的参数为 `-c 300 -t 60` ，也就是并发300个客户端访问并持续60s。

由报错信息第一行中的 `dial tcp :6379` 很容易看出，这是由 Redis 所引起的错误。

### Why
根据 [Stack Overflow](http://stackoverflow.com/questions/19971968/go-golang-redis-too-many-open-files-error) 上的一个回答，这是由于 Linux 下设置的 **文件描述符上限(file descriptors limit)** 所引起的错误，在Ubuntu系统中，该值上限为 **1024** ，于是当 Redis 需要接收来自高并发所带来的连接请求时，连接数很有可能 **超出文件描述符的上限值** ，于是 Redis 就会报错了。

> 文件描述符:    
> 内核（kernel）利用文件描述符（file descriptor）来访问文件。文件描述符是非负整数。打开现存文件或新建文件时，内核会返回一个文件描述符。读写文件也需要使用文件描述符来指定待读写的文件。

### Solve
要解决这个问题也很简单，只需要将服务器系统的文件描述符上限修改成一个更大的值即可：

```shell
$ ulimit -n 99999
```

然后还需要对 Redigo 的连接池设置做出修改：

```go
return &redis.Pool{
		MaxIdle:     64,
		IdleTimeout: 3 * time.Second,
		MaxActive:   99999, // max number of connections
		...
}
```

编译，测试。终于不再报错了。


