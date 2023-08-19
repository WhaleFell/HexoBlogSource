---
title: JavaScript 快速上手指南
date: 2023-08-19 16:21:59
updated: 2023-08-19 16:21:59
categories: JavaScript
tags: [JavaScript, Guide]
description: 
thumbnail: 
banner_img: 
---

# JavaScript 快速上手指南

主要把 JavaScript 的基础语法过一遍，为接下来学习 Vue framework 打基础，因为是自用复习，所以会比较简洁。会加入一些 ES6 的语法特性，并参考以下文档： 

[JavaScript 简介 | 菜鸟教程](https://www.runoob.com/js/js-intro.html)

## 语法（Syntax）

1. 每行末尾加分号 `;`
2. 注释：

```javascript
/* 下面的这些代码会输出 一个标题和一个段落 并将代表主页的开始 */
// 输出标题：
```

## 变量&数据类型（Data types）

值类型：  
	1. Number 数字  
	2. String 字符串  
	3. Boolean 布尔  
	4. Null 空  
	5. Undefine 未定义  
	6. Symbol 符号

引用数据类型：  
	1. Object 对象 --> RegExp 正则、Date 日期；  
	2. Function 函数  
	3. Array 数组

```javascript
var length = 16;                                  // Number 通过数字字面量赋值  
var points = x * 10;                              // Number 通过表达式字面量赋值  
var lastName = "Johnson";                         // String 通过字符串字面量赋值  
var cars = ["Saab", "Volvo", "BMW"];              // Array  通过数组字面量赋值  
var person = {firstName:"John", lastName:"Doe"};  // Object 通过对象字面量赋值

// 声明变量类型
var carname=new String;  
var x=      new Number;  
var y=      new Boolean;  
var cars=   new Array;  
var person= new Object;

// 类型转换
String(obj);
Number(obj);

// 设置 null 来清空变量
cars=null;  
person=null;

// 你可以使用 typeof 操作符来检测变量的数据类型。
typeof "John"                // 返回 string  
typeof 3.14                  // 返回 number  
typeof false                 // 返回 boolean  
typeof [1,2,3,4]             // 返回 object  
typeof {name:'John', age:34} // 返回 object

// null 和 undefined 的值相等，但类型不等：
typeof undefined             // undefined  
typeof null                  // object  
// === ：值和类型进行比较；==：只比较值
null === undefined           // false  
null == undefined            // true
```

### String 字符串

字符串可以是对象

```javascript
var x = "John";
var y = new String("John");
typeof x //  返回 String
typeof y // 返回 Object
```

属性：  

|   |   |
|---|---|
|constructor|返回创建字符串属性的函数|
|length|返回字符串的长度|
|prototype|允许您向对象添加属性和方法|

方法：

…

模板字符串：

```javascript
const name = 'Runoob';
const age = 30;
const message = `My name is ${name} and I'm ${age} years old.`;
```

### Array 数组

相当于 Python List 列表

```javascript
var cars=new Array();  
cars[0]="Saab";  
cars[1]="Volvo";  
cars[2]="BMW";
// 或者  (condensed array)
var cars=new Array("Saab","Volvo","BMW");
// 或者  (literal array)
var cars=["Saab","Volvo","BMW"];
```

### Object 对象

相当于 Python 字典 Dict，JavaScript 对象是变量的容器。

```javascript
var person={firstname:"John", lastname:"Doe", id:5566};

var person={  
firstname : "John",  
lastname  : "Doe",  
id        :  5566,
// 可以添加对象方法
methodName : function() {
    // 代码 
    }
};

// 访问对象方法
person.methodName();

// 对象寻址
name=person.lastname;  
name=person["lastname"];
```

### 变量

- 变量必须以字母开头
- 变量也能以 $ 和 _ 符号开头（不过我们不推荐这么做）
- 变量名称对大小写敏感（y 和 Y 是不同的变量）

```javascript
var lastname="Doe", age=30, job="carpenter";
var lastname="Doe",  
age=30,  
job="carpenter";
var carname; // undefined
```

**使用 let 和 const (ES6)**  

let 声明的变量只在 let 命令所在的**代码块**内有效。  
const 声明一个只读的常量，一旦声明，常量的值就不能改变。  
在 ES6 之前，JavaScript 只有两种作用域： **全局变量** 与 **函数内的局部变量**。

```javascript
var carName = "Volvo"; // 使用 var 定义为全局变量
const pi = 3.14; // 只读变量
function myFunction() { 
	// 使用 let 定义的变量只能在 Block Scope 块级作用域 {}
	let carName = "inner";
	console.log(carName); 
}
```

### 生命周期

JavaScript 变量的生命期从它们被声明的时间开始。  
局部变量会在函数运行以后被删除。  
全局变量会在页面关闭后被删除。

### Block Scope 块级作用域

使用 var 关键字声明的变量不具备块级作用域的特性，它在 {} 外依然能被访问到。  
ES6 可以使用 let 关键字来实现块级作用域。  
let 声明的变量只在 let 命令所在的代码块 **{}** 内有效，在 **{}** 之外不能访问。

### 循环作用域 loop scope

```javascript
var i = 5; 
// i 变量溢出去了
for (var i = 0; i < 10; i++) {
// 一些代码… 
} 
// 这里输出 i 为 10

// 使用 let i 变量不溢出
var i = 5; for (let i = 0; i < 10; i++) { // 一些代码… } // 这里输出 i 为 5
```

## 分支语句&循环&判断&控制流（Control flow）

### 条件语句

```javascript
if (time<20)
{
    x="Good day";
}

if (time<20)
{
    x="Good day";
}
else
{
    x="Good evening";
}

if (time<10)
{
    document.write("<b>早上好</b>");
}
else if (time>=10 && time<20)
{
    document.write("<b>今天好</b>");
}
else
{
    document.write("<b>晚上好!</b>");
}
```

### Switch 选择语句

工作原理：首先设置表达式 *n*（通常是一个变量）。随后表达式的值会与结构中的每个 case 的值做比较。如果存在匹配，则与该 case 关联的代码块会被执行。请使用 **break** 来阻止代码自动地向下一个 case 运行。使用 default 关键词来规定匹配不存在时做的事情。

```javascript
switch(n)
{
    case 1:
        执行代码块 1
        break;  // 阻止向下一个 case 运行
    case 2:
        执行代码块 2
        break;
    default:
        与 case 1 和 case 2 不同时执行的代码
}
```

### For 循环

Statement 1 在循环开始之前设置变量 (var i=0)。  
Statement 2 定义循环运行的条件（i 必须小于 5）。  
Statement 3 在每次代码块已被执行后增加一个值 (i++)。

```javascript
for (var i=0;i<cars.length;i++)
{ 
    document.write(cars[i] + "<br>");
}

//  for/in 语句循环遍历对象的属性：
var person={fname:"Bill",lname:"Gates",age:56}; 
for (x in person) // x 为属性名 { txt=txt + person[x]; }

```

### While 循环

while 循环会在指定条件为真时循环执行代码块。  
`break;` `continue;` 同样用于跳出和继续循环。 

```javascript
while (i<5)
{
    x=x + "The number is " + i + "<br>";
    i++;
}

do
{
    // do 里面的语句至少会执行一次
    // 即使为 false 也会执行一次
    x=x + "The number is " + i + "<br>";
    i++;
}
while (i<5);
```

## 函数&模块 （Functions）

JS return 只能返回一个数据

```javascript
function functionname()  
{  
    // 执行代码_  
}

// 含参参数
function myFunction(var1,var2)  
{  
//代码  
}

// 带返回值的参数
function myFunction()  
{  
    var x=5;  
    return x;  
}
var myVar=myFunction();
```

## 类 （class）对象

### This keyword this 关键字

面向对象语言中 this 表示当前对象的一个引用。

但在 JavaScript 中 this 不是固定不变的，它会随着执行环境的改变而改变。

- 在方法中，this 表示该方法所属的对象。
- 如果单独使用，this 表示全局对象。
- 在函数中，this 表示全局对象。
- 在函数中，在严格模式下，this 是未定义的 (undefined)。
- 在事件中，this 表示接收事件的元素。
- 类似 call() 和 apply() 方法可以将 this 引用到任何对象。（#1）

```javascript
// 单独使用 this 在浏览器中，window 就是该全局对象为 [**object Window**]:
var x = this;

var person = {
  firstName: "John",
  lastName : "Doe",
  id       : 5566,
  // 方法中的 this 表示方法所属于的对象
  fullName : function() {
    return this.firstName + " " + this.lastName;
  }
};
```

显式函数绑定：

在 JavaScript 中函数也是对象，对象则有方法，apply 和 call 就是函数对象的方法。这两个方法异常强大，他们允许切换函数执行的上下文环境（context），即 this 绑定的对象。  
在下面实例中，当我们使用 person2 作为参数来调用 person1.fullName 方法时, **this** 将指向 person2, 即便它是 person1 的方法：

```javascript
var person1 = {
  fullName: function() {
    return this.firstName + " " + this.lastName;
  }
}
var person2 = {
  firstName:"John",
  lastName: "Doe",
}
person1.fullName.call(person2);  // 返回 "John Doe"
```

## 异常处理 （Exception handling）

当错误发生时，当事情出问题时，JavaScript 引擎通常会停止，并生成一个错误消息。  
描述这种情况的技术术语是：JavaScript 将**抛出 (throw)**一个错误。

```javascript
try {
    …    //异常的抛出
    throw exception; // throw 语句抛出错误
} catch(e) {
    …    //异常的捕获与处理
} finally {
		  // finally 语句不论之前的 try 和 catch 中是否产生异常都会执行该代码块。
    …    //结束处理
}
```

## javascript:void(0) 含义

javascript:void(0) 中最关键的是 **void** 关键字， **void** 是 JavaScript 中非常重要的关键字，**该操作符指定要计算一个表达式但是不返回值。**

```javascript
void(func())
javascript:void(func())

function getValue(){
   var a,b,c;
   a = void ( b = 5, c = 7 ); // 运行 b = 5, c = 7 但不赋值给 a
   // a = undefind
   document.write('a = ' + a + ' b = ' + b +' c = ' + c );
}
```

## JavaScript asynchronous 异步

JavaScript 中的异步操作函数往往通过 **回调函数** 来实现异步任务的结果处理。  
回调函数就是一个函数，它是在我们启动一个异步任务的时候就告诉它：等你完成了这个任务之后要干什么。这样一来主线程几乎不用关心异步任务的状态了，他自己会善始善终。

```javascript
function print() {
    document.getElementById("demo").innerHTML="RUNOOB!";
}
setTimeout(print, 3000);

setTimeout(function () {
    document.getElementById("demo").innerHTML="RUNOOB!";
}, 3000);
```

[JavaScript Promise | 菜鸟教程](https://www.runoob.com/js/js-promise.html)
