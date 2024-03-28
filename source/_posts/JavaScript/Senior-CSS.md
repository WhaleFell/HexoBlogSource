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
3. Tailwind CSS Chinese [Tailwind CSS w3cschool](https://www.w3cschool.cn/tailwind_css/)
4. 后盾人文档,是一个介绍前端技术栈的博主 [后盾人文档](https://doc.houdunren.com/)
5. CSS Video Tutorial 通过简明的视频动画阐述 CSS 各种概念 [BV1Rv4y177rj](https://www.bilibili.com/video/BV1Rv4y177rj/)

## Selector 选择器

```css
h1 // 选择 h1 标签
a[title] // 选择有 title 属性的标签
a:hover // 伪类(描述元素在某特性状态下的样式)，鼠标悬浮时触发
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

一个例子是  `calc()`  函数。这个函数允许在 CSS 中进行简单的计算：

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

另一个例子是  [`transform`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform) （变换角度）的不同取值，如  `rotate()`。

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

CSS 的  [`@rules`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/At-rule)（读作“at-rules”）是一些特殊的规则，提供了关于 CSS 应该执行什么或如何表现的指令。有些 @ 规则很简单，只有一个关键词和一个值。例如，`@import`  将一个样式表导入另一个 CSS 样式表：

一个常见的@规则是  `@media`，它被用来创建 [媒体查询](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_media_queries)。媒体查询使用条件逻辑来应用 CSS 样式。

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

一些属性，如  `font`、`background`、`padding`、`border`  和 `margin`  等属性称为 **简写属性**。它们允许在一行中设置多个属性值，从而节省时间并使代码更整洁。

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

1. **内容盒子**：显示内容的区域；使用  `inline-size`  和  `block-size`或  `width` 和 `height` 等属性确定其大小。
2. **内边距盒子**：填充位于内容周围的空白处；使用 `padding`  和相关属性确定其大小。
3. **边框盒子**：边框盒子包住内容和任何填充；使用`border`  和相关属性确定其大小。
4. **外边距盒子**：外边距是最外层，其包裹内容、内边距和边框，作为该盒子与其他元素之间的空白；使用 `margin`和相关属性确定其大小。

![](Senior-CSS.png)

```css
body {
  background: #6e28d9;
  padding: 0 24px; /* 内边距 **/
  color: white; /* text color */
  margin: 0; /* 外边距 **/
  height: 100vh; /* 高度占满 **/
  display: flex; /* 弹性布局 **/
  justify-content: center; /* 内容对齐方式 居中 **/
  align-items: center; /* 元素组对其方式 居中 **/
  border-radius: 50%;  /* 设置圆角 **/
}
```

## flexbox 弹性布局

建立一个 container div，设置 `display: flex`，div 内子元素会 **自动排列**，无需手动布局，可以自适应屏幕大小。尤其是在子元素个数和容器尺寸不确定的情况下，非常方便。

容器属性：

1. `flex-direction`：决定主轴的方向（即子项目的排列方向）。 `raw` 水平 `column` 垂直
2. `justify-content`：定义了子项目在 **主轴** 上的对齐方式 `center`。 `justify` vt. 证明…有理；为…辩护 这里指的是 v.对齐
3. `align-items`：定义子项目在 **交叉轴** 上如何对齐。 `flex-start` 默认，交叉轴前对齐 `center` 交叉轴居中对齐 `baseline` 文字基线对齐 `stretch` 拉伸
4. `flex-wrap`：定义了子项目在 **超出主轴** 换行时的行为。 `nowrap` 不换行 `wrap` 换行 `wrap-reverse` 反向换行
5. `align-content`：定义了多行项目在交叉轴上的对齐方式，只对 **多行项目** 有效。

项目属性：

1. `order`：定义项目的排列顺序。数值越小，排列越靠前，默认为 0。
2. `flex-grow`：定义项目的放大比例，默认为 0，即如果存在剩余空间，也不放大。
3. `flex-shrink`：定义了项目的缩小比例，默认为 1，即如果空间不足，该项目将缩小。
4. `flex-basis`：定义了在分配多余空间之前，项目占据的主轴空间（main size）。

## 响应式设计

1. 媒体查询
2. Flexbox 弹性布局
3. Grid 网格布局
4. 相对单位 rem、em、vw、vh

-   em：相对于父元素的字体大小
-   rem：相对于根元素的字体大小

## CSS 动画

CSS 动画也叫 **补间动画**（Tweening Animation），确定了元素的起始状态和结束状态，然后计算中间状态，通过过渡动画实现。

`@keyframes` 动画关键帧，定义动画的开始和结束状态。起点和终点可以使用百分比或关键字（from 和 to）来表示。

```css
.box {
    animation-name: animation-name; /* 动画名称 */
    animation-duration: 4s; /* 动画持续时间 */
    animation-timing-function: ease-in-out; /* 动画速度曲线 */
    animation-delay: 2s; /* 动画延迟时间 */
    animation-iteration-count: infinite; /* 动画播放次数 */
    animation-direction: alternate; /* 动画方向 设置动画在每次运行完后是反向运行还是重新回到开始位置重复运行。 */
    /* normal 正向 reverse 反向 alternate 正反交替  */
}

/* 简写 */
/* animation: animation-name 4s ease-in-out 2s infinite alternate; */

body div {
    width: 200px;
    height: 20px;
    background: #000;
    rotate: 1px;
    animation: animationname 4s ease 2s infinite normal;
}

@keyframes animationname {
    0% {
        transform: translateX(0);
        background-color: red;
        transform: rotate(0turn);
    }
    25% {
        transform: translateX(5vw);
        background-color: yellow;
        transform: rotate(0.25turn);
    }
    50% {
        transform: translateX(5vw);
        background-color: blue;
        transform: rotate(0.5turn);
    }

    75% {
        transform: translateX(5vw);
        background-color: blue;
        transform: rotate(0.75turn);
    }

    100% {
        transform: translateX(5vw);
        background-color: green;
        transform: rotate(1turn);
    }
}
```

贝塞尔曲线（cubic-bezier），可以自定义动画速度曲线。animation 的第三个参数。

1. `linear`：匀速运动
2. `ease`：慢速开始，然后加速
3. `ease-in`：慢速开始
4. `ease-out`：慢速结束
5. `ease-in-out`：慢速开始和结束
6. `cubic-bezier(n,n,n,n)`：自定义速度曲线
7. `steps(n)`：分 n 步完成动画,没有平滑过渡

## Tailwind CSS

Taiwind CSS 无需离开 HTML 通过**类名**快速实现各种样式，支持响应式设计和媒体查询。

### Install

#### raw HTML

```shell
pnpm install -D tailwindcss
npx tailwindcss init
```

edit `tailwind.config.js` file:

```js
"./src/**/*.{html,js}", "index.html"
```

add in to `tail.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

run compiler:

```shell
npx tailwindcss -o ./src/tail.css --watch
```

#### vue3

## Base 基础

设计一个卡片：

```html
<div
    class="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-md flex items-center space-x-4"
>
    <div class="flex-shrink-0">
        <img class="h-12 w-12" src="/img/logo.svg" alt="ChitChat Logo" />
    </div>
    <div>
        <div class="text-xl font-medium text-black">ChitChat</div>
        <p class="text-gray-500">You have a new message!</p>
    </div>
</div>
```

`p-6` 表示设置内边距(padding)为 6。
`max-w-sm` 表示设置最大宽度为 small(小屏幕) 的宽度。
`mx-auto` 表示在水平方向上居中对齐。
`bg-white` 表示设置背景颜色为白色。
`rounded-xl` 表示设置圆角为 extra large(特别大)。
`shadow-md` 表示设置阴影效果，阴影大小为 medium(中等)。
`flex items-center space-x-4` 表示使用 Flexbox 布局，子元素水平排列，元素之间的间距为 4。
