---
title: Golang 学习笔记——Go 接口
date: 2022-05-16 18:30:48
updated: 2022-05-16 18:30:48
categories: Golang
tags: [Golang, Coding]
description: 
index_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png-picsmall
banner_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png-picsmall
---
# Go 接口

**面向对象** 世界中的接口的一般定义是"**接口定义对象的行为**"。它表示让指定对象应该做什么。实现这种行为的方法（实现细节）是**针对对象**的。  

在 `Go` 中，接口是 **一组方法签名**。当类型为接口中的所有方法提供定义时，它被称为 **实现接口**。它与OOP非常相似。接口指定了类型 **应该具有** 的方法，类型决定了 **如何实现** 这些方法。

> 它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口  
>
> 接口定义了一组方法，如果某个对象实现了某个接口的所有方法，则此对象就实现了该接口。

## 图示

![](http://pic.lskyl.xyz/blog/Golang/interface-1.png-picsmall)  

## 示例

**表达一个类型属于某个接口只要这个类型实现这个接口。**

```go
var w io.Writer
w = os.Stdout // OK: *os.File has Write method
w = new(bytes.Buffer) // OK: *bytes.Buffer has Write method
w = time.Second // compile error: time.Duration lacks Write method

var rwc io.ReadWriteCloser
rwc = os.Stdout // OK: *os.File has Read, Write, Close methods
rwc = new(bytes.Buffer) // compile error: *bytes.Buffer lacks Close method
```

![](http://pic.lskyl.xyz/blog/Golang/interface-2.png-picsmall)  
![](http://pic.lskyl.xyz/blog/Golang/interface-4.png-picsmall)  
![](http://pic.lskyl.xyz/blog/Golang/interface-3.png-picsmall)  

> Go语言中接口和类型的实现方式是 **非侵入式** 的,接口定义的方法没有具体代码.

## 通过接口模拟多态

### 多态的定义

"一个事物的多种形态"  

![](http://pic.lskyl.xyz/blog/Golang/interface-5.png-picsmall)  

就一个接口的实现:  

1. 看成 **实现本身的类型**，能够访问 **实现类中的属性和方法**.  
2. 看成 **对应的接口类型**，那就 **只能够访问接口中的方法**.  

接口的用法：  

1. 一个函数如果接受 **接口类型作为参数**，那么实际上 **可以传入该接口的任意实现类** 对象作为参数。  
2. 定义一个类型为 **接口类型**，实际上可以 **赋值为任意实现类的对象**.

鸭子类型:  
> 待补充...

## 空接口

`interface{}` 不包含任何的方法,所以**任何类型都是空接口的实现类**,因此空接口可以储存任何类型的数值.  
![](http://pic.lskyl.xyz/blog/Golang/interface-7.png-picsmall)  

### 空接口定义任意类型的数据

![](http://pic.lskyl.xyz/blog/Golang/interface-6.png-picsmall)  

### 空接口作为函数的参数

表示函数的参数可以是任意类型,相当于 Python 的 `typing.Any`  

![](http://pic.lskyl.xyz/blog/Golang/interface-8.png-picsmall)  
`fmt` 包就是应用了空接口,可以传入任何参数.  
`fmt` 包下的 `Print` 系列函数:  

```go
func Print(a ...interface{}) (n int,err error)
func Printf(format string,a ...interface{}) (n int,err error)
func Println(a ...interface{}) (n int,err error)
```

### 复合数据结构使用空接口

![](http://pic.lskyl.xyz/blog/Golang/interface-9.png-picsmall)  
![](http://pic.lskyl.xyz/blog/Golang/interface-10.png-picsmall)  

## 接口嵌套

接口允许多继承.  

![](http://pic.lskyl.xyz/blog/Golang/interface-11.png-picsmall)  

如果 Cat 想实现接口C,不仅要实现接口C自己的方法,还要实现接口C继承的接口A B中的方法.  

![](http://pic.lskyl.xyz/blog/Golang/interface-12.png-picsmall)  
![](http://pic.lskyl.xyz/blog/Golang/interface-13.png-picsmall)  
![](http://pic.lskyl.xyz/blog/Golang/insterface-14.png-picsmall)  

## 接口断言

前面说过，因为空接口 `interface` 没有定义任何函数，因此 `Go` 中**所有类型都实现了空接口**。当一个函数的形参是 `interface{}` ,那么在函数中，需要对形参**进行断言**，从而**得到它的真实类型**。

![](http://pic.lskyl.xyz/blog/Golang/interface-15.png-picsmall)  

方法一:  
![](http://pic.lskyl.xyz/blog/Golang/interface-16.png-picsmall)  
方法二:  
![](http://pic.lskyl.xyz/blog/Golang/interface-17.png-picsmall)  

## 接口值

接口值由两部分组成：一个具体的类型（type）和这个类型的值（value）。**接口是动态类型。**一个接口值可以持有任意大的动态值。实现了这个接口的类型都可以赋值这个接口。

![](http://pic.lskyl.xyz/blog/img/20221229121130.png-picsmall)

定义一个 nil 接口：

```go
var w io.Writer // 空接口
w.Writer([]byte("hello")) // panic: nil pointer
// 可以通过 w == nil 判断接口值是否为空
```

将 `*os.File` 类型赋值给变量 w，因为 `*os.File` 实现了 `io.Writer` 接口，所以赋值合法。赋值过程调用了一个 **具体类型到接口类型** 的隐式转换（变成T类型 `T(type)` ） `io.Writer(os.Stdout)`

```go
w = os.Stdout
// 隐式转换 
io.Writer(os.Stdout)
```

接口的 value 持有 `os.Stdout` 的拷贝，这是一个代表处理标准输出的 `os.File` 类型的指针。

![接口的赋值](http://pic.lskyl.xyz/blog/img/20221229122508.png-picsmall)

调用一个包含 `*os.File` 类型指针的接口值的 `Write` 方法，得`(*os.File).Write` 方法被调用。这个调用输出 “hello”。

```go
w.Write([]byte("hello")) // "hello"
(*os.File).Write // 等效
```

当我们处理错误或者调试的过程中，得知接口值的动态类型是非常有帮助的。所以我们使用 `fmt` 包的 `%T` 动作，在fmt包内部，使用反射来获取接口动态类型的名称。

```go
var w io.Writer
fmt.Printf("%T\n", w) // "<nil>"
w = os.Stdout
fmt.Printf("%T\n", w) // "*os.File"
w = new(bytes.Buffer)
fmt.Printf("%T\n", w) // "*bytes.Buffer"
```

### ⚠️一个包含nil指针的接口不是nil接口

一个不包含任何值的 nil 接口值：

![一个不包含任何值的 nil 接口值](http://pic.lskyl.xyz/blog/img/20221229121130.png-picsmall)

一个刚好包含nil指针的接口值：

![](http://pic.lskyl.xyz/blog/img/20221229123705.png-picsmall)

```go
var w io.Writer
w = new(bytes.Buffer)  // new函数返回类型的初始化值的指针 nil w: *bytes.Buffer
// w not nil
```

```go
func main() {
    var w io.Writer
    w = new(bytes.Buffer)
    fmt.Printf("w: %T\n", w)
    if w == nil{
        fmt.Println("w is nil")
    } else {
        fmt.Println("w not nil")
    }
}

// output:
// w: *bytes.Buffer
// w not nil
```

## 应用

### `sort.Interface`  接口

在很多语言中，排序算法都是和序列数据类型关联，但 Go 语言的 `sort.Sort` 函数不会对具体的序列和它的元素做限定。它使用一个接口类型 `sort.Interface` 来指定通用的序列算法。

一个内置的排序算法需要知道三个东西：序列的长度，表示两个元素比较的结果，一种交换两个元素的方式；这就是 `sort.Interface` 接口的三个方法：

```go
package sort
type Interface interface {
    Len() int // 序列长度
    Less(i, j int) bool // 两个元素比较的结果
    Swap(i, j int) // 交换两个元素
}
```

排列字符串切片：

```go
sort.Sort(StringSlice(names)) // 将切片转换为实现 `sort.Interface` 接口的类型
sort.Strings(names) // 简化
```

### `http.Handler` 接口

