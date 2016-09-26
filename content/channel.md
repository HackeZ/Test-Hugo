+++
Categories = ["Development", "GoLang"]
Description = "The Channel in Golang"
Tags = ["Development", "golang", "Goroutine"]
date = "2016-05-11T19:41:18+08:00"
menu = ""
title = "The Channel in Golang"

+++

# 
Golang中的Channel分析

### 
作为Golang语言的核心，并发编程是学习Golang的必经之路。对于不同进程之间的通信手段总会涉及到跨进程通信，那么这个通信手段必须是一个可共享内存的方法，而Golang提倡的理念为：

> “应该以通信作为手段来共享内存”

### 
而这一句话的直接体现在于Golang所提供的一个预定义数据类型 —— **Channel**

### 
Channel提供了一种机制。它既可以 **同步** 两个被并发执行的函数，又可以让这两个函数通过传递特定类型的值来进行 **通信**。使用Channel可以让我们编写更清晰且正确的代码。

### 
关于使用Channel需要记住的知识点：

- 在同一时刻，仅有一个Goroutine能向同一个Channel发送元素值，同时也只有一个Goroutine能从它哪里接收元素值。
- Channel是一个FIFO的消息队列。
- Channel中的元素值已经确保具有原子性。
- Channel可以分为缓冲与非缓冲，它们之间的差别非常大。
- Channel可分为双向与单向，一般通道都会声明为双向，只有在限制函数体中使用通道的方式（只允许发送或接收）才会使用单向Channel。

## Talk is Cheap,Show me the Code!

- 初始化通道

### 
因为Channel属于引用类型之一，所以必须使用make关键字初始化它。


```go
// 缓冲通道（容纳int类型元素，有长度，可暂存元素）
intChan := make(chan int, 5)

// 非缓冲通道（容纳byte类型元素，无长度，不可暂存元素）
byteChan := make(chan byte)
```

- 发送元素值

```go
// 向intChan通道发送一个元素值为5的元素
intChan <- 5
```

### 
注意：向一个值为nil的Channel进行发送操作会造成当前Goroutine **永久阻塞！**。    
而向一个已经塞满元素的Channel进行发送操作则会将当前的Goroutine **阻塞**，直至Channel中的元素被接收，所以一般会在 **select** 代码块中进行发送操作。

- 接收元素值

```go
// 在intChan通道中接收一个元素值
elem := <-intChan

// 接收元素值，并判断该通道是否已经关闭
elem, ok := <-intChan
if !ok {
    fmt.Println("Channel is Closed!")
}
```

### 
同样需要注意的是，如果向一个值为nil的Channel进行接收操作，同样会造成 **永久阻塞！**    
而向一个没有元素值的Channel进行接收操作，也会将当前的Goroutine **阻塞**，直至Channel中有了新的元素。

- 关闭Channel

### 
关闭Channel并不是如其字面意思，完全将Channel关闭。而其正确的作用是告诉系统，不应该再允许任何针对被关闭的通道的发送操作，该通道已经被关闭，但是已经缓存在Channel中的元素不会受到影响，这也是Channel非常优秀的特性之一。

```go
// 调用内建函数close()关闭Channel
close(intChan)
```

### 
注意，无论任何时候，我们都 **不应该** 在接收端关闭Channel，因为我们永远都不知道发送端是否已经将元素发送完毕。

---

> 最后，可以到[这里](https://www.github.com/HackeZ)学习更多的Channel相关代码！