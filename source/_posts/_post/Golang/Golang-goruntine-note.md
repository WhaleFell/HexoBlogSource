---

title: Golang 学习笔记——Goruntine并发协程
date: 2022-12-30 11:11:48
updated: 2022-12-30 11:11:48
categories: Golang
tags: [Golang, Coding]
description:
thumbnail: http://oss.whaleluo.top/blog/Golang/icon_img.png-picsmall
banner_img: http://oss.whaleluo.top/blog/Golang/icon_img.png-picsmall

---

# Golang 学习笔记——Goruntine 并发协程

并发程序指同时进行多个任务的程序，Web 服务器会一次处理成千上万的请求。

Go 语言中的并发程序可以用两种手段来实现。本节讲解 goroutine 和 channel，其支持“**顺序通信进程**”（communicating sequential processes）或被简称为 CSP。

在这种编程模型中值会在不同的运行实例（goroutine）中传递，通过 channel 进行不同 Goroutine 间的数据共享。

可以简单地把 goroutine 类比作一个线程，当一个程序启动时，其主函数即在一个单独的 goroutine 中运行，我们叫它 main goroutine（主线程）。

新的 Goroutine 会用 go 语句来创建。在语法上，go 语句是**一个普通的函数或方法调用前加上关键字 go**。go 语句会使其语句中的函数在一个新创建的 goroutine 中运行。**而 go 语句本身会迅速地完成**。（非阻塞）

```go
f()    // call f(); wait for it to return
go f() // create a new goroutine that calls f(); don't wait
```

**主函数返回** 时，**所有的 goroutine 都会被直接打断，程序退出**。除了从主函数退出或者直接终止程序之外，没有其它的编程方法能够让一个 goroutine 来打断另一个的执行。

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

`go` 后跟的 **函数的参数** 会在 go 语句自身执行时被求值，就是在 main goroutine 中被求值。

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

一个无缓冲的 channel 的发送操作会导致发送者的 goroutine 堵塞，直到另一个 goruntine 在相同的 channel 上执行接收操作，堵塞才会解除，反之亦然。

当发送的值通过 channel 成功传输之后，两个 goroutine 才可以继续执行后面的语句。

```go
func main() {
    // 建立 tcp 服务器
    conn, err := net.Dial("tcp", "localhost:8000")
    if err != nil {
        log.Fatal(err)
    }
    // 信息事件
    done := make(chan int)
    go func() {
        io.Copy(os.Stdout, conn)
        log.Println("done")
        done <- 1 // 发送一个 goruntine 已经完成的信息
    }()
    mustCopy(conn, os.Stdin)
    conn.Close()
    <-done // 等待后台 goruntine 完成
}
```

### 检查 Channels 是否关闭

接收 channels 的语句可以写成：第二个结果的一个 bool ，true 表示成功从 channels 接收到值，false 表示 channels 已经被关闭并且里面没有值可接收。

```go
// 方法一
x, ok := <-channel
if !ok {
    // channel error do something....
}
// 方法二
if x, ok := <-channel;ok{
    // channel succues do something.....
}
```

因为这种处理模式很常见，Go 语言的 range 循环可以直接在 channels 上面迭代，它依次从 channel 接收数据，当 channel 被关闭并且没有值可接收时跳出循环。

```go
for x := range channel {
    // rev channel to x and do something....
}
// 当 channel 被关闭时会主动跳出循环
close(channel)
```

不管一个 channel 是否被关闭，当它没有被引用时将会被 Go 语言的垃圾自动回收器回收。所以不需要显性的 `close(channel)`

不要将关闭一个打开文件的操作和关闭一个 channel 操作混淆。**对于每个打开的文件，都需要在不使用的时候调用对应的 Close 方法来关闭文件。**

### 单方向的 Channel

当一个 channel **作为一个函数参数** 时，它一般总是被专门用于只发送或者只接收。这是为了防止滥用，可以定义只接收或只发送的 channel ，这种限制将在编译期检测。

```go
chan <- int // 只发送 int 的 channel
<-chan int // 只接收 int 的 channel
```

close channel 只用于停止向 channel 发送新数据。但不能阻止接收 channel 的数据。所以 **对一个只接受的 channel 调用 close 将引起编译错误**。

```go
channel := make(chan int)
// 单方向的 channel 通常用于函数参数
func inChannel(in <-chan int) // 仅接收
func outChannel(out chan<-int) // 仅发送
```

调用 `inChannel` 时 in 的类型将隐式地从 chan int 转换成 chan<- int。任何双向 channel 向单向 channel 变量的赋值操作都将导致该隐式转换。但是反之不能将单向 channel 赋值给 双向 channel

### 带缓存 (*buffer*) 的 Channels

```go
ch = make(chan string, 3)
```
