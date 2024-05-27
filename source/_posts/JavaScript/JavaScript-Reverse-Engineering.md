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

Cookie:

- 存储在客户端的小型数据文件。
- 每次 HTTP 请求时，浏览器会自动发送 cookie 给服务器。
- 通常用于存储用户偏好或登录状态。
- 安全性较低，容易受到 CSRF 等攻击。(篡改 overwriting)
- 存储大小限制约为 4KB。
- 默认不支持跨越, 需要配置 nginx 和前端允许传递跨域 cookie.

Session:

- 存储在服务器端的数据结构。
- 通过在客户端 cookie 中存储唯一的 session ID 来识别用户。`set-cookie: sessionID=123456` 后端 `123456` --> `{ user: 'wf', age: 18 }`
- 可以存储更多的数据，但会增加服务器的存储压力。
- 比 cookie 更安全，因为数据不直接暴露在客户端。

Token:

- 服务端生成的一串加密的字符串，客户端在每次请求时发送给服务器。
- 不依赖于 cookie，因此可以用于移动应用或跨域请求。
- 可以包含用户的身份信息和权限数据。
- 通常用于 **实现无状态** 的身份验证。
- 简而言之，cookie 和 session 通常结合使用，通过在客户端存储 session ID 来维持用户状态，而 token 则是一种更灵活的方式，可以支持不同的客户端和跨域请求。

JWT (JSON Web Token):

structure: `header.payload.signature`

传递 header: `{Authorization: 'token'}`

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

### Function call stack 函数调用栈

调用栈是解释器（比如浏览器中的 JavaScript 解释器）**追踪函数执行流** 的一种机制。当执行环境中调用了**多个函数**时，通过这种机制，我们能够追踪到**哪个函数正在执行**，执行的函数体中又调用了哪个函数。

1. 每调用一个函数，解释器就会把该函数添加**进调用栈**并开始执行。
2. 正在调用栈中执行的函数还调用了其他函数，那么新函数也将会**被添加进调用栈**，一旦这个函数被调用，便会立即执行。
3. 当前函数执行完毕后，解释器将其**清出调用栈**，继续执行当前执行环境下的剩余的代码。
4. 当分配的调用栈空间被占满时，会引发“堆栈溢出”错误。 **stack overflow**

调用栈是动态变化的. 在写递归(Recursive)函数时, 一定要注意递归的终止条件, 否则会导致调用栈溢出.

<!-- prettier-ignore -->
```javascript
// 一开始调用栈是空的
// the call stack is empty at the beginning
function nestedCall() {
  console.log('nested call')
  // 依次把执行的函数放入栈中
  // put the executed function into the stack one by one
  nestedFunc1(
    nestedFunc2(
       nestedFunc3(
        // 如果执行到 nestedFunc3 内部, 那么调用栈会是这样的 
        // if the execution reaches inside nestedFunc3, the call stack will look like this:
        // call stack: (栈顶) nestedFunc3 -> nestedFunc2 -> nestedFunc1 -> nestedCall (栈底)
        // 然后依次从栈顶开始调用函数
        // then call the functions from the top of the stack
        'sasa'
        )
       , 21), 
    { a: 1 }
  )
}

function mian(){
  nestedCall()
  // 执行完 nestedCall 后, 调用栈会是这样的 因为栈是先进后出的
  // after nestedCall is executed, the call stack will look like this:
  // call stack: (栈顶) main (栈底)
}
// 执行完所有代码后, 调用栈会是空的
// after all the code is executed, the call stack will be empty
```

<!-- prettier-ignore-end -->

![JS Content stack](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2.png&webp=true)

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

1. resume/pause script execution
   恢复/暂停脚本执行
2. step over next function call
   跨过，实际表现是不遇到函数时，执行下一步。遇到函数时，不进入函数直接执行下一步。
3. step into next function call
   跨入，实际表现是不遇到函数时，执行下一步。遇到到函数时，进入函数执行上下文。
4. step out of current function
   跳出当前函数
5. deactivate breakpoints
   停用断点
6. don‘t pause on exceptions
   不暂停异常捕获

Console: 控制台

output log: 输出日志

```javascript
console.log("Hello World");
console.info("Hello World");
console.warn("Hello World");
console.error("Hello World");
// 记录函数调用的次数
console.count("The number of times this line has been called");

// 断言
console.assert;

// output table: perfect for displaying arrays or objects 方便查看 structed data 结构化数据
let data = [
  { a: 1, b: 2 },
  { a: "foo", b: "bar" },
];

console.table(data);
// copy data to clipboard
copy(data);
// clear console
clear();

// $_: last evaluated expression 最后一次记录的表达式
// $: as a shortcut for `document.querySelector()`
$(".my-element-class");
// xpath selector
$x("//h1");
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
if ((eval + "").indexOf("[native code]") === -1) {
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

分析流程:

1. 抓包分析哪些参数是加密的.
2. 搜索参数
3. 查看 Network 的 Initiator 发起者
4. xhr breakpoint 网络断点
5. hook 逻辑
6. 分析加密
7. 补全加密逻辑

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

处理混淆后的代码:

e.g:

1. emoji 颜文字编码
   ![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-1.png&webp=true)

2. 操作原型链

   ![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-2.png&webp=true)
   ![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-3.png&webp=true)
   ![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-4.png&webp=true)

3. eval
   ![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-5.png&webp=true)

4. 严重混淆
   ![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-6.png&webp=true)

## 常见加解密 编码解码 算法

> 密码学是一个很庞大的分支, 对数学要求很高.

### Ascii 码

**ASCII** [/ˈæskiː/](American Standard Code For Information Interchange): 美国标准信息交换代码, 一种将字符编码成**二进制**的方式. 用于显示现代英语以及其他的一些西欧语言.

在 Pytion 中 使用 `ord()` 获取字符的 ASCII 码.

### Base64

将二进制数据转换为文本数据, 对于非二进制方式都先转成二进制, 连续使用 6bit 计算十进制的值, 对应检索码表.

特征: 以 `==` 结尾

引用: 嵌入 base64 格式图片 | 有些厂商非定制特定的码表达到 obfuscation 的目的

### MD5 信息指纹

MD5 计算做 Hash 校验, 对数据的完整性进行校验.

![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-7.png&webp=true)

Python Code:

![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-8.png&webp=true)

MD5 已经不具备抗碰撞性了, 不再是一种安全的散列算法, 真的不安全了吗? 查彩虹表(Hash Table) 有没有这个值.

![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-9.png&webp=true)

### AES (Advanced Encryption Standard) 高级加密标准 对称加密

用于替代原来的 DES(Data Encryption Standard) 数据加密标准. AES 是一种**对称加密算法**, 使用相同的密钥进行加密和解密. (只有一个密钥)

![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-10.png&webp=true)

Python 加解密库: Pycrpto

非对称加密: RSA(Rivest-Shamir-Adleman): 有公钥私钥两种密钥, 避免了密钥传输过程中的泄露问题.

## 控制流平坦化 (Control Flow Flattening)

通过引入**状态机**(根据条件的值做出相应的动作)与循环，破坏代码上下文之间的阅读连续性和代码块之间的关联性，将若干个分散的小整体整合成一个巨大的循环体。无法还原成原来具体的函数。

![JavaScript-Reverse-Engineering-15.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-15.png&webp=true)

目的 Purpose:

1. 无法以函数为单位的调试方法，大幅度增加调试难度。
2. 降低代码运行效率，提高爬虫运行时执行 js 的资源成本.
3. 可根据 js 运行时检测到的某些因素自由跳转到蜜罐或跳出代码执行.

实现方法:

![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-11.png&webp=true)
![alt text](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/JavaScript-Reverse-Engineering-p2-12.png&webp=true)

1. 大量的 switch case 语句 模拟状态机分发代码
2. 大量的 if else 语句
3. 多维数组
4. 数值计算比较

混淆原理 Obfuscation Principle:

通过 AST (Abstract Syntax Tree) 抽象语法树，将代码中的函数、变量、语句等元素转换为树状结构，再通过对树状结构的提取, 重新组合, 实现代码的混淆。

ATSexplorer: AST 可视化工具 <https://astexplorer.net/>

应对三部曲:

1. **全局观察:**
   大致观察每一个代码结构，是否有类似于 dom 操作的代码，是否为纯计算型的循环体，是否有 try-catch 异常捕获结构。

2. **整体分析与载入:**
   断点定于 while 开头部分(状态机跳转判断),  
   断点定于 try 代码体第一行, while 循环体整体取出构造原始函数.

3. **构造函数**
   查缺补漏，在运行的过程中通过不断地运行报错，补充缺失的函数或者数据.

通过 AST 增强代码可读性: via AST to enhance code readability.

通过 `recast` 库将代码转换为 AST 树, 通过 AST 树的遍历, 逐步还原代码, 增强代码可读性.

switch case 语句还原, 每个 case 打印每个 case 的代码, 还原每个 case 的执行顺序.
