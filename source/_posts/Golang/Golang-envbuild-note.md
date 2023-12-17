---

title: Golang 学习笔记——Go环境搭建
date: 2022-04-19 18:30:48
updated: 2022-04-19 18:30:48
categories: Golang
tags: [Golang, Coding]
description:
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png

---

# Go 环境搭建

1. 安装完 Go,添加 `bin` 目录到环境变量.
2. CMD 配置 go 环境

```shell
go version # 输出版本
go env # 显示 go 环境变量
set GO111MODULE=on  # 包管理
set GOPROXY=http://goproxy.cn  # 设置镜像源
```

1. Vscode 安装 `Go` `Run code` 插件

## Hello Go

```go
package main

import "fmt"

func main() {
 fmt.Println("hello go")
}

# 运行
go run helloworld.go
# 编译为可执行文件
go build hellworld.go
# 下载包
go mod init go_pro  # 新建依赖
go get XXX 或 go get install 源码包
go list # 列出包
go tool # 输出go工具
```
