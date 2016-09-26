+++
Categories = ["Development", "GoLang", "Beego"]
Description = ""
Tags = ["Development", "golang", "beego"]
date = "2016-09-01T18:45:49+08:00"
menu = ""
title = "How to Use Captcha in Beego Correct"

+++

## Intro
最近在做 Beego 的 Web 网站开发，主题是 `个人词典` ，项目地址 [点我](https://github.com/HackeZ/Personal-Dictionary) 。
在关于 Beego 的验证码使用方法上出现了问题，后面通过阅读 Beego 的源码解决了该问题，下面来详细讲诉一下。

## Problem
先来看看错误的代码吧

```go
// ...
var cpt *captcha.Captcha

func (c *MainController) Login () {
    // ...
    // Get Verification Code.
    store = cache.NewMemoryCache()
    cpt = captcha.NewWithFilter("/captcha/", store)
	cpt.ChallengeNums, _ = beego.AppConfig.Int("captcha_length")
	cpt.StdWidth = 100
	cpt.StdHeight = 42

    c.TplName = "login.tpl"
}
```

这样写出现的问题是，开启服务器之后，只有第一次输入的验证码是正确的，一旦刷新页面或者已经登录之后，无论再次怎么进行登录都是 `验证码不正确` 的错误。
甚至在很多情况下都会出现验证码图片显示不出来。

## Solve
其实很容易就可以想到是 Cache 模块而导致的问题，因为 Beego 的 Memory Cache 从源代码中看其实就是一个存放在内存的 `Map` ，而且该 `Map` 是带生存周期的。所以应该将 `Cache` 设置为一个全局变量，那么才能可以让 `Captcha` 每次都能到正确的内存地址中的 Map 存取数据。而不是每次访问都新建一次 `Cache` 。

正确的用法如下：

```go
// ...
var cpt *captcha.Captcha
var store cache.Cache

func (c *MainController) Login () {
    // ...
    // Get Verification Code.
    cpt = captcha.NewWithFilter("/captcha/", store)
	cpt.ChallengeNums, _ = beego.AppConfig.Int("captcha_length")
	cpt.StdWidth = 100
	cpt.StdHeight = 42

    c.TplName = "login.tpl"
}

func init() {
    store = cache.NewMemoryCache()
}
```

这样，无论是怎么折腾该验证码， `Captcha` 都能到正确的 `Map` 中进行操作，一切的问题都解决了。
