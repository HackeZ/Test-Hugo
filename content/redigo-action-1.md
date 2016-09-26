+++
Categories = ["Development", "GoLang","Redis"]
Description = "This is Redigo Action."
Tags = ["Development", "golang","redis","DB"]
date = "2016-07-22T10:41:10+08:00"
menu = ""
title = "Redigo Action - 1"

+++

# Redigo Action

#### Redis 作为一个内存型的高性能数据库，如今是越来越火了，为了得到更高的 QPS 以及 TPS ，我们无法忽视掉这个如此强大的数据库。

#### 在 Redis 官网中，Golang语言的[框架](http:redis.io/clients#go)有两个是被官方所推荐的，分别为：

**1. [Redigo](https:github.com/garyburd/redigo)**
**2. [Radix](https:github.com/mediocregopher/radix.v2)**

#### 本着源码易读优先，我选择了 Redigo 进行开发尝试，项目地址[点我](https:www.github.com/HackeZ/getAcFunPage)。

### Action
 熟悉Redis的同学都知道，Redis是 **单进程，单线程，IO多路复用** 的，这一点不同于 MySQL 的多线程。
 这就意味这Redis可以使用长连接来进行通信，那么，我们就需要一个连接池去管理这些长连接，当一个长连接使用完毕之后就可以交给下一个长连接继续进行使用。

> **连接池**
> 基本思想是在系统初始化的时候，将数据库连接作为对象存储在内存中，当用户需要访问数据库时，并非建立一个新的连接，而是从连接池中取出一个已建立的空闲连接对象。使用完毕后，用户也并非将连接关闭，而是将连接放回连接池中，以供下一个请求访问使用。
> 而连接的建立、断开都由连接池自身来管理。同时，还可以通过设置连接池的参数来控制连接池中的初始连接数、连接的上下限数以及每个连接的最大使用次数、最大空闲时间等等。
> 也可以通过其自身的管理机制来监视数据库连接的数量、使用情况等。

而 Redigo 就是支持连接池的，看看 [Redigo - Pool](https:github.com/garyburd/redigo/blob/master/redis/pool.go#L43).
其 L43 ～ L92 就给出了一个完整的 连接池 的正确打开方式。

```go
func newPool(server, password string) *redis.Pool {
      return &redis.Pool{
          MaxIdle: 3,
          IdleTimeout: 240 * time.Second,
          Dial: func () (redis.Conn, error) {
            c, err := redis.Dial("tcp", server)
            if err != nil {
                return nil, err
            }
            if _, err := c.Do("AUTH", password); err != nil {
                c.Close()
                return nil, err
              }
               return c, err
          },
          TestOnBorrow: func(c redis.Conn, t time.Time) error {
              _, err := c.Do("PING")
              return err
          },
      }
  }
```

这段简单易懂的代码返回了一个可用的 Redis 连接池，为了能够进行长连接处理，我们还需要定义一个全局的 **redis.Pool** 变量进行使用。

```go
  var (
      pool *redis.Pool
      redisServer = flag.String("redisServer", ":6379", "")
      redisPassword = flag.String("redisPassword", "", "")
  )

  func main() {
      flag.Parse()
      pool = newPool(*redisServer, *redisPassword)
      ...
  }
```

当 **request请求** 来到，我们就可以这样进行获取连接，并且一定记得在使用完毕之后将连接放回连接池。

```go
   func serveHome(w http.ResponseWriter, r *http.Request) {
       conn := pool.Get()
       defer conn.Close()
       ....
   }
```

##### 到了这里，一个可用并且高性能的 Redis 数据库的连接已经基本构建完毕了！
##### 接下来就可以愉快地进行使用了～


