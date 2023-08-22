---
title: NodeJS 速通笔记
date: 2023-08-22 14:40:10
updated: 2023-08-22 14:40:10
categories: JavaScript
tags: [NodeJS, JS, JavaScript]
description: 
thumbnail: 
banner_img: 
---

# NodeJS 速通笔记

[Node.js 教程 | 菜鸟教程](https://www.runoob.com/nodejs/nodejs-tutorial.html)

Nodejs 是运行在服务端的 JavaScript 环境。速通 NodeJS 也可以为开发 **Vue** 应用打下基础。  
使用 NodeJs 时，不仅实现了应用还实现了一整个 HTTP 服务器，可以避免使用 Nginx 进行提供 HTTP 服务。  
`NPM` 是 `NodeJS` 的一个包管理工具，类似 `Python pip`。  

NodeJS 是 **单线程单进程** 的应用程序，但是 V8 引擎提供了异步执行回调的接口，通过接口可以处理大量并发。

```shell
node -v
npm -v
```

## Npm 包管理工具

```shell
# 更新 npm 版本
npm install npm -g
# 使用淘宝的 npm 镜像
npm install -g cnpm --registry=https://registry.npmmirror.com

# 安装模块 web 框架模块 express
npm install express

npm install express          # 本地安装
npm install express -g   # 全局安装

npm list -g # 查看安装信息
npm uninstall express # 卸载模块
npm ls # 查看安装的模块
npm update express # 更新模块
npm search express # 搜索模块
npm cache clear # 清空 NPM 本地缓存
npm install
```

本地安装好之后，`express` 包就放在了工程目录下的 `node_modules` 目录中，因此在代码中只需要通过 `require('express')` 的方式就好，无需指定第三方包路径。

```js
var express = require("express") 
```

npm 的包安装分为本地安装（local）、全局安装（global）两种。使用 `-g` 指定全局安装。

在工程目录使用 `package.json` 定义包的属性，类似 Python 中的 `requirements.txt`

## Nodejs 回调函数

异步编程依托于 **回调** 来实现。Node 使用了大量的回调函数。

例如可以一边读取文件一边执行其他命令，在文件读取完成后，可以将文件内容作为回调函数的参数返回，这样在执行代码时候就没有堵塞和等待 I/O 操作

回调函数一般作为函数的最后一个参数出现：

```js
function foo(name, age, dealine, callback){
	// 执行 I/O 操作
	...
	// 执行完毕进行回调
	callback(content);
}
```

例如读取文件：

```js
var fs = require("fs"); // 导入文件操作包

var data = fs.readFileSync('input.txt'); // 堵塞了

console.log(data.toString());
console.log("程序执行结束!");

```

非堵塞：

```js
var fs = require("fs");
fs.readFile('input.txt', function (err, data) {
	// 回调函数
    if (err) return console.error(err);
    console.log(data.toString());
});
console.log("程序执行结束!"); // 先弹出
```

因此，阻塞是按顺序执行的，而非阻塞是**不需要按顺序**的，所以如果需要处理**回调函数的参数**，我们就需要写在回调函数内。

## NodeJS event loop 事件循环

Node.js 单线程类似进入一个 `while(true)` 的事件循环，直到没有事件观察者退出，每个异步事件都生成一个**事件观察者**，如果有事件发生就调用该 **回调函数**.

本质上就是一个 **事件驱动程序**

```js
// 引入 events 模块
var events = require('events');
// 创建 eventEmitter 对象 事件发射器
var eventEmitter = new events.EventEmitter(); 
 
// 创建事件处理程序
var connectHandler = function connected() {
   console.log('连接成功。');
  
   // 触发 data_received 事件 
   eventEmitter.emit('data_received');
}
 
// 绑定 connection 事件处理程序
eventEmitter.on('connection', connectHandler);
 
// 使用匿名函数绑定 data_received 事件
eventEmitter.on('data_received', function(args){
   console.log('数据接收成功。'+args);
});
 
// 触发 connection 事件 
// 发射一个 connection 事件到事件队列
// nodejs 触发对应的回调函数,可以跟参数发射到回调函数
eventEmitter.emit('connection', "param");
 
console.log("程序执行完毕。");
```

Node.js 所有的异步 I/O 操作在完成时都 会**发送一个事件到事件队列**。

Node.js 里面的许多对象都会分发事件：一个 `net.Server` 对象会在每次有新连接时触发一个事件， 一个 `fs.readStream` 对象会在文件被打开的时候触发一个事件。 所有这些产生事件的对象都是 `events.EventEmitter` 的实例。

## Nodejs Buffer 缓冲区

js 语言没有二进制类型，但是处理 tcp 流或者文件流时候必须使用二进制数据。  

使用 **Buffer** 类（自带的不需要引入），可以创建一个专门存放二进制数据的缓冲区。

```js
const buf = Buffer.from('runoob', 'ascii');

// 输出 72756e6f6f62
console.log(buf.toString('hex'));

// 输出 cnVub29i
console.log(buf.toString('base64'));
```

详细请参考：[Node.js Buffer(缓冲区) | 菜鸟教程](https://www.runoob.com/nodejs/nodejs-buffer.html)

## NodeJs 模块系统

### 引入模块

```js
// 引入当前目录下的 hello.js 文件
var hello = require('./hello');
hello.world(); // 调用 hello.js 文件的 world() 函数
```

Node.js 提供了 `exports` 和 `require` 两个对象，其中 `exports` 是模块**公开的接口**，`require` 用于**从外部获取一个模块的接口**，即所获取模块的 exports 对象。

hello.js

```js
// 可以从外部引入，导出到外部
exports.world = function() {
  console.log('Hello World');
}

// hello.js 
function Hello() {
	// object create 对象构造器
    var name; 
    this.setName = function(thyName) { 
        name = thyName; 
    }; 
    this.sayHello = function() { 
        console.log('Hello ' + name); 
    }; 
}; 
module.exports = Hello;  // 把一个对象封装到模块中

```

在以上示例中，hello.js 通过 exports 对象把 world 作为 **模块的访问接口**，在 main.js 中通过 `require('./hello')` 加载这个模块，然后就可以直接访 问 hello.js 中 exports 对象的成员函数了。

main.js

```js
//main.js 
var Hello = require('./hello'); 
hello = new Hello(); // 直接引用 
hello.setName('BYVoid'); 
hello.sayHello(); 
```
