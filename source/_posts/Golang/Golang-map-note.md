---

title: Golang 学习笔记——map映射数据结构
date: 2022-05-1 18:30:48
categories: Golang
updated: 2022-05-1 18:30:48
tags: [Golang, Coding]
description:
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/icon_img.png&webp=true

---

# Go Map 数据类型

map: 映射/哈希表，是一种转码用于存储键值对的结合，属于引用类型。

## 定义 Map 对象

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-1.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/20220424210757.png&webp=true)

## 存储值获取值

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-3.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-4.png&webp=true)

## Map 的遍历

> 使用 `sort` 包进行排序.

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-6.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-7.png&webp=true)

## Map 和 Slice 结合使用

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-8.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-9.png&webp=true)

原理图:  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/mapWithSlice.png&webp=true)

## Map 是引用类型

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-12.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-10.png&webp=true)  
[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-11.png&webp=true)

## 总结

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/Golang/map-5.png&webp=true)
