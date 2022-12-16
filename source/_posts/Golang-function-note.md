---
title: Golang 学习笔记——func函数
date: 2022-05-2 2:17:48
updated: 2022-05-2 2:17:48
categories: Golang
tags: [Golang, Coding]
description: 
index_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png
banner_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png
---

# go 函数.
![](http://pic.lskyl.xyz/blog/Golang/func-2.png)  

## 函数调用过程
![](http://pic.lskyl.xyz/blog/Golang/func-1.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-3.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-4.png)  

## 函数的参数使用.
![](http://pic.lskyl.xyz/blog/Golang/func-5.png)  

## 实参和形参:  
![](http://pic.lskyl.xyz/blog/Golang/func-6.png)  

## 函数传参:  
![](http://pic.lskyl.xyz/blog/Golang/func-7.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-8.png)  

## 可变参数:
![](http://pic.lskyl.xyz/blog/Golang/func-9.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-10.png)  

## 参数传递:
![](http://pic.lskyl.xyz/blog/Golang/func-13.png)  
### 参数的值传递:  
![](http://pic.lskyl.xyz/blog/Golang/func-11.png)  
### 参数的引用传递:  
![](http://pic.lskyl.xyz/blog/Golang/func-12.png)  

## 函数的返回值
![](http://pic.lskyl.xyz/blog/Golang/func-17.png)  

---

![](http://pic.lskyl.xyz/blog/Golang/func-14.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-15.png)  
> 在定义参数返回值的时候已经初始化了.

### 函数多返回值
![](http://pic.lskyl.xyz/blog/Golang/func-16.png)  

### Return 语句
![](http://pic.lskyl.xyz/blog/Golang/func-18.png)  
`return`语句：词义"返回"  
1. 一个函数有返回值，那么使用return将返回值返回给调用处
2. 同时意味着结束了函数的执行

**注意点:**
1. 一个函数定义了返回值，必须使用return语句将**结果返回给调用处**。return后的数据必须和**函数定义的一致**：个数，类型，顺序。
2. 可以使用`_`,来舍弃多余的返回值
3. 如果一个函数定义了有返回值，那么函数中有分支，循环，那么要保证，无论执行了哪个分支，都要有`return`语句被执行到.
4. 如果一个函数没有定义返回值，那么函数中也可以使用return,专门用于**结束函数的执行**。

## 函数中变量的作用域
![](http://pic.lskyl.xyz/blog/Golang/func-19.png)  
> 随着函数的结束,函数内的变量自己会销毁.

### 全局变量和局部变量
![](http://pic.lskyl.xyz/blog/Golang/func-20.png)  
> 全局变量 **不支持简短定义的写法**

## 递归函数
![](http://pic.lskyl.xyz/blog/Golang/func-21.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-22.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-23.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-24.png)  

## defer(延迟函数)

**defer的词义:"延迟","推迟"**  
在go语言中,使用 `defer` 关键字来延迟一个函数或者方法的执行.  
1. deferi函数或方法：**一个函数或方法的执行被延迟了。**
2. defer的用法:  
   对象,close(),临时文件的删除.  
   文件.open()  
   defer close() 关闭文件  
3. 语言中关于异常的处理，
使用 `panic()` 和 `recover()`    
panic函数用于引发恐慌，导致程序中断执行
recover函数用于恢复程序的执行，`recover()`语法上要求必须在 `defer` 中执行。
4. 如果多个defer函数：
5. deferi函数传递参数的时候：

### 栈的结构(先进后出!后进先出!)
![](http://pic.lskyl.xyz/blog/Golang/func-25.png)  
当一个函数有多个延迟调用时，它们被添加到一
个堆栈中，并Last In First Out(LIFO) **先进后出!后进先出!** 的顺序中执行。

![](http://pic.lskyl.xyz/blog/Golang/func-26.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-27.png)  

### defer 注意点

1. 当外围函数中的语句正常执行完毕时，只有其中所有的延迟函数都执行完毕，外围函数才会真正的结束执行。
2. 当执行外围函数中的return语句时，只有其中所有的延迟函数都执行完毕后，外围函数才会真正返回。
3. 当外围函数中的代码引发运行恐慌时，只有其中所有的延迟函数都执行完毕后，该运行时恐慌才会真正被扩展至调用函数。

## 函数的数据类型
函数的类型：  
`func(参数列表的数据类型)(返回值列表的数据类型)`  

![](http://pic.lskyl.xyz/blog/Golang/func-31.png)  

### 基本类型回顾
![](http://pic.lskyl.xyz/blog/Golang/func-28.png)  

## 函数的本质
![](http://pic.lskyl.xyz/blog/Golang/func-30.png)  

---

![](http://pic.lskyl.xyz/blog/Golang/func-29.png)
> "说到底,函数也是一种特殊类型的变量."  

![](http://pic.lskyl.xyz/blog/Golang/func-32.png)  

## 匿名函数
![](http://pic.lskyl.xyz/blog/Golang/func-36.png)  

---

![](http://pic.lskyl.xyz/blog/Golang/func-34.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-35.png)  

## 回调函数
![](http://pic.lskyl.xyz/blog/Golang/func-41.png)  

---

![](http://pic.lskyl.xyz/blog/Golang/func-37.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-38.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-39.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-40.png)  

## 闭包
1. go 语言支持函数式编程:  
	支持将一个函数作为另一个函数的参数  
	也支持将一个函数作为另一个函数的返回值.  
2. 闭包(closure):  
   1. 一个外层函数中,有内层函数,该内层函数中,会操作外层函数的**局部变量**(外层函数中的参数，或者外层函数中直接定义的变量),并且该外层函数的返回值就是这个内层函数.  
   这个**内层函数和外层函数的局部变量**，统称为**闭包结构**。
   1. 局部变量的**生命周期**会发生改变，正常的局部变量随着函数调用而创建，随着函数的结束而销毁  
   但是闭包结构中的外层函数的局部变量并不会随着外层函数的结束销毁，因为内层函数还要**继续使用**。

---

![](http://pic.lskyl.xyz/blog/Golang/func-43.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-42.png)  
![](http://pic.lskyl.xyz/blog/Golang/func-44.png)  
