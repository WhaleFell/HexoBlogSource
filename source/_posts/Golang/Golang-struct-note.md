---

title: Golang 学习笔记——struct结构体
date: 2022-12-30 18:30:48
categories: Golang
updated: 2022-12-30 18:30:48
tags: [Golang, Coding]
description:
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true

---

# Go 结构体

**结构体:** 是由一系列具有 **相同类型** 或 **不同类型** 的数据构成的数据 **集合**。

结构体成员是由 **一系列的成员变量** 构成，这些 **成员变量** 也被称为 **字段**。

**字段** 大写就表明该字段是可导出字段，可以被外部包导入。

如果考虑效率的话，较大的结构体通常会用 **指针的方式** 传入和返回。如果要在函数内部修改结构体成员的话，用 **指针传入** 是必须的；因为在 Go 语言中，所有的函数参数都是值拷贝传入的，函数参数将不再是函数调用时的原始变量。

## 结构体定义

定义结构体

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-1.png&webp=true)

初始化结构体:

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-2.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-3.png&webp=true)

如果不赋值,默认存储的是类型的零值.

## 结构体指针

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-4.png&webp=true)

### 定义结构体指针

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-5.png&webp=true)

`*` 获取指针指向的原始数据.  
`&` 获取一个对象的地址.

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-6.png&webp=true)

### 通过 `new` 构建结构体指针

> 内置函数 `new()` 专门用于创建某种类型的指针.

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-7.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-8.png&webp=true)

通过, `new()` 函数 (不为 `nil`,空指针) 指向了 **新分配的类型的内存空间** ,里面存该类型的零值.

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-10.png&webp=true)

## 结构体的匿名

### 匿名结构体

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-11.png&webp=true)

### 结构体的匿名字段

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-12.png&webp=true)

## 结构体嵌套

一个结构体可能包含一个字段，而这个字段是一个结构体。这个结构体被称为嵌套结构。

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-13.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-14.png&webp=true)

### 将结构体的地址作为字段数值 (引用传递) [建议使用]

在嵌套结构体中,结构体字段是结构体指针.使用了引用传递.

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-16.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-15.png&webp=true)

## Golang 中的 OPP(面向对象)

`Golang` 并不是面向对象的语言….但可以模拟继承性.

通过结构体嵌套模拟面向对象的继承性.

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-17.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-18.png&webp=true)

### 通过结构体的嵌套模拟继承性

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-19.png&webp=true)

`s3.Person.name->s3.name`

Student 结构体将 Person 结构体作为一个匿名字段了.

那么 Person 中的字段，对于 Student 来讲，就是提升字段.

Student 对象直接访问 Person 中的字段

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/struct-20.png&webp=true)
