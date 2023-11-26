---

title: Golang 学习笔记——map映射数据结构
date: 2022-05-1 18:30:48
categories: Golang
updated: 2022-05-1 18:30:48
tags: [Golang, Coding]
description:
thumbnail: http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/icon_img.png
banner_img: http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/icon_img.png

---

# Go Map 数据类型

map: 映射/哈希表，是一种转码用于存储键值对的结合，属于引用类型。

## 定义 Map 对象

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-1.png)  
![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/20220424210757.png)

## 存储值获取值

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-3.png)  
![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-4.png)

## Map 的遍历

> 使用 `sort` 包进行排序.

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-6.png)  
![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-7.png)

## Map 和 Slice 结合使用

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-8.png)  
![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-9.png)

原理图:  
![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/mapWithSlice.png)

## Map 是引用类型

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-12.png)  
![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-10.png)  
![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-11.png)

## 总结

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/Golang/map-5.png)
