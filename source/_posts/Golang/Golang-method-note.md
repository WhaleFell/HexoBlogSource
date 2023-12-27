---

title: Golang 学习笔记——method方法
date: 2022-05-6 21:54:48
categories: Golang
updated: 2022-05-6 21:54:48
tags: [Golang, Coding]
description:
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true

---

# Go 方法

## 面向对象编程 (OOP)

一个对象其实也就是一个简单的值或者一个变量，在这个对象中会包含一些方法，而一个方法则是一个一个和特殊类型关联的函数。

G0 语言中同时有函数和方法。一个方法就是一个**包含了接受者的函数**，接受者可以是**命名类型或者结构体类型**的一个**值**或者是一个**指针**。所有给定类型的方法属于**该类型的方法集**.

方法只是一个**函数**，它带有一个**特殊的接收器类型**，它是在 `func` 关键字和方法名之间编写的。接收器可以是 `struct` 类型或非 `struct` 类型。**接收方可以在方法内部访问**。

## 对比函数

- 意义  
  方法：某个类别的行为功能，需要**指定的接受者调用**.  
  函数：一段**独立功能**的代码，可以直接调用.
- 语法  
  方法：方法名可以相同，只要接受者不同.  
  函数：命名不能冲突.

> 方法可以模拟 `class` 继承结构.

## 定义一个方法

Go 能够给任意类型定义方法，Go 和很多其它的面向对象的语言不太一样。因此在 Go 语言里，我们为一些简单的数值、字符串、slice、map 来定义一些附加行为。**方法可以被声明到任意类型**，**只要不是一个指针或者一个 interface。**

语法:

```go
func (接收者) 方法名(参数列表)(返回值列表){
    // do..something..
}
```

### 接收者为一个值

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/method-1.png&webp=true)

### 接收者为一个指针

当调用一个方法时，会对其 **每一个参数值进行拷贝**，如果一个方法需要更新一个变量，或者方法的其中一个参数实在太大我们希望能够避免进行这种默认的拷贝，这种情况下就需要用到指针了。

```go
func (p *Point) ScaleBy(factor float64) {
    p.X *= factor
    p.Y *= factor
}
```

在现实的程序里，一般会约定如果 Point 这个类有一个指针作为接收器的方法，那么所有 Point 的方法都必须有一个指针接收器.

只有类型 `(Point)` 和指向他们的指针 `(*Point)`，才是可能会出现在接收器声明里的两种接收器。此外，为了避免歧义，在声明方法时，**如果一个类型名本身是一个指针的话，是不允许其出现在接收器中的**，

```go
type P *int
func (P) f() { /* ... */ } // compile error: invalid receiver type
```

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/method-2.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/method-3.png&webp=true)

### 注意点

1. 不管你的 method 的 receiver 是指针类型还是非指针类型，都是可以通过指针/非指针类型进行调用的，编译器会帮你做类型转换。
2. 在声明一个 method 的 receiver 该是指针还是非指针类型时，你需要考虑两方面的内部，第一方面是这个 **对象本身是不是特别大**，如果声明为非指针变量时，调用会产生一次拷贝；第二方面是如果你用指针类型作为 receiver，这种指针类型指向的始终是一块内存地址，就算你对其进行了拷贝,修改数据时总是会映射到原来的内存地址.

## 方法的继承性

### OOP 继承

如果两个类 (struct) 存在**继承关系**，其中一个是子类，另一个作为父类，那么：

1. 子类可以**直接访问**父类的属性和方法
2. 子类可以**新增**自己的属性和方法
3. 子类可以**重写**父类的方法 (就是将父类已有的方法，**重新实现**)

封装的最重要的优点，是阻止了外部调用方对 **对象内部的值任意地进行修改**。因为**对象内部变量只可以被同一个包内的函数修改**，所以包的作者可以让这些函数确保对象内部的一些值的不变性。比如下面的 Counter 类型允许调用方来增加 counter 变量的值，并且允许将这个值 reset 为 0，但是不允许随便设置这个值。外部包根本访问不了 `Counter.n` 变量。

只用来访问或修改内部变量的函数被称为 `setter` 或者 `getter` 。

```go
type Counter struct { n int }
func (c *Counter) N() int { return c.n }
func (c *Counter) Increment() { c.n++ }
func (c *Counter) Reset() { c.n = 0 }
```

### 示例

`method` 是可以继承的，如果匿名字段实现了一个 `method`,那么包含这个匿名字段的 `struct` 也能调用该 `method`  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/method-4.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/method-5.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/method-6.png&webp=true)
