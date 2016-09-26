+++
Categories = ["Development", "GoLang"]
Description = ""
Tags = ["Development", "golang"]
date = "2016-09-25T23:27:46+08:00"
menu = ""
title = "CircuitBreaker 设计模式"

+++

## Intro

今天发现了 [Sony](https://github.com/sony/) 竟然在 Github 上开源了他们的一些项目！而他们也是在用 `Golang` 在开发后台！

**Amazing**

于是不亦乐乎地看起了其中的 Golang 开源项目，而其中一个名为 [sony/gobreaker](https://github.com/sony/gobreaker) 的项目引起了我的注意。

项目简述中描述了这是一个 Golang 版本的 **CircuitBreaker** 实现！

那么什么是 **CircuitBreaker（断路器）** 呢？下面就来一起看看。

## What is CircuitBreaker

根据传统的解释，断路器是广泛用于 *电子工程产业* 的一个重要安全保障！

当你家里的洗衣机漏电了，电流就会瞬间增大，那么连接家里总线的 **断路器** 就会剩下，及时切断总电源，防止意外的发生！

那么在最近的 `微服务` 越来越流行的时代，软件架构开始将 **断路器** 这一概念添加进来了。

我们知道，当你一旦开始将系统中的一部分拆解为一个独立服务，那么你就已经走进了 `微服务` 的时代了。
而在微服务中最重要的是要保证服务运行的稳定性，如果独立服务无法提供高质量或者是不能提供服务，那么这将会导致整个系统的崩溃！

而 `微服务` 会遇到的故障有可能是：
    - 瞬时故障：如慢的网络连接、超时，资源过度使用而暂时不可用；
    - 不容易预见的突发故障：需要更长时间来纠正的故障；

而解决这些故障常常有两种方法：
    - 重试机制：对于预期的短暂故障问题，通过重试模式是可以解决的；
    - 断路器（CircuitBreaker）模式：将受保护的服务封装在一个可以监控故障的断路器对象中，当故障达到一定门限，断路器将跳闸（trip），所有后继调用将不会发往受保护的服务而由断路器对象之间返回错误。对于需要更长时间解决的故障问题，不断重试就没有太大意义了，可以使用断路器模式。

![CircuitBreaker - Sketch](http://martinfowler.com/bliki/images/circuitBreaker/sketch.png?_=0.814744712175792)

![CircuitBreaker - State](http://martinfowler.com/bliki/images/circuitBreaker/state.png?_=0.47541342622693494)

## Action in gobreaker

简单介绍完 CircuitBreaker 的概念，那么接下来就结合 [gobreaker](https://github.com/sony/gobreaker) 的源码实际看看如何设计一个 断触器。

首先一个值得我们关注的点是 CircuitBreaker State， 它被设计为 **3** 种状态：

```go
type State int
const (
    StateClosed State = iota
    StateHalfOpen
    StateOpen
)
```

CircuitBreaker 会根据当前处于不同的 `State` ，而判断最多可以通过多少个 `Request` 。

接下来是 `Setting` ，通过 Setting 对象的值，可以新建出一个 CircuitBreaker ：

```go
type Settings struct {
	Name          string  // CircuitBreaker 的名字
	MaxRequests   uint32  // 最大连接数，根据 State 会自动调节允许通过的 Request 值
	Interval      time.Duration // 当 CircuitBreaker 处于 Close 状态的时候，循环该时间段，清空连接数
	Timeout       time.Duration // 当 CircuitBreaker 处于 Open 状态的时候，如果触发了该超时时间，将它置为 Half-Open
	ReadyToTrip   func(counts Counts) bool // 判断当前失败数，是否应该进入 Close 状态
	OnStateChange func(name string, from State, to State) // 当状态发生变化时候，触发该函数
}

func NewCircuitBreaker(st Settings) *CircuitBreaker {
    ...
}
```

因为这个源码实现其实非常简单，我也就不一一讲诉了，就再将一个比较重要的函数 `Execute` 吧：

```go
func (cb *CircuitBreaker) Execute(req func() (interface{}, error)) (interface{}, error) {
	generation, err := cb.beforeRequest()
	if err != nil {
		return nil, err
	}

	defer func() {
		e := recover()
		if e != nil {
			cb.afterRequest(generation, fmt.Errorf("panic in request"))
			panic(e)
		}
	}()

	result, err := req()
	cb.afterRequest(generation, err)
	return result, err
}
```

该函数用于执行需要 CircuitBreaker 触发的函数，详情可以参考这里 => [example](https://github.com/sony/gobreaker/blob/master/example/http_breaker.go)

首先执行 CircuitBreaker 的 beforeRequest，然后执行传进来的 req 函数，最后执行 afterRequest ，并捕获异常，如果有异常， recover 它，不停止程序，返回错误信息。

## 参考网站

[mryqu - blog](http://blog.sina.com.cn/s/blog_72ef7bea0102vvsn.html)

[English](http://www.cnblogs.com/davidwang456/p/3976607.html)