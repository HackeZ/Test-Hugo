+++
Categories = ["Development", "GoLang"]
Description = ""
Tags = ["Development", "golang"]
date = "2016-09-16T10:07:54+08:00"
menu = ""
title = "tcpdump in Action"

+++

## Intro 

昨天上线了新完成的网站 [Personal-Dictionary](http://123.207.0.81:8563) [源码在此]( https://www.github.com/HackeZ/Personal-Dictionary)

但是上线期间却发生网站从 http://127.0.0.1:8563 可以访问，但是外网访问却显示 `“无法访问此网络”` 的情况，最后通过分析抓包工具 `tcpdump` 的结果解决了该问题。

## Problem

这是一个 `Beego` 应用，按理来说只要编译出二进制文件，然后将静态文件和二进制文件发送到服务器端，然后运行该二进制文件即可。

编译 Linux OS 下可执行文件的命令为

`$ CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o PD main.go`

编译完成之后， 通过 `$ scp PD root@123.207.0.81:/home/...` 将文件上传至服务器。

使用 `$ nohup ./PD &` 即可将该应用以后台服务的形式运行在服务器中。

但是问题出现了，通过外网的 IP 地址访问该 应用的端口地址却提示 `“无法访问此网络”` 的情况， 然后我使用 `$ curl http://127.0.0.1:8563` 却能直接返回网页内容！

## Slove

为了解决这个问题，我最先想到的就是该端口是否被外网的中间件屏蔽了呢。有一个最直观的方法可以观察检查该情况，就是使用 `tcpdump` 抓包工具！

在服务器端启动应用之后，使用 `$ tcpdump 网卡[eth0, lo] tcp port 8563 host 123.207.0.81`

去截获访问该应用的端口 TCP 包，然后通过浏览器访问 http://123.207.0.81:8563 测试 TCP 包是否能正确到达 服务器的该端口。

结果 tcpdump 截获到的结果可以简化为

```
.... [S]
.... [R.]
.... [S]
.... [R.]
.... [S]
.... [R.]
```

很明显了：
    - `S` 代表着 `SEND`
    - `R` 代表着 `RST`

浏览器试图访问该端口，然后服务器直接就 RST 掉该请求，这样的情况下很可能就是该端口没有被打开！

关于更多的 RST 情况我推荐一篇 Blog —— [costaxu](https://my.oschina.net/costaxu/blog/127394)

根据上面的情况，我很快就能定位到问题所在：

```go
// main.go
func main() {
    beego.Run("127.0.0.1"+beego.AppConfig.String("httpport")) // 这里只指定了内网的服务地址...
}
```

最后，只需要将 `beego.Run()` 替换掉上面的即可解决。
