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

Nodejs 是运行在服务端的 JavaScript 环境.速通 NodeJS 也可以为开发 **Vue** 应用打下基础.  
使用 NodeJs 时,不仅实现了应用还实现了一整个 HTTP 服务器,可以避免使用 Nginx 进行提供 HTTP 服务.  
`NPM` 是 `NodeJS` 的一个包管理工具,类似 `Python pip`.

NodeJS 是 **单线程单进程** 的应用程序,但是 V8 引擎提供了异步执行回调的接口,通过接口可以处理大量并发.

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

本地安装好之后,`express` 包就放在了工程目录下的 `node_modules` 目录中,因此在代码中只需要通过 `require('express')` 的方式就好,无需指定第三方包路径.

```js
var express = require("express")
```

npm 的包安装分为本地安装(local)、全局安装(global)两种.使用 `-g` 指定全局安装.

在工程目录使用 `package.json` 定义包的属性,类似 Python 中的 `requirements.txt`

## Nodejs 回调函数

异步编程依托于 **回调** 来实现.Node 使用了大量的回调函数.

例如可以一边读取文件一边执行其他命令,在文件读取完成后,可以将文件内容作为回调函数的参数返回,这样在执行代码时候就没有堵塞和等待 I/O 操作

回调函数一般作为函数的最后一个参数出现:

```js
function foo(name, age, dealine, callback){
	// 执行 I/O 操作
	...
	// 执行完毕进行回调
	callback(content);
}
```

例如读取文件:

```js
var fs = require("fs") // 导入文件操作包

var data = fs.readFileSync("input.txt") // 堵塞了

console.log(data.toString())
console.log("程序执行结束!")
```

非堵塞:

```js
var fs = require("fs")
fs.readFile("input.txt", function (err, data) {
  // 回调函数
  if (err) return console.error(err)
  console.log(data.toString())
})
console.log("程序执行结束!") // 先弹出
```

因此,阻塞是按顺序执行的,而非阻塞是**不需要按顺序**的,所以如果需要处理**回调函数的参数**,我们就需要写在回调函数内.

## NodeJS event loop 事件循环

Node.js 单线程类似进入一个 `while(true)` 的事件循环,直到没有事件观察者退出,每个异步事件都生成一个**事件观察者**,如果有事件发生就调用该 **回调函数**.

本质上就是一个 **事件驱动程序**

```js
// 引入 events 模块
var events = require("events")
// 创建 eventEmitter 对象 事件发射器
var eventEmitter = new events.EventEmitter()

// 创建事件处理程序
var connectHandler = function connected() {
  console.log("连接成功.")

  // 触发 data_received 事件
  eventEmitter.emit("data_received")
}

// 绑定 connection 事件处理程序
eventEmitter.on("connection", connectHandler)

// 使用匿名函数绑定 data_received 事件
eventEmitter.on("data_received", function (args) {
  console.log("数据接收成功." + args)
})

// 触发 connection 事件
// 发射一个 connection 事件到事件队列
// nodejs 触发对应的回调函数,可以跟参数发射到回调函数
eventEmitter.emit("connection", "param")

console.log("程序执行完毕.")
```

Node.js 所有的异步 I/O 操作在完成时都 会**发送一个事件到事件队列**.

Node.js 里面的许多对象都会分发事件:一个 `net.Server` 对象会在每次有新连接时触发一个事件, 一个 `fs.readStream` 对象会在文件被打开的时候触发一个事件. 所有这些产生事件的对象都是 `events.EventEmitter` 的实例.

## Nodejs Buffer 缓冲区

js 语言没有二进制类型,但是处理 tcp 流或者文件流时候必须使用二进制数据.

使用 **Buffer** 类(自带的不需要引入),可以创建一个专门存放二进制数据的缓冲区.

```js
const buf = Buffer.from("runoob", "ascii")

// 输出 72756e6f6f62
console.log(buf.toString("hex"))

// 输出 cnVub29i
console.log(buf.toString("base64"))
```

详细请参考:[Node.js Buffer(缓冲区) | 菜鸟教程](https://www.runoob.com/nodejs/nodejs-buffer.html)

## NodeJs Module System 模块系统

### 引入模块

```js
// 引入当前目录下的 hello.js 文件
var hello = require("./hello")
hello.world() // 调用 hello.js 文件的 world() 函数
```

Node.js 提供了 `exports` 和 `require` 两个对象,其中 `exports` 是模块**公开的接口**,`require` 用于**从外部获取一个模块的接口**,即所获取模块的 exports 对象.

hello.js

```js
// 可以从外部引入,导出到外部
exports.world = function () {
  console.log("Hello World")
}

// hello.js
function Hello() {
  // object create 对象构造器
  var name
  this.setName = function (thyName) {
    name = thyName
  }
  this.sayHello = function () {
    console.log("Hello " + name)
  }
}
module.exports = Hello // 把一个对象封装到模块中
```

在以上示例中,hello.js 通过 exports 对象把 world 作为 **模块的访问接口**,在 main.js 中通过 `require('./hello')` 加载这个模块,然后就可以直接访 问 hello.js 中 exports 对象的成员函数了.

main.js

```js
//main.js
var Hello = require("./hello")
hello = new Hello() // 直接引用
hello.setName("BYVoid")
hello.sayHello()
```

## NodeJS steaming 流 English Version

> Streams in Node.js have a reputation(n. 名气) for being hard to work with, and even harder to understand.
> Node.js 中的流因难以使用而闻名,甚至更难理解.

Advantages:

- Memory efficiency[/i'fiʃәnsi/:/](内存效率): 在处理大数据之前,不需要将其全部加载到内存中
- Time efficiency(时间效率): 在数据传输过程中, 可以同时处理数据. 不必等到全部数据传输完毕再处理

4 types of streams in Node.js:

1. Readable(可读流): Used for read operation. e.g. `fs.createReadStream()`
2. Writable(可写流): Used for write operation. e.g. `fs.createWriteStream()`
3. Duplex(双工流): Used for both read and write operation. e.g. `net.Socket`
4. Transform(转换流): A type of duplex stream where the output is computed based on input.

### Readable Streams 可读流

Start:

```typescript
import stream from "node:stream"
import fs from "fs"
const readableStream = new stream.Readable()

// send data to stream
readableStream.push("Hello")
readableStream.push("World!")
// Can convert the data to Readable stream, even.
const readable = Readable.from("Good morning!", { encoding: "utf8" })

// It's highly recommended to use async iterator(异步迭代器) when working with streams.
// Read chunk(块) from stream
// Note that, in this case, we had to use an async function because we wanted to return a Promise.
async function logChunks(readableObj: stream.Readable) {
  for await (const chunk of readableObj) {
    console.log(chunk)
  }
}
const readable = fs.createReadStream("tmp/test.txt", { encoding: "utf8" })
logChunks(readable)
```

`Readable.from()`: Creating readable streams from iterables (从可迭代对象创建可读流)

从迭代器创建可读流的实用方法, 它保存可迭代器中包含的数据.

可迭代对象可以是同步可迭代对象, 也可以是异步可迭代对象, 参数选项是可选的,除其他外,可以用于指定文本编码.

```typescript
async function* generate() {
  yield "hello"
  yield "streams"
}

const readable = Readable.from(generate())

readable.on("data", (chunk) => {
  console.log(chunk)
})
```

#### Two Reading Modes: 两种读取模式

flowing(流动) and paused(暂停)

- In flowing mode, data is read from the underlying system automatically and provided to an application as quickly as possible using events via the EventEmitter interface.

  > 在流动模式下,数据会自动从底层系统读取, 并通过 EventEmitter 接口使用事件尽快提供给应用程序.

- In paused mode, the `stream.read()` method must be called explicitly[/ɪkˈsplɪsɪt/](adv. 明确地) to read chunks of data from the stream.
  > 在暂停模式下,必须显式调用 `stream.read()` 方法才能从流中读取数据块.

In **flowing mode**, data can be read from the stream using events, such as `data`, `end`, and `error`.

```typescript
import fs from "fs"

var data = ""
// Create a readable stream
var readerStream = fs.createReadStream("file.txt")

// Set the encoding to be utf8.
readerStream.setEncoding("UTF8")

// As soon as you listen to data event and attach a callback it starts flowing. 一旦监听数据事件并附加回调, 它就开始流动.
// Handle stream events --> data, end, and error
readerStream.on("data", function (chunk) {
  data += chunk
})

readerStream.on("end", function () {
  console.log(data)
})

readerStream.on("error", function (err) {
  console.log(err.stack)
})

console.log("Program Ended")
```

In **paused mode**, you just need to call `read()` on the stream instance repeatedly until every chunk of data has been read:

```typescript
import fs from "fs"
var readableStream = fs.createReadStream("file.txt")
var data = ""
var chunk

// Adding a readable event handler automatically make the stream to stop flowing, and the data to be consumed via readable.read().
readableStream.on("readable", function () {
  while ((chunk = readableStream.read()) != null) {
    data += chunk
  }
})

readableStream.on("end", function () {
  console.log(data)
})
```

All Readable streams begin in **paused mode** but can be switched to **flowing mode** in one of the following ways:

- Adding a 'data' event handler.
- Calling the `stream.resume()` method.
- Calling the `stream.pipe()` method to send the data to a Writable.

The Readable can switch back to **paused mode** using one of the following:

- If there are no pipe destinations, by calling the `stream.pause()` method.
- If there are pipe destinations, by removing all pipe destinations. Multiple pipe destinations may be removed by calling the `stream.unpipe()` method.

### Writable Streams 可写流

start:

```typescript
import fs from "fs"
var readableStream = fs.createReadStream("file1.txt")
var writableStream = fs.createWriteStream("file2.txt")

readableStream.setEncoding("utf8")

readableStream.on("data", function (chunk) {
  // This function returns a boolean value.
  writableStream.write(chunk)
})
// Calling th`e writable.end()` method
// signals that no more data will be written to the Writable.
writableStream.end(() => {
  console.log("Write completed.")
})
// same as:
writableStream.on("finish", () => {
  console.log("Write completed.")
})
```

#### Writing to a Stream

Using `readable.pipe()` to write to a writable stream:

like it: `readableStream.pipe(writableStream)`

readableStream ----pipe()---> writableStream

```typescript
const Stream = require("stream")

const readableStream = new Stream.Readable()
const writableStream = new Stream.Writable()

// declare the write function
writableStream._write = (chunk, encoding, next) => {
  console.log(chunk.toString())
  next()
}

readableStream.pipe(writableStream)

readableStream.push("ping!")
readableStream.push("pong!")

writableStream.end()
```

You can also use **async iterators** to write to a writable stream, which is **recommended**

```typescript
import * as util from "util"
import * as stream from "stream"
import * as fs from "fs"
import { once } from "events"

// The default version of stream.finished() is `callback-based` but can be turned into a `Promise-based` version via `util.promisify()` (line A).
const finished = util.promisify(stream.finished)
// same as:
const finished = new Promise((resolve, reject) => {
  stream.finished(stream, (err) => {
    if (err) reject(err)
    else resolve()
  })
})

async function writeIterableToFile(iterable, filePath) {
  const writable = fs.createWriteStream(filePath, { encoding: "utf8" })

  for await (const chunk of iterable) {
    if (!writable.write(chunk)) {
      // (B)
      // Handle backpressure, waiting until it's safe to write again.
      // The 'drain' event is emitted when it's safe to write again.
      // 可写流发出 drain 事件时, 表示已经喝干了, 可以继续写入.
      await once(writable, "drain")
    }
  }
  // closing a writable stream and waiting until writting is done.
  writable.end()
  // Wait until done. Throws(抛出) if there are errors.
  await finished(writable)
}

await writeIterableToFile(["One", " line of text.\n"], "tmp/log.txt")
assert.equal(
  fs.readFileSync("tmp/log.txt", { encoding: "utf8" }),
  "One line of text.\n"
)
```

#### Readable and Writable Streams

```text
X---ReadableStream---output
                     input---WritableStream---X

input---TransformStream---output

X---ReadableStream---pipe()---WritableStream---X

pipeline(
  ReadableStream,
  WritableStream,
  (err) => {
    if (err) {
      console.error("Pipeline failed", err)
    } else {
      console.log("Pipeline succeeded")
    }
  }
)

```

#### pipline() 管道

`pipeline()` should be used instead of `pipe()`, as pipe is unsafe.

Piping is a way that provide the output of one stream as the input to another stream.

> 管道是一种方式, 将一个流的输出作为另一个流的输入.

output----pipeline()----input

```typescript
import fs from "fs"
import zlib from "zlib"
import { pipeline } from "stream"

// Use the pipeline API to easily pipe a series of streams
// together and get notified when the pipeline is fully done.
// A pipeline to gzip a large video file efficiently:

pipeline(
  fs.createReadStream("熊出没逆转时空.mp4"),
  zlib.createGzip(),
  fs.createWriteStream("熊出没逆转时空.mp4.gzip"),
  (err) => {
    if (err) {
      console.error("Pipeline failed", err)
    } else {
      console.log("Pipeline succeeded")
    }
  }
)
```

### The Stream Module 流模块

The Node.js stream module provides the foundation(基础) upon which all streaming APIs are build.

The Stream module is a native module that shipped(装船) by default in Node.js. The Stream is an instance of the `EventEmitter` class which handles events **asynchronously** in Node. Because of this, streams are inherently event-based.

> Stream 模块是 Node.js 中默认提供的原生模块. Stream 是 EventEmitter 类的一个实例,它在 Node 中异步处理事件.因此,流本质上是基于事件的.

Here are some important events related to writable streams:

- `error` – Emitted to indicate(v. 指示) that an error has occurred while writing/piping.
- `pipeline` – When a **readable stream** is piped into a writable stream, this event is emitted by the writable stream.
- `unpipe` – Emitted when you call unpipe on the readable stream and stop it from piping into the destination stream.

```typescript
import * as stream from "stream"
```

The stream module is useful for creating new types of stream instances. It is usually not necessary to use the stream module to consume streams.
