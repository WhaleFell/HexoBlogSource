---

title: Golang 学习笔记——文本和HTML模板
date: 2022-12-17 15:17:48
categories: Golang
updated: 2022-12-17 15:17:48
tags: [Golang, Coding]
description:
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true

---

# GO 文本和 HTML 模板

前面的例子，只是最简单的格式化，使用 Printf 是完全足够的。但是有时候会需要复杂的打印格式，这时候一般需要将格式化代码分离出来以便更安全地修改。这写功能是由 text/template 和 html/template 等模板包提供的，它们提供了一个将变量值填充到一个文本或 HTML 格式的模板的机制。

一个模板是一个字符串或一个文件，里面包含了一个或多个由双花括号包含的 `{{action}}` 对象。

通过 \`\` 定义字符串字面量。

```go
const templ = `{{.TotalCount}} issues:{{range .Items}}----------------------------------------Number: {{.Number}}User: {{.User.Login}}Title: {{.Title | printf "%.64s"}}Age: {{.CreatedAt | daysAgo}} days{{end}}`
```

对于每一个 action，都有一个当前值的概念，对应点操作符，写作“.”,模板中 `{{.TotalCount}}` 对应 action 将展开为结构体中 TotalCount 成员

模板中 `{{range .Items}}` 和 `{{end}}` 对应一个循环 action ，因此它们直接的内容可能会被展开多次，循环每次迭代的当前值对应当前的 Items 元素的值。

在一个 action 中， | 操作符表示将前一个表达式的结果作为后一个函数的输入，类似于 UNIX 中管道的概念。

## 生成模板

```go
// 先解析模板
report, err := template.New("report").Funcs(template.FuncMap{"daysAgo": daysAgo}).Parse(templ)
if err != nil {
    log.Fatal(err)
}
// 在执行模板，使用 result 作为输入源，os.Stdout 作为输出源。
if err := report.Execute(os.Stdout, result); err != nil {
    log.Fatal(err)
}
```

**方法调用链** ：template.New 先创建并返回一个模板；Funcs 方法将 daysAgo 等自定义函数注册到模板中，并返回模板；最后调用 Parse 函数分析模板。

```go
func Must(t *Template, err error) *Template {....}
var report = template.Must(report)
```

因为模板通常在编译时就测试好了，如果模板解析失败将是一个致命的错误。template.Must 辅助函数可以简化这个致命错误的处理：它接受一个模板和一个 error 类型的参数，检测 error 是否为 nil（如果不是 nil 则发出 panic 异常），然后返回传入的模板。

## html/template

使用和 text/template 包相同的 API 和模板语言，但是增加了一个将 **字符串自动转义特性**，这可以避免输入字符串和 HTML JavaScript、CSS 或 URL 语法产生冲突的问题。

```go
func main() {
    const templ = `<p>A: {{.A}}</p><p>B: {{.B}}</p>`
    t := template.Must(template.New("escape").Parse(templ))
    var data struct {
        A string // untrusted plain text
        B template.HTML // trusted HTML
    }
    data.A = "<b>Hello!</b>"
    data.B = "<b>Hello!</b>"
    if err := t.Execute(os.Stdout, data); err != nil {
        log.Fatal(err)
    }
}
```
