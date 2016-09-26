+++
Categories = ["Development", "GoLang", "Dev"]
Description = "Log Some Point in Golang."
Tags = ["Development", "golang"]
date = "2016-07-23T16:34:45+08:00"
menu = ""
title = "Golang Dev Log"

+++


## Golang Dev Log
--- 
今天快要把 [getAcFunPage](https://www.github.com/HackeZ/getAcFunPage) 这个项目完结了，结果在重构项目的时候出现了两个哭笑不得的 **BUG** 。总结下来，都是因为自身对 Golang 认识不够深入而出现的问题，所以现在这篇 Blog 是专门记录我在Golang开发中遇到的需要注意的点，以此警醒自己！

1. 一个通用的结构体应该以一个包的方式存在并进行引用，否则会出现同一个结构体在不同的包中声明之后，在调用的时候，编译器会报 `cannot use xxx (type user) as type School.user` 的错误。

```go
// Example 1

// file main.go
type user struct {
    name     string
    age      int64
}

// file school.go
type user struct {
    name     string
    age      int64
}
```

2. 如果一个函数需要使用一个 **相对路径** 调用一个 `静态文件` ，那么需要将这个 `静态文件` 的路径作为参数进行传入。    
因为 Golang 中对于 `静态文件` 的调用不是根据函数所在位置的 **相对路径** ，而是取决于调用这个函数的文件的位置所对应的相对路径。

```go
// Example 2

// ./markdown/markdown-style.go (Wrong)
func GetStyle() {
    f, err := os.OpenFile("./markdown-style.css",...)
}

// ./markdown/markdown-style.go (Corrent)
func GetStyle(filepath string) {
    f, err := os.OpenFile(filepath,...)
}

// ./main.go
func main() {
    // Wrong:  The system cannot find the file specified.
    md.GetStyle()

    // Corrent
    md.Corrent("./markdown/markdown-style.css")
}
```
