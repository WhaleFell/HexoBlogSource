---

title: Golang 学习笔记——func函数
date: 2022-05-2 2:17:48
updated: 2022-05-2 2:17:48
categories: Golang
tags: [Golang, Coding]
description:
thumbnail: http://oss.whaleluo.top/blog/Golang/icon_img.png-picsmall
banner_img: http://oss.whaleluo.top/blog/Golang/icon_img.png-picsmall

---

# Go 函数

```go
// 6.5, 7.8 实际参数
// x, y 形式参数
res := hypot(6.5, 7.8)

func hypot(x, y float64) float64 {
   return math.Sqrt(x*x + y*y)
}
fmt.Println(hypot(3,4)) // "5"
```

函数调用时，Go 语言没有默认参数值，也没有任何方法可以通过参数名指定形参，因此形参和返回值的变量名对于函数调用者而言没有意义。

实参通过值的方式传递，因此函数的 **形参是实参的拷贝** 。对形参进行修改不会影响实参。但是，如果实参包括 **引用类型**，如指针，slice(切片)、map、function、channel 等类型，实参可能会由于函数的简介引用被修改。

偶尔遇到没有函数体的函数声明，这表示该函数不是以 Go 实现的。这样的声明定义了函数标识符。

Go 使用控制流机制（如 if 和 return）处理异常，这使得编码人员能更多的关注错误处理。

![](http://oss.whaleluo.top/blog/Golang/func-2.png-picsmall)

## 函数调用过程

![](http://oss.whaleluo.top/blog/Golang/func-1.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-3.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-4.png-picsmall)

## 函数的参数使用

![](http://oss.whaleluo.top/blog/Golang/func-5.png-picsmall)

## 实参和形参

![](http://oss.whaleluo.top/blog/Golang/func-6.png-picsmall)

## 函数传参

![](http://oss.whaleluo.top/blog/Golang/func-7.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-8.png-picsmall)

## 可变参数

参数数量可变的函数称为为可变参数函数。需要在参数列表的最后一个参数类型之前加上省略符号“…”，这表示该函数会接收任意数量的该类型参数。

```go
func sum(vals...int) int
```

在函数体中,vals 被看作是类型为 []int 的切片。sum 可以接收任意数量的 int 型参数：

如果原始参数已经是切片类型,在最后一个参数后加上省略符。

```go
values := []int{1, 2, 3, 4}
fmt.Println(sum(values...)) // "10"
```

![](http://oss.whaleluo.top/blog/Golang/func-9.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-10.png-picsmall)

## 参数传递

实参通过值的方式传递，因此函数的 **形参是实参的拷贝** 。对形参进行修改不会影响实参。但是，如果实参包括 **引用类型**，如指针，slice(切片)、map、function、channel 等类型，实参可能会由于函数的间接引用被修改。

![](http://oss.whaleluo.top/blog/Golang/func-13.png-picsmall)

### 参数的值传递

![](http://oss.whaleluo.top/blog/Golang/func-11.png-picsmall)

### 参数的引用传递

![](http://oss.whaleluo.top/blog/Golang/func-12.png-picsmall)

## 函数的返回值

![](http://oss.whaleluo.top/blog/Golang/func-17.png-picsmall)

---

![](http://oss.whaleluo.top/blog/Golang/func-14.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-15.png-picsmall)

> 在定义参数返回值的时候已经初始化了.

### 函数多返回值

如果一个函数将所有的返回值都显示的变量名，那么该函数的 return 语句可以省略操作数。这称之为 bare return。

![](http://oss.whaleluo.top/blog/Golang/func-16.png-picsmall)

### Return 语句

![](http://oss.whaleluo.top/blog/Golang/func-18.png-picsmall)  
`return` 语句：词义 " 返回 "

1. 一个函数有返回值，那么使用 return 将返回值返回给调用处
2. 同时意味着结束了函数的执行

**注意点:**

1. 一个函数定义了返回值，必须使用 return 语句将**结果返回给调用处**。return 后的数据必须和**函数定义的一致**：个数，类型，顺序。
2. 可以使用 `_`,来舍弃多余的返回值
3. 如果一个函数定义了有返回值，那么函数中有分支，循环，那么要保证，无论执行了哪个分支，都要有 `return` 语句被执行到.
4. 如果一个函数没有定义返回值，那么函数中也可以使用 return,专门用于**结束函数的执行**。

## 函数中变量的作用域

![](http://oss.whaleluo.top/blog/Golang/func-19.png-picsmall)

> 随着函数的结束,函数内的变量自己会销毁.

### 全局变量和局部变量

![](http://oss.whaleluo.top/blog/Golang/func-20.png-picsmall)

> 全局变量 **不支持简短定义的写法**
>
> ```go
> // s := 0.12 全局变量不能使用类型推断
> var s float64 = 0.12  // 全局变量定义正确方式
> ```

## 递归函数

![](http://oss.whaleluo.top/blog/Golang/func-21.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-22.png-picsmall)

递归函数实现 fibonacci 数列 (斐波那契数列)

![斐波那契数列](http://oss.whaleluo.top/blog/Golang/func-23.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-24.png-picsmall)

## defer(延迟函数)

**defer 的词义:&quot; 延迟&quot;,&quot; 推迟&quot;**,在 go 语言中,使用 `defer` 关键字来延迟一个函数或者方法的执行.

你可以在一个函数中执行多条 defer 语句，它们的执行顺序与声明顺序相反。

defer 语句经常被用于处理成对的操作，如打开、关闭、连接、断开连接、加锁、释放锁。释放资源的 defer 应该直接跟在请求资源的语句后。

1. deferi 函数或方法：**一个函数或方法的执行被延迟了。**
2. defer 的用法:  
   对象,close(),临时文件的删除.  
   文件.open()  
   defer close() 关闭文件

```go
package ioutil
func ReadFile(filename string) ([]byte, error) {
   f, err := os.Open(filename)
   if err != nil {
      return nil, err
   }
   defer f.Close()
   return ReadAll(f)
}
```

### 使用 Defer 记录函数

defer 机制也常被用于记录何时进入和退出函数。

不要忘记 defer 语句后的圆括号，否则本该在进入时执行的操作会在退出时执行，而本该在退出时执行的，永远不会被执行。

```go
// 使用闭包跟踪函数运行时间
func trace(msg string) func() {
   start := time.Now()
   log.Printf("enter %s", msg)
   return func() {
      log.Printf("exit %s (%s)", msg,time.Since(start))
   }
}

func bigSlowOperation() {
   defer trace("bigSlowOperation")() // don't forget the
   // extra parentheses
   // ...lots of work…
   time.Sleep(10 * time.Second) // simulate slow
   // operation by sleeping
}
```

### 语言中关于异常的处理

虽然 Go 的 panic 机制类似于其他语言的异常，但 panic 的适用场景有一些不同。由于 panic 会引起程序的崩溃，因此 panic 一般用于严重错误，如程序内部的逻辑不一致.对于大部分漏洞，我们应该使用 Go 提供的错误机制，而不是 panic ，**尽量避免程序的崩溃**。

`regexp` 包提供了包装函数 `regexp.MustCompile` 检查输入的合法性。输入错误就立马 panic,函数名中的 **Must 前缀是一种针对此类函数输入错误的立马 panic**

```go
package regexp
func Compile(expr string) (*Regexp, error) { /* ... */ }
func MustCompile(expr string) *Regexp {
re, err := Compile(expr)
if err != nil {
panic(err)
}
return re
}
```

#### Runtime 输出堆栈信息

```go
func main() {
defer printStack()
f(3)
}
func printStack() {
var buf [4096]byte  // 字节切片储存信息
n := runtime.Stack(buf[:], false) // 写入
os.Stdout.Write(buf[:n]) // 写入到输出流
}
```

#### 使用 `panic()` 和 `recover()`

panic 函数用于引发恐慌，导致程序中断执行

recover 函数用于恢复程序的执行，`recover()` 语法上要求必须在 `defer` 中执行。

1. 如果多个 defer 函数：
2. defer 函数传递参数的时候：

如果在 deferred 函数中调用了内置函数 recover，并且定义该 defer 语句的函数发生了 panic 异常，recover 会使程序从 panic 中恢复，并返回 panic value。导致 panic 异常的函数**不会继续运行，但能正常返回**。在未发生 panic 时调用 recover，recover 会返回 nil。

```go
func Parse(input string) (s *Syntax, err error) {
defer func() {
if p := recover(); p != nil {
err = fmt.Errorf("internal error: %v", p)
}
}()
// ...parser...
    panic("错误") // 程序不会停止,会转到 defer recover 处理
}
```

安全的做法是有选择性的 recover 。换句话说，只恢复应该被恢复的 panic 异常.

可以将 panic value 设置成特殊类型。在 recover 时对 panic value 进行检查，如果发现 panic value 是特殊类型，就将这个 panic 作为 error 处理，如果不是，则按照正常的 panic 进行处理.

```go
func soleTitle(doc *html.Node) (title string, err error) {
type bailout struct{}
defer func() {
    // 通过 switch 语句 判断 panic 的类型
    // 进行有选择的 recover
switch p := recover(); p {
case nil: // no panic
case bailout{}: // "expected" panic
err = fmt.Errorf("multiple title elements")
default:
panic(p) // unexpected panic; carry on panicking
}
}()

panic(bailout{})
}

```

### 栈的结构 (先进后出!后进先出!)

![](http://oss.whaleluo.top/blog/Golang/func-25.png-picsmall)  
当一个函数有多个延迟调用时，它们被添加到一  
个堆栈中，并 Last In First Out(LIFO) **先进后出!后进先出!** 的顺序中执行。

![](http://oss.whaleluo.top/blog/Golang/func-26.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-27.png-picsmall)

### Defer 注意点

1. 当外围函数中的语句正常执行完毕时，只有其中所有的延迟函数都执行完毕，外围函数才会真正的结束执行。
2. 当执行外围函数中的 return 语句时，只有其中所有的延迟函数都执行完毕后，外围函数才会真正返回。
3. 当外围函数中的代码引发运行恐慌时，只有其中所有的延迟函数都执行完毕后，该运行时恐慌才会真正被扩展至调用函数。

## 函数的数据类型

在 Go 中，函数被看作第一类值 (一等公民)（first-class values）：函数像其他值一样，拥有类型，可以被赋值给其他变量，传递给函数，从函数返回。

`strings.Map` **对字符串中的每个字符** 调用 add1 函数，并将每个 add1 函数的返回值组成一个新的字符串返回给调用者。

```go
type rune = int32
func Map(mapping func(rune) rune, s string) string
```

```go
func add1(r rune) rune { return r + 1 }

fmt.Println(strings.Map(add1, "HAL-9000")) // "IBM.:111"
fmt.Println(strings.Map(add1, "VMS")) // "WNT"
fmt.Println(strings.Map(add1, "Admix")) // "Benjy"
```

函数的类型：  
`func(参数列表的数据类型)(返回值列表的数据类型)`

![](http://oss.whaleluo.top/blog/Golang/func-31.png-picsmall)

### 基本类型回顾

![](http://oss.whaleluo.top/blog/Golang/func-28.png-picsmall)

## 函数的本质

![](http://oss.whaleluo.top/blog/Golang/func-30.png-picsmall)

---

![](http://oss.whaleluo.top/blog/Golang/func-29.png-picsmall)

> " 说到底,函数也是一种特殊类型的变量."

![](http://oss.whaleluo.top/blog/Golang/func-32.png-picsmall)

## 匿名函数

拥有函数名的函数只能在包级语法块中被声明，通过函数字面量（function literal），，在任何表达式中表示一个函数值。函数值字面量是一种表达式，它的值被称为匿名函数  
（anonymous function）。

```go
strings.Map(func(r rune) rune { return r + 1 }, "HAL-9000")
```

通过这种方式定义的函数可以访问 **完整的词法环境**（lexical environment），这意味着在函数中定义的内部函数可以引用该函数的变量，如下例所示：

在 squares 中定义的匿名内部函数可以访问和更新 squares 中的局部变量，匿名函数和 squares 中，存在变量引用。

变量的生命周期不由它的作用域决定：squares 返回后，变量 x 仍然隐式的存在于 f 中。

Go 使用闭包（closures）技术实现函数值，Go 程序员也把函数值叫做闭包。

```go
// squares返回一个匿名函数。（闭包）
// 该匿名函数每次被调用时都会返回下一个数的平方。
func squares() func() int {
   var x int
   return func() int {
      x++
      return x * x
   }
}
func main() {
   // f是匿名函数，可以访问到 squares 保持的 x 局部变量
   f := squares() 
   fmt.Println(f()) // "1"
   fmt.Println(f()) // "4"
   fmt.Println(f()) // "9"
   fmt.Println(f()) // "16"
}
```

![](http://oss.whaleluo.top/blog/Golang/func-36.png-picsmall)

---

![](http://oss.whaleluo.top/blog/Golang/func-34.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-35.png-picsmall)

## 回调函数

![](http://oss.whaleluo.top/blog/Golang/func-41.png-picsmall)

---

![](http://oss.whaleluo.top/blog/Golang/func-37.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-38.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-39.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-40.png-picsmall)

## 闭包

### Go 语言支持函数式编程

函数被看作第一类值（first-class values）：函数像其他值一样，拥有类型，可以被赋值给其他变量，传递给函数，从函数返回。对函数值（function value）的调用类似函数调用。

函数类型的零值是 nil。调用值为 nil 的函数值会引起 panic 错误.

函数值之间是不可比较的，也不能用函数值作为 map 的 key。

支持将一个函数作为另一个函数的参数,也支持将一个函数作为另一个函数的返回值.

strings.Map **对字符串中的每个字符调用** add1 函数，并将每个 add1 函数的返回值组成一个新的字符串返回给调用者。

```go
func add1(r rune) rune { return r + 1 }
fmt.Println(strings.Map(add1, "HAL-9000")) // "IBM.:111"

fmt.Println(strings.Map(func(r rune) rune { return r + 1 }, "HAL-9000")) // 使用匿名函数的方式
```

### 闭包 (closure)

一个外层函数中,有内层函数,该内层函数中,会操作外层函数的 **局部变量** (外层函数中的参数，或者外层函数中直接定义的变量),并且该外层函数的返回值就是这个内层函数.

这个**内层函数和外层函数的局部变量**，统称为**闭包结构**。

局部变量的 **生命周期** 会发生改变，正常的局部变量随着函数调用而创建，随着函数的结束而销毁

但是闭包结构中的外层函数的局部变量并不会随着外层函数的结束销毁，因为内层函数还要**继续使用**。

---

![](http://oss.whaleluo.top/blog/Golang/func-43.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-42.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/func-44.png-picsmall)

## 捕获迭代变量

> **将介绍 Go 词法作用域的一个陷阱。请务必仔细的阅读，弄清楚发生问题的原因。即使是经验丰富的程序员也会在这个问题上犯错误。**

循环中的 d 函数值记录的是 **循环变量的内存地址** ，不是循环变量某一时刻的值。for 循环结束时，d 指向循环最后一次迭代的值。而需要一个局部变量 dir 将 d **进行深拷贝保存作为循环变量的副本**。

```go
var rmdirs []func()
for _, d := range tempDirs() {
   dir := d // NOTE: necessary!
   os.MkdirAll(dir, 0755) // creates parent directories too
   rmdirs = append(rmdirs, func() {
      os.RemoveAll(dir)
   })
}
// ...do some work…
for _, rmdir := range rmdirs {
 rmdir() // clean up
}
```

这个问题不仅存在基于 range 的循环，go 语句或者 defer 语句会经常遇到此类问题。这不是 go 或 defer 本身导致的，而是因为它们都会 **等待循环结束后** ，再执行函数值。
