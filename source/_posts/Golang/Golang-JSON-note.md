---

title: Golang 学习笔记——JSON的解析
date: 2022-12-17 12:17:48
categories: Golang
updated: 2022-12-17 12:17:48
tags: [Golang, Coding]
description:
thumbnail: http://oss.whaleluo.top/blog/Golang/icon_img.png-picsmall
banner_img: http://oss.whaleluo.top/blog/Golang/icon_img.png-picsmall

---

# Golang 解析 JSON 数据

`JSON` 是对 `JavaScript` 中各种类型的值——字符串、数字、布尔值和对象—— Unicode 本文编码。它可以用有效可读的方式表示基础数据类型和数组、slice、结构体和 map 等聚合数据类型。

## JSON 序列化（编码）

将结构体 slice 转为 JSON 的过程叫编组（marshaling）。编组通过调用 `json.Marshal` 函数完成：

```go
type Movie struct {
    Title string
    Year int `json:"released"`
    Color bool `json:"color,omitempty"`
    Actors []string
}
data, err := json.Marshal(movies)
if err != nil {
    log.Fatalf("JSON marshaling failed: %s", err)
}
fmt.Printf("%s\n", data)
// [{"Title":"Casablanca","released":1942,"Actors":["Humphrey Bogart","Ingrid Bergman"]}]
```

> 在结构体声明中，Year 和 Color 成员后面的字符串面值是结构体成员 Tag

`json.MarshalIndent` 函数将产生整齐缩进的输出。该函数有两个额外的字符串参数用于表示每一行输出的前缀和每一个层级的缩进：

```go
data, err := json.MarshalIndent(movies, "", " ")
if err != nil {
    log.Fatalf("JSON marshaling failed: %s", err)
}
fmt.Printf("%s\n", data)
```

## JSON 反序列化（解码）

编码的逆操作是解码，对应将 JSON 数据解码为 Go 语言的数据结构，Go 语言中一般叫 unmarshaling，通过 `json.Unmarshal` 函数完成。

```go
func Unmarshal(data []byte, v interface{}) error{...}
```

结构体中只有 Title 成员。通过定义合适的 Go 语言数据结构，我们可以 **选择性** 地解码 JSON 中感兴趣的成员。

```go
var titles []struct{ Title string }  // 匿名结构体
if err := json.Unmarshal(data, &titles); err != nil {
    log.Fatalf("JSON unmarshaling failed: %s", err)
}
fmt.Println(titles) // "[{Casablanca} {Cool Hand Luke} {Bullitt}]"
```

将定义的结构体传入函数时，需要 **传入结构体的地址（指针）** 。因为在函数内部默认是深拷贝数据，写入结构体时需要映射原有的结构体。

## Struct 结构体成员 Tag

Go 语言默认将结构体的成员名字作为 JSON 的对象（通过 reflect 反射技术）。**只有导出的结构体成员才会被编码，要用大写字母开头的成员名称。**

有些 JSON 成员名字和 Go 结构体成员名字并不相同，因此需要 Go 语言结构体成员 Tag 来指定对应的 JSON 名字。

```go
Year int `json:"released"`
Color bool `json:"color,omitempty"`
// 定义的 tag 解析到（序列化） JSON
// {released:"Year_value",color:"Color_value"}
```

Color 成员的 Tag 还带了一个额外的 omitempty 选项，表示当 Go 语言结构体成员为空或零值时不生成 JSON 对象。

## 流式 JSON 解码器

基于流式的解码器 `json.Decoder`，它可以从一个输入流解码 JSON 数据，尽管这不是必须的。如您所料，还有一个针对输出流的 `json.Encoder` 编码对象。

![流式 JSON 解码器](http://oss.whaleluo.top//blog/Golang/json-1.png-picsmall)

```go
if err := json.NewDecoder(resp.Body).Decode(&result); err != nil {
    resp.Body.Close()
    return nil, err
}
```

调用 Decode 方法传入结构体指针来填充变量。
