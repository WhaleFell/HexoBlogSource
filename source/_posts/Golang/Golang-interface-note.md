---

title: Golang 学习笔记——Go 接口
date: 2022-05-16 18:30:48
updated: 2022-05-16 18:30:48
categories: Golang
tags: [Golang, Coding]
description:
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true

---

# Go 接口

**面向对象** 世界中的接口的一般定义是 "**接口定义对象的行为**"。它表示让指定对象应该做什么。实现这种行为的方法（实现细节）是**针对对象**的。

在 `Go` 中，接口是 **一组方法签名**。当类型为接口中的所有方法提供定义时，它被称为 **实现接口**。它与 OOP 非常相似。接口指定了类型 **应该具有** 的方法，类型决定了 **如何实现** 这些方法。

> 它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口
>
> 接口定义了一组方法，如果某个对象实现了某个接口的所有方法，则此对象就实现了该接口。

## 图示

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-1.png&webp=true)

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

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-2.png&webp=true)  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-4.png&webp=true)  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-3.png&webp=true)

> Go 语言中接口和类型的实现方式是 **非侵入式** 的,接口定义的方法没有具体代码.

## 通过接口模拟多态

### 多态的定义

" 一个事物的多种形态 "

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-5.png&webp=true)

就一个接口的实现:

1. 看成 **实现本身的类型**，能够访问 **实现类中的属性和方法**.
2. 看成 **对应的接口类型**，那就 **只能够访问接口中的方法**.

接口的用法：

1. 一个函数如果接受 **接口类型作为参数**，那么实际上 **可以传入该接口的任意实现类** 对象作为参数。
2. 定义一个类型为 **接口类型**，实际上可以 **赋值为任意实现类的对象**.

鸭子类型:

> 待补充…

## 空接口

`interface{}` 不包含任何的方法,所以**任何类型都是空接口的实现类**,因此空接口可以储存任何类型的数值.  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-7.png&webp=true)

### 空接口定义任意类型的数据

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-6.png&webp=true)

### 空接口作为函数的参数

表示函数的参数可以是任意类型,相当于 Python 的 `typing.Any`

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-8.png&webp=true)  
`fmt` 包就是应用了空接口,可以传入任何参数.  
`fmt` 包下的 `Print` 系列函数:

```go
func Print(a ...interface{}) (n int,err error)
func Printf(format string,a ...interface{}) (n int,err error)
func Println(a ...interface{}) (n int,err error)
```

### 复合数据结构使用空接口

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-9.png&webp=true)  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-10.png&webp=true)

## 接口嵌套

接口允许多继承.

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-11.png&webp=true)

如果 Cat 想实现接口 C,不仅要实现接口 C 自己的方法,还要实现接口 C 继承的接口 A B 中的方法.

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-12.png&webp=true)  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-13.png&webp=true)  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/insterface-14.png&webp=true)

## 接口断言

前面说过，因为空接口 `interface` 没有定义任何函数，因此 `Go` 中 **所有类型都实现了空接口**。当一个函数的形参是 `interface{}` ，那么在函数中，需要对形参 **进行断言**，从而 **得到它的真实类型**。

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-15.png&webp=true)

方法一: 通过 `x.(T)` 的方式断言

```go
var w io.Writer
w = os.Stdout
f := w.(*os.File)      // success: f == os.Stdout
c := w.(*bytes.Buffer) // panic: interface holds *os.File, not *bytes.Buffer
```

**如果断言的类型 T 是一个接口类型**，然后类型断言检查是否 x 的动态类型满足 T。如果这个检查成功了，动态值没有获取到；这个结果仍然是一个有相同动态类型（type）和值部分（value）的接口值，但是结果为类型 T。

用于判断 `x.(T)` x 是否满足 T 接口。

```go
var w io.Writer // io.Writer 接口
w = os.Stdout // os.Stdout接口 实现了 io.Writer 接口
rw := w.(io.ReadWriter) // success: *os.File has both Read and Write
w = new(ByteCounter)
rw = w.(io.ReadWriter) // panic: *ByteCounter has no Read method
```

如果一个类型满足下面的这个接口，然后 `WriteString(s)` 方法就必须和 `Write([]byte(s))` 有相同的效果。

Write 方法需要传入一个 byte 切片而我们希望写入的值是一个字符串，所以我们需要使用 `[]byte(…)` 进行转换。**这个转换分配内存并且做一个拷贝**，但是这个拷贝在转换后几乎立马就被丢弃掉，会影响一丢丢性能。

我们知道在这个程序中的 w 变量持有的动态类型也有一个**允许字符串高效写入**的 `WriteString` 方法；这个方法会避免去分配一个临时的拷贝。

许多满足 io.Writer 接口的重要类型同时也有 WriteString 方法，包括 `*bytes.Buffer`，`*os.File` 和 `*bufio.Writer`。

我们不能对任意实现 `io.Writer` 接口类型的变量 w，假设它也拥有 `WriteString` 方法。但是我们可以定义一个只有这个方法的新接口并且使用类型断言来检测是否 w 的动态类型满足这个新接口。

```go
func writeString(w io.Writer, s string) (n int, err error) {
    // 定义一个实现了 WriteString 方法的接口
    type stringWriter interface {
        WriteString(string) (n int, err error)
    }
    // 检查 w 是否实现了 WriteString 方法
    if sw, ok := w.(stringWriter); ok {
        // 实现了就调用这个方法
        return sw.WriteString(s) // avoid a copy
    }
    // 没有实现 WriteString 方法就回退到使用 w.Write 方法
    return w.Write([]byte(s)) // allocate temporary copy
}
```

它太有用了以致于标准库将它作为 `io.WriteString` 函数提供。**这是向一个 io.Writer 接口写入字符串的推荐方法。**

上面的 writeString 函数使用一个**类型断言来获知一个普遍接口类型的值是否满足一个更加具体的接口类型**；并且如果满足，它会使用这个更具体接口的行为。

**如果断言操作的对象是一个 nil 接口值**，那么不论被断言的类型是什么这个类型断言都会失败。

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-16.png&webp=true)

```go
var w io.Writer = os.Stdout
f, ok := w.(*os.File)      // success:  ok, f == os.Stdout
b, ok := w.(*bytes.Buffer) // failure: !ok, b == nil

// 更简洁的结构
if f, ok := w.(*os.File); ok {
    // ...use f...
}
```

当类型断言的操作对象是一个变量时，if 内层其实是声明了一个同名的新的本地变量，外层原来的 w 不会被改变。

```go
if w, ok := w.(*os.File); ok {
    // ...use w...
}
```

方法二：使用 `switch…case…` 语句，断言类型分支。

一个类型分支隐式的创建了一个词法块，因此新变量 x 的定义不会和外面块中的 x 变量冲突。每一个 case 也会隐式的创建一个单独的词法块。

```go
switch x := x.(type) {
    case nil:       // ...
    case int, uint: // ...
    case bool:      // ...
    case string:    // ...
    default:        // ...
}
```

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/interface-17.png&webp=true)

## 接口值

接口值由两部分组成：一个具体的类型（type）和这个类型的值（value）。**接口是动态类型。**一个接口值可以持有任意大的动态值。实现了这个接口的类型都可以赋值这个接口。

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/20221229121130.png&webp=true)

定义一个 nil 接口：

```go
var w io.Writer // 空接口
w.Writer([]byte("hello")) // panic: nil pointer
// 可以通过 w == nil 判断接口值是否为空
```

将 `*os.File` 类型赋值给变量 w，因为 `*os.File` 实现了 `io.Writer` 接口，所以赋值合法。赋值过程调用了一个 **具体类型到接口类型** 的隐式转换（变成 T 类型 `T(type)` ） `io.Writer(os.Stdout)`

```go
w = os.Stdout
// 隐式转换 
io.Writer(os.Stdout)
```

接口的 value 持有 `os.Stdout` 的拷贝，这是一个代表处理标准输出的 `os.File` 类型的指针。

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/20221229122508.png&webp=true)

调用一个包含 `*os.File` 类型指针的接口值的 `Write` 方法，得 `(*os.File).Write` 方法被调用。这个调用输出 “hello”。

```go
w.Write([]byte("hello")) // "hello"
(*os.File).Write // 等效
```

当我们处理错误或者调试的过程中，得知接口值的动态类型是非常有帮助的。所以我们使用 `fmt` 包的 `%T` 动作，在 fmt 包内部，使用反射来获取接口动态类型的名称。

```go
var w io.Writer
fmt.Printf("%T\n", w) // "<nil>"
w = os.Stdout
fmt.Printf("%T\n", w) // "*os.File"
w = new(bytes.Buffer)
fmt.Printf("%T\n", w) // "*bytes.Buffer"
```

### ⚠️一个包含 Nil 指针的接口不是 Nil 接口

一个不包含任何值的 nil 接口值：

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/20221229121130.png&webp=true)

一个刚好包含 nil 指针的接口值：

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/20221229123705.png&webp=true)

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

### `sort.Interface` 接口

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

<u>*net/http*</u>

```go
package http
type Handler interface { 
    ServeHTTP(w ResponseWriter, r*Request)
} 
func ListenAndServe(address string, h Handler) error
```

`ListenAndServe` 函数需要一个例如“localhost:8000”的服务器地址，和一个所有请求都可以分派的 Handler 接口实例。它会一直运行，直到这个服务因为一个错误而失败（或者启动失败），它的返回值一定是一个非空的错误。

使用不同的 URL 触发不同的行为

```go
func (db database) ServeHTTP(w http.ResponseWriter, req *http.Request) {
switch req.URL.Path {
case "/list":
    for item, price := range db {
    fmt.Fprintf(w, "%s: %s\n", item, price)
    }
case "/price":
    // Quary 方法可以将 http 请求参数解析为一个 map
    item := req.URL.Query().Get("item")
    price, ok := db[item]
    if !ok {
    w.WriteHeader(http.StatusNotFound) // 404
    fmt.Fprintf(w, "no such item: %q\n", item)
    return
    }
    fmt.Fprintf(w, "%s\n", price)
default:
    w.WriteHeader(http.StatusNotFound) // 404
    fmt.Fprintf(w, "no such page: %s\n", req.URL)
}
}
```

现在 handler 基于 URL 的路径部分（`req.URL.Path`）来决定执行什么逻辑。如果不能识别，调用 `w.WriteHeader(http.StatusNotFound)` 返回客户端一个 HTTP 404 错误。

`http.ResponseWriter` 是另一个接口。它在 `io.Writer` 上增加了发送 HTTP 相应头的方法。另外，还可以使用 `http.Error` 函数。

```go
http.Error(w, msg, http.StatusNotFound)
```

net/http 包提供了一个请求多路器 `ServeMux` 来简化 URL 和 handlers 的联系（路由映射）。一个 ServeMux 将一批 `http.Handler` 聚集到一个单一 `http.Handler` 中。

```go
func main() {
    mux := http.NewServeMux()
    mux.Handle("/list", http.HandlerFunc(db.list))
    mux.Handle("/price", http.HandlerFunc(db.price))
    log.Fatal(http.ListenAndServe("localhost:8000", mux))
}
```

所以 `db.list` 是一个实现了 handler 类似行为的函数，但是因为它没有方法，所以它不满足 `http.Handler` 接口并且不能直接传给 `mux.Handle`。

语句 `http.HandlerFunc(db.list)` 是一个转换而非一个函数调用，因为 `http.HandlerFunc` 是一个类型。它有如下的定义：

```go
package http
type HandlerFunc func(w ResponseWriter, r *Request)
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
    f(w, r)
}
```

因为 handler 通过这种方式注册非常普遍，ServeMux 有一个方便的 HandleFunc 方法，它帮我们简化 handler 注册代码成这样：

```go
mux.HandleFunc("/list", db.list)
mux.HandleFunc("/price", db.price)
```

为了方便，net/http 包提供了一个全局的 ServeMux 实 DefaultServerMux 和包级别的 `http.Handle` 和 `http.HandleFunc` 函数。现在，为了使用 DefaultServeMux 作为服务器的主 handler，我们不需要将它传给 ListenAndServe 函数；nil 值就可以工作。

使用包级别的 `http.HandleFunc` 注册路由：

```go
func main() {
    db := database{"shoes": 50, "socks": 5}
    http.HandleFunc("/list", db.list)
    http.HandleFunc("/price", db.price)
    log.Fatal(http.ListenAndServe("localhost:8000", nil))
}
```

最后，web 服务器在一个新的协程 Goruntine 中调用每一个 handler，所以当 handler 获取其它协程的共享变量时一定要使用预防措施比如 **锁机制**。

### Error 接口

```go
type error interface {
    Error() string
}
```

创建一个 error 最简单的方法就是 `error.New` 函数，其实整个 error 包只有 4 行：

```go
package errors
func New(text string) error { return &errorString{text} }
type errorString struct { text string }
func (e *errorString) Error() string { return e.text }
```

指针类型 `*errorString` 满足 error 接口而非 `errorString` 类型，承载 `errorString` 的类型是一个结构体而非一个字符串，所以每个 New 函数的调用都分配了一个独一无二的错误示例。

```go
fmt.Println(errors.New("EOF") == errors.New("EOF")) // "false"
```

有一个方便的封装函数 `fmt.Errorf`，允许对错误信息进行字符串格式化 ：

```go
package fmt
import "errors"
func Errorf(format string, args ...interface{}) error {
    return errors.New(Sprintf(format, args...))
}
```

[详解 Go 语言的 rune 类型](https://www.cnblogs.com/cheyunhua/p/16007219.html)

## 建议

当设计一个新的包时，小白 Gopher 总是先创建一套接口，然后再定义一些满足它们的具体类型。这种方式的结果就是有很多的接口，它们中的每一个仅只有一个实现。**接口只有当有两个或两个以上的具体类型必须以相同的方式进行处理时才需要**。
