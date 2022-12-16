---
title: Golang 学习笔记——map映射数据结构
date: 2022-05-1 18:30:48
categories: Golang
updated: 2022-05-1 18:30:48
tags: [Golang, Coding]
description: 
index_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png
banner_img: http://pic.lskyl.xyz/blog/Golang/icon_img.png
---

# Go map 数据类型  

map: 映射/哈希表，是一种转码用于存储键值对的结合，属于引用类型。

## 定义 Map 对象

![](http://pic.lskyl.xyz/blog/Golang/map-1.png)  
![](http://pic.lskyl.xyz/blog/Golang/20220424210757.png)  

## 存储值获取值

![](http://pic.lskyl.xyz/blog/Golang/map-3.png)
![](http://pic.lskyl.xyz/blog/Golang/map-4.png)  

## Map 的遍历
> 使用 `sort` 包进行排序.  

![](http://pic.lskyl.xyz/blog/Golang/map-6.png)  
![](http://pic.lskyl.xyz/blog/Golang/map-7.png)  

## Map 和 Slice 结合使用

![](http://pic.lskyl.xyz/blog/Golang/map-8.png)  
![](http://pic.lskyl.xyz/blog/Golang/map-9.png)  

原理图:  
![](http://pic.lskyl.xyz/blog/Golang/mapWithSlice.png)  

## Map 是引用类型

![](http://pic.lskyl.xyz/blog/Golang/map-12.png)  
![](http://pic.lskyl.xyz/blog/Golang/map-10.png)  
![](http://pic.lskyl.xyz/blog/Golang/map-11.png)  

## 总结

![](http://pic.lskyl.xyz/blog/Golang/map-5.png)  