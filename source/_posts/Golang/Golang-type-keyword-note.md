---

title: Golang 学习笔记——Type关键字
date: 2022-05-21 8:30:48
updated: 2022-05-21 8:30:48
categories: Golang
tags: [Golang, Coding]
description:
thumbnail: http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/icon_img.png
banner_img: http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/icon_img.png

---

# Go Type 关键字

`type` 是 Go 语法里的重要而且常用的关键字,`type` 绝不只是对应于 C/C+ 中的 `typedef`.搞清楚 `type` 的使用,就容易理解 Go 语言中的核心概念 `struct`、`interface`、`函数` 等的使用。

## 通过 Type 定义新类型

```go
type 类型名 Typde
type myint int  //定义了一共 `myint` 类型它是 `int` 类型
```

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/type-2.png)

## 通过 Type 定义函数类型

Go 语言支持 **高阶函数** ,所以可以把函数作为一个函数的返回值,或者一个函数的参数.  
![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/type-1.png)

## 类型别名

常常用于代码迁移,适配新旧代码.

```go
//不是重新定义新的数据类型，只是给int起别名，和int可以通用,%T打印的类型还是int
type myint = int 
```

## 非本地类型不能定义方法

`time` 包下的 `Duration` 类型不是 `main` 包下的类型,所以不是本地类型.不能定义方法.

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/type-3.png)

## 在结构体成员嵌入时使用别名

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/type-4.png)
