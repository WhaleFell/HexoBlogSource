---
title: CSS 樣式入門
date: 2024-03-03 16:35:83
updated: 2024-03-03 16:35:83
categories:
  - JavaScript
tags:
  - Design
  - 前端
  - CSS
description: 
thumbnail: 
banner_img:
---

# CSS 樣式入門

## Start

学了前端那么久，发现 CSS 真是一个 **博大精深** 的语言，可以简单的实现很多 JS 做不到的效果。故出此专栏，记录一些常用、神奇的 CSS。

Reference：

1. CSS online [CSS Playground](https://playcode.io/css)
2. MDN CSS Tutorial [CSS 入门概述 - 学习 Web 开发 | MDN](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/First_steps)

## Selector 选择器

```css
h1 // 选择 h1 标签
a[title] // 选择有 title 属性的标签
a:hover // 伪类，鼠标悬浮时触发
.header // 选择 class=header
#myid // 选择 id=myid
* // 全局
.box p // 选择 box class 下的所有 p 标签
.box p:first-child // 选择 box class 下的第一个 p 标签
h1, h2, .intro // , 表示分别设置

```

选择器 **层叠**（cascade）和 **优先级**（specificity）

类选择器具有高优先级

## Function 函数

一个例子是 `calc()` 函数。这个函数允许在 CSS 中进行简单的计算：

```css
.outer {
  border: 5px solid black;
}

.box {
  padding: 10px;
  width: calc(90% - 30px);
  background-color: rebeccapurple;
  color: white;
}
```

另一个例子是 [`transform`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform) （变换角度）的不同取值，如 `rotate()`。

```css
.box {
  margin: 30px;
  width: 100px;
  height: 100px;
  background-color: rebeccapurple;
  transform: rotate(0.8turn);
}

.box {
  /* 圆角 */
  border-radius: 10px;
}

```

## @ Rule @ 规则

CSS 的 [`@rules`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/At-rule)（读作“at-rules”）是一些特殊的规则，提供了关于 CSS 应该执行什么或如何表现的指令。有些 @ 规则很简单，只有一个关键词和一个值。例如，`@import` 将一个样式表导入另一个 CSS 样式表：

一个常见的@规则是 `@media`，它被用来创建 [媒体查询](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_media_queries)。媒体查询使用条件逻辑来应用 CSS 样式。

```css
body {
  background-color: pink;
}

@media (min-width: 30em) {
  body {
    background-color: blue;
  }
}
```

## 简写属性

一些属性，如 [`font`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/font)、[`background`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background)、[`padding`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding)、[`border`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border) 和 [`margin`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin) 等属性称为**简写属性**。它们允许在一行中设置多个属性值，从而节省时间并使代码更整洁。

`padding`：**设置内边距**  
`margin`: **设置外边距**

```css
/* 在像 padding 和 margin 这样的 4 值简写语法中，
   数值的应用顺序是上、右、下、左（从顶部顺时针方向）。
   也有其他的简写类型，例如 2 值简写，
   它为顶部/底部设置 padding/margin，然后是左侧/右侧 */
padding: 10px 15px 15px 5px;

/* same as */
padding-top: 10px;
padding-right: 15px;
padding-bottom: 15px;
padding-left: 5px;
```

同样的还有设置 background：

```css
background: red url(bg-graphic.png) 10px 10px repeat-x fixed;

/* same as */
background-color: red;
background-image: url(bg-graphic.png);
background-position: 10px 10px;
/* repeat 控制背景平铺行为 */
background-repeat: repeat-x;
background-attachment: fixed;
```

## Box 盒子模型

**在 CSS 中，所有的元素都被一个个的“盒子”包围着。**

几种类型的盒子，一般分为 **区块盒子**（block boxes）和 **行内盒子**（inline boxes）。

类型指的是盒子在页面流中的行为方式以及与页面上其他盒子的关系。盒子有**内部显示**（inner display type）和 **外部显示**（outer display type）两种类型。

`block` **区块盒子** 类型：**独占一行会换行**；高度宽度都起作用；如果不指定宽度，占满一整行。

 `inline` **行内盒子**：**盒子不会换行**；高度宽度都起**不作用**

盒子模型的各个部分：

- **内容盒子**：显示内容的区域；使用 [`inline-size`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/inline-size) 和 [`block-size`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/block-size) 或 [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width) 和 [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height) 等属性确定其大小。
- **内边距盒子**：填充位于内容周围的空白处；使用 [`padding`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding) 和相关属性确定其大小。
- **边框盒子**：边框盒子包住内容和任何填充；使用 [`border`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border) 和相关属性确定其大小。
- **外边距盒子**：外边距是最外层，其包裹内容、内边距和边框，作为该盒子与其他元素之间的空白；使用 [`margin`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin) 和相关属性确定其大小。

![](Senior-CSS.png)


```css
body {
  background: #6e28d9;
  padding: 0 24px;
  color: white; /* Change my color to yellow */
  margin: 0;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
```