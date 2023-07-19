---

title: Golang 学习笔记——map映射数据结构
date: 2022-05-1 18:30:48
categories: Golang
updated: 2022-05-1 18:30:48
tags: [Golang, Coding]
description:
thumbnail: http://oss.whaleluo.top/blog/Golang/icon_img.png-picsmall
banner_img: http://oss.whaleluo.top/blog/Golang/icon_img.png-picsmall

---

# Go Map 数据类型

map: 映射/哈希表，是一种转码用于存储键值对的结合，属于引用类型。

## 定义 Map 对象

![](http://oss.whaleluo.top/blog/Golang/map-1.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/20220424210757.png-picsmall)

## 存储值获取值

![](http://oss.whaleluo.top/blog/Golang/map-3.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/map-4.png-picsmall)

## Map 的遍历

> 使用 `sort` 包进行排序.

![](http://oss.whaleluo.top/blog/Golang/map-6.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/map-7.png-picsmall)

## Map 和 Slice 结合使用

![](http://oss.whaleluo.top/blog/Golang/map-8.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/map-9.png-picsmall)

原理图:  
![](http://oss.whaleluo.top/blog/Golang/mapWithSlice.png-picsmall)

## Map 是引用类型

![](http://oss.whaleluo.top/blog/Golang/map-12.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/map-10.png-picsmall)  
![](http://oss.whaleluo.top/blog/Golang/map-11.png-picsmall)

## 总结

![](http://oss.whaleluo.top/blog/Golang/map-5.png-picsmall)
