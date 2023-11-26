---

title: Golang 学习笔记——数据类型
date: 2022-04-21 18:30:48
categories: Golang
updated: 2022-04-21 18:30:48
tags: [Golang, Coding]
description:
thumbnail: http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/icon_img.png
banner_img: http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/icon_img.png

---

# Go 数据类型

在 G0 编程语言中，数据类型用于 **声明函数和变量**，数据类型的出现是为了把数据分成所需**内存大小**不同的数据，编程的时候需要用大数据的时候才需要申请大内存，就可以**充分利用内存**。

G0 语言按类别有以下几种数据类型：

1. 布尔型  
   布尔型的值只可以是常量 `true` 或者 `false` 。一个简单的例子：  
   `var b bool=true`
2. 数字类型  
   整型 int 和浮点型 float32、float64,Go 语言支持整型和浮点型数字，并且支持复数，具中位的运算采用补码。
3. 字符串类型  
   字符串就是一串因定长度的字符连接起来的字符序列。G0 的字符串是由单个字节连接起来的。Go 语言的字符串的字节使用 UTF-8 编码标识 Unicode 文本，
4. 派生类型
   1. 指针类型 (Pointer)
   2. 数组类型 (c)
   3. 结构体类型 (struct)
   4. Channel: 类型
   5. 函数类型
   6. 切片类型
   7. 接口类型
   8. Map 类型

## 格式化输出类型

```go
package main

import "fmt"

func main() {
 var name string = "tom"
 age := 20
 b := true
 // 格式化输出
 // %T 类型
 fmt.Printf("%T\n", name)
 fmt.Printf("%T\n", age)
 fmt.Printf("%T\n", b)
}
```

输出:

```shell
string
int
bool
```

## 布尔类型

go 语言中的布尔类型有两个常量值：`true` 和 `false`。布尔类型经常用在**条件判断**语句，或者**循环语句**。也可以用在**逻辑表达式**中。

## 数值类型

## 派生类型

### 指针类型

```go
package main

import "fmt"

func main() {
    //指针类型
    a := 100
    p := &a //取a的内存地址 p是指针类型
    fmt.Printf("%T", p) //*int
}
```
