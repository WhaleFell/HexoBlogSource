---
title: JavaScript 逆向工程小白指南
date: 2024-04-29 00:28:59
updated: 2023-04-29 00:28:59
categories: JavaScript
tags: [JavaScript, Reverse]
description:
thumbnail:
banner_img:
---

# JavaScript 逆向工程小白指南

## Reference

Thx the author of the following articles:

1. [https://github.com/JSREI](https://github.com/JSREI)

## 前置知识 (Pre-requisite)

### 事件循环 event loop

定义: 在 Chrome V8 引擎中, 主线程不断地重复获取执行信息, 再执行. 不断循环的机制称为事件循环.

为什么:

- JS 是单线程的
- 再处理异步操作的时候需要事件循环机制

概念:

- 堆(Heap): 大块非结构化内存区域, 储存对象,数据.
- 栈(Stack): 调用栈 储存该次循环待主程序执行的任务
- 队列(Queue): 事件队列, 先进先出被推入调用栈中

![JavaScript-Reverse-Engineering.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering.png&webp=true)

### 原型链 (Prototype Chain)

![JavaScript-Reverse-Engineering-1.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-1.png&webp=true)

### 浏览器储存 (Browser Storage [/ˈstɔːrɪdʒ/])

![JavaScript-Reverse-Engineering-3.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-3.png&webp=true)

### 跨域资源共享 Cross-Origin Resource Sharing (CORS)

![JavaScript-Reverse-Engineering-4.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-4.png&webp=true)

### JavaScript 模块化 打包 捆绑器 (Module Bundler[/ˈbʌndəl/])

1. Webpack

   ![JavaScript-Reverse-Engineering-5.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-5.png&webp=true)

   Note: 通常使用 babel 作为 webpack 的 compiler，将 ES6 语法转换为 ES5 语法。

### What is AST (Abstract Syntax Tree)

Generate by chatGPT:

AST 是抽象语法树（Abstract Syntax Tree）的缩写。它是在计算机科学中用于表示 **编程语言的语法结构** 的一种 **数据结构**。抽象语法树是源代码在编译或解析过程中的**中间表示形式**，它捕捉了源代码的结构和语义信息。

在编程过程中，源代码首先会被 **解析器或编译器** 读入，并生成对应的 **抽象语法树**。抽象语法树可以看作是源代码的树状表示，其中每个节点代表源代码中的一个语法构造（例如表达式、语句、函数定义等），而节点之间的关系代表了语法结构中的层次关系和依赖关系。

通过使用抽象语法树，编译器、解释器和其他代码分析工具可以对源代码进行静态分析、语法检查、语义分析、优化等操作。抽象语法树在编程语言领域中具有广泛的应用，是理解和处理源代码的重要工具之一。

## Chrome DevTools (Chrome 开发者工具使用)

Chrome 开发者工具(Chrome Development Tools)是一套内置于 Google Chrome 浏览器中的 Web 开发和调试工具。

Part:

- Elements (元素)
- Console (控制台)
- Sources (源代码)
- Network (网络)
- Performance (性能)
- Memory (内存)
- Application (应用)
- Security (安全)
- Audits[/ˈɔːdɪt/] (审计)

Keyboard shortcuts:

- `Ctrl + Shift + I` 打开开发者工具

Network filter: 网络请求过滤器:

![JavaScript-Reverse-Engineering-6.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-6.png&webp=true)
![JavaScript-Reverse-Engineering-7.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-7.png&webp=true)

Note: 按住 shift 可以查看请求间的 relier 依赖关系

Source panel: 源代码面板

conditional breakpoints: 条件断点

XHR/Fetch 网络请求
Dom Breakpoints: DOM 断点
Global Breakpoints: 全局断点
Event[/ɪˈvent/] Listener Breakpoints: 事件监听器断点

![JavaScript-Reverse-Engineering-10.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-10.png&webp=true)

override: 重写一个文件, 代替网络请求. 将网络文件代理到本地. 类似功能的 Chrome 插件有 `ReRes` 和 `Requestly`

snippet: 代码片段面板

![JavaScript-Reverse-Engineering-8.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-8.png&webp=true)

breakpoint debug: 断点调试按钮

![JavaScript-Reverse-Engineering-9.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-9.png&webp=true)

Console: 控制台

output log: 输出日志

```javascript
console.log('Hello World')
console.info('Hello World')
console.warn('Hello World')
console.error('Hello World')
// 记录函数调用的次数
console.count('The number of times this line has been called')

// 断言
console.assert

// output table: perfect for displaying arrays or objects 方便查看 structed data 结构化数据
let data = [
  { a: 1, b: 2 },
  { a: 'foo', b: 'bar' },
]

console.table(data)
// copy data to clipboard
copy(data)
// clear console
clear()

// $_: last evaluated expression 最后一次记录的表达式
// $: as a shortcut for `document.querySelector()`
$('.my-element-class')
// xpath selector
$x('//h1')
```

## 抓包工具 (Packet Sniffer)

Charles [/tʃɑ:lz/]: cross-platform 一款抓包工具，可以用来查看手机端和电脑端的网络请求，查看请求头，请求体，响应头，响应体等信息。

## TemperMonkey Hook

TemperMonkey 是一款浏览器插件，可以用来自定义网页的行为，可以用来修改网页的内容，添加新的功能等。

使用 TemperMonkey Hook `window.btoa()` 函数，将字符串转换为 base64 编码：

![JavaScript-Reverse-Engineering-11.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-11.png&webp=true)

Check if a function is native, if not, it has been hooked.
反 hook 的思路: 判断一个函数是否是 native 函数, 如果不是就证明被 hook 过了.

```javascript
// `String.indexOf()` returns the position of the first occurrence of a specified value in a string. if not found, return -1.
if ((eval + '').indexOf('[native code]') === -1) {
  // eval is not native
  // eval function has been hooked
}
```

## Stop infinite Debugger 阻止无限调试

解决恶心你 阻止你进行调试的无限 debugger, 一般用于反爬虫.

Solution n. / Resolve v.:

1. Deactivate breakpoints: 禁用所有断点
2. Never pause here: 禁用某处断点
3. conditional breakpoints: 条件断点
4. 利用中间人工具替换字符串: Replace strings with man-in-the-middle tools

   - `fiddler` 写 js script 替换
   - `mitmproxy` 写 python script 替换
   - `reres` or `override` 替换成本地文件
   - 重写并置空关键函数

5. **出现 debugger 后** 置空 `Function.prototype.constructor` 函数构造器.

```javascript
// 在出现 debugger 后 执行
// when debugger is triggered, execute following code
// can clear the `debugger;` in the function
Function.prototype.constructor = function(){...}

// but only works if following statement is true
// 只有在函数 object 的构造器指向 Function 基类时才有效
(function(){}).constructor === Function
```

刷新后请求的 js 被加入了查询参数, 导致 breakpoint 失效的情况: 通过 `reres` 扩展与本地文件做一个映射. loading 中快速按 esc 取消 cancel 掉请求.

## 快速定位关键点 (Quickly Locate Key Points)

通过使用常见加密算法的 hook, 快速定位到加密解密的关键点. 不用分析大段代码.

### 快速定位 -- 搜索关键字 (Search Keywords)

1. 中间人的全局搜索 fidder, mitmproxy
2. 开发者工具中的全局搜索. (Global Search In Devtools)

### 断点调试 (Breakpoint Debugging)

XHR/DOM/Event

### Hook

Hook 常用的加解码函数:

1. Base64: `window.btoa()` `window.atob()`
2. JSON stringify: `JSON.stringify()`
3. set-cookie: `document.cookie`
4. eval: `eval()` evaluate v. 评估
5. Websocket: `WebSocket.prototype.send = ()=>{}`

Hook 常用方法:

1. copy raw function and override: 复制原函数并重写
2. Object.defineProperty: 重写属性, getter, setter
3. 原型链

Hook inject script Time: 代码注入时期, 当页面加载完成时

### Analysis [/əˈnæləsɪs/]

1. Elements Event Listeners: 元素事件监听器
2. Network type initator: 网络请求的发起者
3. Console log XMLHttpRequest: 控制台输出网络请求

## Code obfuscation 代码混淆

- 代码压缩: 去除空格 换行
- 代码加密: eval emscripten WebAssembly
- 代码混淆: 变量 常量 控制流扁平 调试保护

`eval(var a = 1; var b = 2; console.log(a + b);)`

eval 执行里面被混淆的函数, 还原也很简单, 形同虚设.

Emscripten: C/C++ to JavaScript `asm.js`

通过 Emscripten 可以将 C/C++ 代码编译为 JavaScript 代码, 从而实现在浏览器中运行 C/C++ 代码的功能。

WebAssembly: 一种新型的低级 **字节码格式**，旨在提供一种高效的、可移植的、安全的二进制格式，以用于 Web 平台。resulte: wasm 文件 调用: JS

混淆技术: Obfuscation Techniques

- 变量混淆
- 字符串混淆
- 属性加密: Obj k-v 加密转发
- 控制流扁平化
  ![JavaScript-Reverse-Engineering-15.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-15.png&webp=true)
- 僵尸代码注入: 注入一些无用代码, 干扰判断
- 代码压缩
- 反调试: infinite debugger | 环境检测 | 置空 `console.log` 禁用控制台输出 | 格式化后卡死
- 多态变异: 一旦被调用, 立马变成不同形式的代码, 但保证功能完全一致, 避免代码被动态分析
- 锁定域名: 降低被模拟风险
- 反格式化
- 特殊编码

![JavaScript-Reverse-Engineering-12.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-12.png&webp=true)

jsfuck: 通过仅使用六个字符 `[]()!+` 来编写 JavaScript 代码的一种技术。

javascript-obfuscator: 代码混淆工具

```shell
pnpm init
pnpm install -D javascript-obfuscator
```

use:

![JavaScript-Reverse-Engineering-13.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-13.png&webp=true)

![JavaScript-Reverse-Engineering-14.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-14.png&webp=true)

https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-14.png&webp=true
