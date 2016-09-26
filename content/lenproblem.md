+++
Categories = ["Development", "GoLang"]
Description = ""
Tags = ["Development", "golang"]
date = "2016-06-02T11:08:54+08:00"
menu = ""
title = "Named Question in Golang"

+++

# 
Golang中遇到的命名问题

### 
昨天在写随机生成字符串代码时候遇到了一个Golang的命名问题，代码如下：

```go
func GetRandomString(len string) string {
    str := "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
    bytes := []byte(str)
    result := []byte{}
    r := rand.New(rand.NewSource(time.Now().UnixNano()))

    for i := 0; i < len; i++ {
        result = append(result, bytes[r.Intn(len(str))]) // <- Here is the Problem: `cannot call non-function len (type int)`
    }
    return string(result)
}
```


### 
这个问题太蛋疼了，之前好像从来没遇到过这个问题，而且Google好像也没有找到相关的问题原因。

### 
后来一步步排查代码，才发现问题原来是出现在：

> for i := 0; i < **len**; i++ {

### 
中的 **len** 变量与函数 **len()** 重复而出现的命名错误，所以只需要将 **len** 变量重新命名即可解决该问题。

### 
总结：在Golang中使用的变量一定不要和某个函数名字相同，否则不会通过，我现在暂时不清楚是Golang编译器出现的问题，还是Golang本来就不允许这样写，我会继续查阅相关文档查清楚！
