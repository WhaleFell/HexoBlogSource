---
title: Golang 学习笔记——Goruntine并发协程
date: 2022-12-30 11:11:48
updated: 2022-12-30 11:11:48
categories: Golang
tags: [Golang, Coding]
description: 
index_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png-picsmall
banner_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png-picsmall
---
# Golang 学习笔记——Goruntine并发协程

并发程序指同时进行多个任务的程序，Web服务器会一次处理成千上万的请求。

Go语言中的并发程序可以用两种手段来实现。本节讲解 goroutine 和channel，其支持“**顺序通信进程**”（communicating sequential processes）或被简称为CSP。

在这种编程模型中值会在不同的运行实例（goroutine）中传递，通过 channel 进行不同 Goroutine 间的数据共享。

可以简单地把 goroutine 类比作一个线程，当一个程序启动时，其主函数即在一个单独的 goroutine 中运行，我们叫它 main goroutine（主线程）。

新的 Goroutine 会用 go 语句来创建。在语法上，go 语句是**一个普通的函数或方法调用前加上关键字go**。go语句会使其语句中的函数在一个新创建的goroutine中运行。**而go语句本身会迅速地完成**。（非阻塞）

```go
f()    // call f(); wait for it to return
go f() // create a new goroutine that calls f(); don't wait
```

**主函数返回** 时，**所有的 goroutine 都会被直接打断，程序退出**。除了从主函数退出或者直接终止程序之外，没有其它的编程方法能够让一个goroutine 来打断另一个的执行。

Go 显示一个加载动画函数，可以放在 `go spinner(100 * time.Millisecond)` 执行

```go
func spinner(delay time.Duration) {
    for {
        for _, r := range `-\|/` {
            fmt.Printf("\r%c", r)
            time.Sleep(delay)
        }
    }
}
```

`go` 后跟的 **函数的参数** 会在go语句自身执行时被求值，就是在 main goroutine 中被求值。

## Channels

Channel 是 goruntine 间的通信管道，每个 channel 都有一个可发送数据的类型。

可以使用 make 函数，创建一个 channel：

```go
ch := make(chan int) // ch has type 'chan int'
// 返回一个 chan int 类型的
```

和 map 类似，channel 也对应一个 make 创建的 **底层数据结构的引用**。当我们复制一个 channel 或用于函数参数传递时，我们只是拷贝了一个 channel 引用，channel 的零值也是 nil。

### 发送&接收

```go
ch <- x  // 将 x 发送到 ch
x = <-ch // 从 ch 取出一个值给 x
<-ch     // 从 ch 取出一个值但是 Drop 掉
```

Channel 支持关闭 close 操作，关闭 channel 后对通道的任何发送操作都会 panic 但进行接收操作仍然可以接收到之前一键成功发送的数据，如果 channel 中已经没有数据了就返回一个零值数据。

```go
chlose(ch)
```

### 不带缓存的 Channels

[[8.4.1. 不带缓存的Channels](https://gopl-zh.github.io/ch8/ch8-04.html#841-不带缓存的channels)] 未完待续。。。。
