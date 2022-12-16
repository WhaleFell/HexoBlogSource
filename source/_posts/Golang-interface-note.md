---
title: Golang 学习笔记——Go 接口
date: 2022-05-16 18:30:48
updated: 2022-05-16 18:30:48
categories: Golang
tags: [Golang, Coding]
description: 
index_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png
banner_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png
---
# Go 接口

**面向对象** 世界中的接口的一般定义是"**接口定义对象的行为**"。它表示让指定对象应该做什么。实现这种行为的方法（实现细节）是**针对对象**的。  
在 `Go` 中，接口是**一组方法签名**。当类型为接口中的所有方法提供定义时，它被称为**实现接口**。它与OOP非常相似。接口指定了类型**应该具有**的方法，类型决定了**如何实现**这些方法。

> 它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口  
> 接口定义了一组方法，如果某个对象实现了某个接口的所有方法，则此对象就实现了该接口。

## 图示

![](http://pic.lskyl.xyz/blog/Golang/interface-1.png)  

## 示例

![](http://pic.lskyl.xyz/blog/Golang/interface-2.png)  
![](http://pic.lskyl.xyz/blog/Golang/interface-4.png)  
![](http://pic.lskyl.xyz/blog/Golang/interface-3.png)  
> Go语言中接口和类型的实现方式是**非侵入式**的,接口定义的方法没有具体代码.

## 通过接口模拟多态

### 多态的定义

"一个事物的多种形态"  
![](http://pic.lskyl.xyz/blog/Golang/interface-5.png)  

就一个接口的实现:  

1. 看成**实现本身的类型**，能够访问**实现类中的属性和方法**.  
2. 看成是**对应的接口类型**，那就**只能够访问接口中的方法**.  

接口的用法：  

1. 一个函数如果接受**接口类型作为参数**，那么实际上**可以传入该接口的任意实现类**对象作为参数。  
2. 定义一个类型为**接口类型**，实际上可以**赋值为任意实现类的对象**.

鸭子类型:  
> 待补充...

## 空接口

`interface{}` 不包含任何的方法,所以**任何类型都是空接口的实现类**,因此空接口可以储存任何类型的数值.  
![](http://pic.lskyl.xyz/blog/Golang/interface-7.png)  

### 空接口定义任意类型的数据

![](http://pic.lskyl.xyz/blog/Golang/interface-6.png)  

### 空接口作为函数的参数

表示函数的参数可以是任意类型,相当于 Python 的 `typing.Any`  

![](http://pic.lskyl.xyz/blog/Golang/interface-8.png)  
`fmt` 包就是应用了空接口,可以传入任何参数.  
`fmt` 包下的 `Print` 系列函数:  

```go
func Print(a ...interface{}) (n int,err error)
func Printf(format string,a ...interface{}) (n int,err error)
func Println(a ...interface{}) (n int,err error)
```

### 复合数据结构使用空接口

![](http://pic.lskyl.xyz/blog/Golang/interface-9.png)  
![](http://pic.lskyl.xyz/blog/Golang/interface-10.png)  

## 接口嵌套

接口允许多继承.  
![](http://pic.lskyl.xyz/blog/Golang/interface-11.png)  
如果 Cat 想实现接口C,不仅要实现接口C自己的方法,还要实现接口C继承的接口A B中的方法.  
![](http://pic.lskyl.xyz/blog/Golang/interface-12.png)  
![](http://pic.lskyl.xyz/blog/Golang/interface-13.png)  
![](http://pic.lskyl.xyz/blog/Golang/insterface-14.png)  

## 接口断言

前面说过，因为空接口 `interface` 没有定义任何函数，因此 `Go` 中**所有类型都实现了空接口**。当一个函数的形参是 `interface{}` ,那么在函数中，需要对形参**进行断言**，从而**得到它的真实类型**。

![](http://pic.lskyl.xyz/blog/Golang/interface-15.png)  

方法一:  
![](http://pic.lskyl.xyz/blog/Golang/interface-16.png)  
方法二:  
![](http://pic.lskyl.xyz/blog/Golang/interface-17.png)  
