---
title: LightRoom 后期调色学习笔记
date: 2024-02-13 21:18:86
updated: 2024-02-13 21:18:86
categories:
  - Design
tags:
  - Design
  - 设计
  - 后期
description:
thumbnail:
banner_img:
---

# LightRoom 后期调色学习笔记

## Start

最近闲来无事，沉浸于光影的美伦变幻，所以系统学一下后期调色、对比度、饱和度、色相等知识。LightRoom 是一个专业的调色软件，和 PS 配合使用并支持 **RAW** 模式。

RAW 模式是相机把 **CMOS 镜头感应光** 的初始数据都保存在里面了，方便后期调色。

Reference：

1. B 站的一个介绍**影视后期** UP 主 **Genji 真想教会你**，通过 90 分钟的视频给你介绍各种后期软件如何使用，真是太方面想要速成的小白白了： [拜托三连了！这绝对是全B站最用心（没有之一）的Lr公开课程，调光调色就靠它！](https://www.bilibili.com/video/BV1g64y1474s/)

## 专业术语

1. LUT (LookupTable)：颜色查找表，色卡，一个算法，让每个画面中的像素完成在色彩空间上的向量变化。通俗点就是 **滤镜**。
2. RGB（红绿蓝）：xyz 轴标注着红色、绿色、蓝色的空间坐标。

## 光色理论

**红橙黄绿青蓝紫**

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/LightRoom-Tutorial.png&webp=true)

**三相色**色盘

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/LightRoom-Tutorial-1.png&webp=true)

如果想要画面更蓝，就**减少黄色**，或者**增加**与蓝色相邻的过渡色 **品红** 和 **泛蓝**

### CMYK 显色原理 通透？

上面比下面的图片 **对比度** 更高，更 **通透**。

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/LightRoom-Tutorial-2.png&webp=true)
