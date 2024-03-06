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

1. JS return 只能返回一个数据
2. JS Function 是一个函数对象： `typeof -> function`
3. JavaScript 函数有 **属性** 和 **方法**。`.length->返回接收的参数个数` `.toString()->将函数作为一个字符串返回`

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

### 函数表达式

JavaScript 函数可以通过一个表达式定义。  
函数表达式可以 **存储在变量** 中：

```javascript
// 以分号结尾，因为这是一个执行语句
var x = function (a, b) {return a * b};
var z = x(4, 3);
```

以上函数实际上是一个 **匿名函数**  (类似 Python `limbda a:a+1` 函数没有名称)。  
函数存储在变量中，不需要函数名称，通常通过变量名来调用。

### Function 构造函数 （最好不用）

> 在 JavaScript 中，很多时候，你需要避免使用 **new** 关键字。

函数本质上是一个 object ，可以通过 `new Function()` 函数构造器定义。 

```javascript
var myFunction = new Function("a", "b", "return a * 
	b");
var x = myFunction(4, 3);
```

### 函数提升 （Hoisting）

类似 `var` 时的变量提升，使用表达式定义函数时无法提升。

- 提升（Hoisting）是 JavaScript 默认将当前作用域提升到前面去的行为。
- 提升（Hoisting）应用在变量的声明与函数的声明。

```javascript
myFunction(5);

function myFunction(y) {
    return y * y;
}
```

### 匿名函数（Anonymous function）

### 自调用函数 （self invoking）

- **函数表达式** 可以 " 自调用 "。
- 自调用表达式会自动调用。
- 如果表达式后面紧跟 () ，则会自动调用。
- 不能自调用声明的函数。
- **通过添加括号，来说明它是一个函数表达式**：

```javascript
(function () {
    var x = "Hello!!";      // 我将调用自己
})();
```

以上函数实际上是一个 **匿名自我调用的函数** (没有函数名)。

### 箭头函数 ES6

```javascript
(参数1, 参数2, …, 参数N) => { 函数声明 }
(参数1, 参数2, …, 参数N) => 表达式(单一)
// 相当于：(参数1, 参数2, …, 参数N) =>{ return 表达式; }
```

当只有一个参数时，圆括号是可选的：

```javascript
(单一参数) => {函数声明}
单一参数 => {函数声明}
```

没有参数的函数应该写成一对圆括号:

```javascript
() => {函数声明}
```

e.g.

```javascript
// ES5
var x = function(x, y) {
     return x * y;
}
 
// ES6
const x = (x, y) => x * y;
```

1. 箭头函数都**没有**自己的 **this**。 不适合定义一个 **对象的方法**。
2. 当我们使用箭头函数的时候，箭头函数会默认帮我们**绑定外层 this 的值**，所以在箭头函数中 this 的值和外层的 this 是一样的。
3. 箭头函数是**不能提升**的，所以需要在使用之前定义。
4. 使用 **const** 比使用 **var** 更安全，因为函数表达式始终是一个常量。
5. 如果**函数部分只是一个语句**，则可以省略 return 关键字和大括号 {}，这样做是一个比较好的习惯:

```javascript
const x = (x, y) => x * y;
const x = (x, y) => { return x * y };
```

### 函数参数 (args ==> arguments)

#### Parameters （显性参数） Arguments （隐式参数）

JavaScript 函数有个内置的对象 arguments 对象。  
arguments 对象包含了函数调用的参数数组，**可以传入不定数量的参数。**

```javascript
// 找到最大的数
x = findMax(1, 123, 500, 115, 44, 88);
 
function findMax() {
	// arguments 包含传入函数参数的数组;
    let arg = arguments[0];
    
    if(arguments.length < 2) return arg;
	 
    for (let i = 0; i < arguments.length; i++) {
        if (arguments[i] > max) {
            max = arguments[i];
        }
    }
    return max;
}

// 计算加和
x = sumAll(1, 123, 500, 115, 44, 88);
 
function sumAll() {
    let i, sum = 0;
    for (i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
}
```

#### Default Args (默认参数)

如果函数在调用时未提供隐式参数，参数会默认设置为： **undefined**

```javascript
function myFunction(x, y) {
    if (y === undefined) {
          y = 0;
    } 
}

// simple way
// 如果 y 已经定义，y || 0 返回 y，因为 y 是 true，否则返回 0，因为 undefined 为 false。
function myFunction(x, y) {
    y = y || 0; 
}

// in ES6
// ES6 支持函数带有默认参数，就判断 undefined 和 || 的操作：
function myFunction(x, y = 10) {
    // y is 10 if not passed or undefined
    return x + y;
}
 
myFunction(0, 2) // 输出 2
myFunction(5); // 输出 15, y 参数的默认值
```

#### 值传递和引用对象传递

- 通过值传递给函数，函数对值的更改，在函数外部是不可见的。
- 通过引用对象传递给函数，函数接收到的是 **引用对象的指针**，在函数内部对引用对象的修改是可以影响到函数外部原本的对象的。

### 函数调用（function invoking）

in general. `this` 指向函数执行时当前的对象。  
在浏览器环境，`this` 指向的是 windows 对象，为浏览器环境的全局对象。

```javascript
function myFunction(a, b) {
    return a * b;
}
window.myFunction(10, 2);    // window.myFunction(10, 2) 返回 20
```

全局对象：

```javascript
function myFunction() {
    return this;
}
myFunction();                // 返回 window 对象
```

func as obj methods invoking:

```javascript
var myObject = {
    firstName:"John",
    lastName: "Doe",
    fullName: function () {
	    // this 值为对象本身
        return this;
    }
}
myObject.fullName();          // 返回 [object Object] (所有者对象)
```

使用构造函数调用函数：  
如果在函数调用前使用了 `new` 关键字，就是调用了构造函数。

```javascript
// 构造函数:
// 构造函数中 this 关键字没有任何的值。
// this 的值在实例化 new object 时候创建
function myFunction(arg1, arg2) {
    this.firstName = arg1;
    this.lastName  = arg2;
}
 
// This creates a new object
var x = new myFunction("John","Doe");
x.firstName;                             // 返回 "John"
```

作为函数方法调用函数：

function 就是一个 object 他有自己的 attribute 和 methods.

`call()` `apply()` 是预定义的函数方法，这两个方法用于调用函数，第一个参数必须是函数 this 的对象本身：

```javascript
myObject = {
name: "21211",
method: args => {
	console.log("test"+args);
}
}

function myFunction(a, b) {
	// 输出 21211
	console.log(this.name)
    return a * b;
}
// call() 按顺序传入参数 第一个参数会成为函数 this 的值
myObject = myFunction.call(myObject, 10, 2);     // 返回 20

function myFunction(a, b) {
    return a * b;
}
myArray = [10, 2];
// apply() 传入一个参数数组
myObject = myFunction.apply(myObject, myArray);  // 返回 20
```

### 函数闭包 （Function Closure）

1. 变量的生命周期
2. 变量的作用域：所有函数都能访问全局变量。在 JavaScript 中，所有函数 **都能访问它们上一层的作用域**。

```javascript
function add() {
    var counter = 0;
    function plus() {
	    // 可以访问到父函数的 counter 变量
	    counter += 1;
    }
    plus();
    return counter; 
}
```

闭包是一种保护函数私有变量的机制，在函数执行时形成私有的作用域，保护里面的私有变量不受外界干扰：

> 匿名函数内部定义的变量和函数 **只能在函数内部访问**，外部无法直接访问，从而实现了信息的封装和隐藏。  
> 如果要实现外部访问，可以通过 `return` 一个函数或者 obj 来实现外部访问。

```javascript
// 自调用函数
// 使得 add() 函数拥有私有变量 counter
// 计数器受匿名函数的作用域保护，只能通过 add 方法修改。
var add = (function () {
    let counter = 0; // 立即 invoke 且只会执行一次,设置计数器为0并返回函数表达式
    return function () {
	    return counter += 1;
    }
})();
 
add();
add();
add();
 
// 计数器为 3
```

### JS import 模块

Ref：[import - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import)

## 类 （class）对象

### Create class 创建类

**类是用于创建对象的模板。**

使用 class 关键字来创建一个类，类体在一对大括号 {} 中，我们可以在大括号 {} 中定义类成员的位置，如方法或构造函数。

每个类中包含了一个特殊的方法 `constructor()` （构造器），它是类的 **构造函数** （类似 Py `def __init__(self)`），这种方法用于创建和初始化一个由 **class** 创建的对象，初始化对象的 attribute。

```javascript
// 创建一个类并初始化了两个属性
class ClassName{
	constructor(name, url) {
		this.name = name;
		this.url = url;
		this.year = 2023;	
	}
	// add methods
	method_1() { … } 
	method_2() { … } 
	method_3() { … }
	age() { let date = new Date(); return date.getFullYear() - this.year; }
}

// 创建对象时自动运行 constructor method
let site = new ClassName("落落の博客", "https://whaleluo.top");
console.log(site.age())
```

类表达式：  
类表达式是定义类的另一种方法。类表达式 **可以命名或不命名**。命名类表达式的名称是该类体的局部名称。

```javascript
// 未命名/匿名类
let Runoob = class {
  constructor(name, url) {
    this.name = name;
    this.url = url;
  }
};
console.log(Runoob.name);
// output: "Runoob"
 
// 命名类
let Runoob = class Runoob2 {
  constructor(name, url) {
    this.name = name;
    this.url = url;
  }
};
console.log(Runoob.name);
// 输出: "Runoob2"
```

### Extends class 继承类

`super()` 用于调用父类的构造函数 `constructor()` 

```javascript
// 基类
class Animal {
    constructor(where) {
	    this.where = where;
    }
    eat() { ... }
    sleep() { ... }
};

// 派生类 Dog 类继承自 Animal
class Dog extends Animal {
    constructor(where, age) {
	    super(where); // 调用父类的初始化函数
	    this.age = age;
    }
    bark() {
	    console.log("The dog in"+this.where+" age:"+this.age);
    }
};

let dog = new Dog("china","21");
```

JavaScript 并没有像其他编程语言一样具有传统的类，而是基于 **原型的继承模型**。  
**ES6** 引入了类和 class 关键字，但底层机制仍然基于原型继承。  
**ES6** 引入了 class 关键字，使得定义类和继承更加清晰，`extends` 关键字用于建立继承关系，`super` 关键字用于在子类构造函数中调用父类的构造函数。

基于 **原型链 (prototype `/ˈprəʊ.tə.taɪp/` )** 的继承：

在下面实例中，Animal 是一个基类，Dog 是一个继承自 Animal 的子类  
`Dog.prototype` 使用 `Object.create(Animal.prototype)` 来创建一个新对象，它继承了 `Animal.prototype` 的方法和属性，通过将 `Dog.prototype.constructor` 设置为 Dog，确保继承链上的构造函数正确。

```javascript
function Animal(name) {
  this.name = name;
}
 
Animal.prototype.eat = function() {
  console.log(this.name + " is eating.");
};
 
function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}
 
// 建立原型链，让 Dog 继承 Animal
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
 
Dog.prototype.bark = function() {
  console.log(this.name + " is barking.");
};
 
var dog = new Dog("Buddy", "Labrador");
dog.eat();  // 调用从 Animal 继承的方法
dog.bark(); // 调用 Dog 的方法
```

### Getter and setter

类中我们可以使用 `getter` 和 `setter` 来获取和设置值，`getter` 和 `setter` 都需要在严格模式下执行。`getter` 和 `setter` 可以使得我们对属性的操作变的很灵活。

1. 类中添加 getter 和 setter 使用的是 `get` 和 `set` 关键字。
2. 即使 getter 是一个方法，当你想 **获取属性值时也不要使用括号**。
3. 使用 setter，请使用与设置属性值时相同的语法，虽然 set 是一个方法，但需要 **不带括号**
4. `getter/setter` 方法的名称不能与属性的名称相同，在本例中属名为 `sitename`。
5. 很多开发者在属性名称前使用下划线字符 `_` 将 `getter/setter` 与实际属性分开：

```javascript
class Runoob {
  constructor(name) {
    this._sitename = name;
  }
  get sitename() {
    return this.sitename;
  }
  set sitename(x) {
    this.sitename = x;
  }
}
 
let noob = new Runoob("菜鸟教程");
noob.sitename = "121" // 相当于调用 sitename("121")
documen2t.getElementById("demo").innerHTML = noob.sitename;
```

### 类提升（class hoisting）

函数声明和类声明之间的一个重要区别在于, **函数声明会提升，类声明不会。**  
你首先需要声明你的类，然后再访问它，否则将抛出 ReferenceError

### Class static method (类静态方法)

静态方法是使用 static 关键字修饰的方法，又叫类方法，**属于类的，但不属于对象**，在实例化对象之前可以通过 `类名.方法名` 调用静态方法。

**静态方法不能在对象上调用，只能在类中调用。**

```javascript
class Animal {
	constructor(name) {
		this._name = name;
	}
	// 类初始化后对象的方法
	hello() {
		return "The "+this.name+"say hello!"
	}
	// 类的静态方法
	static state() {
		return "static method"
	}
	get name() {
		return this._name()
	}
}

// 可以在类上直接调用 static 方法,不用 new 实例化类
Animal.state()

let dog = new Animal();
dog.state() // 不可以在类实例化后的对象中调用类的静态方法
```

### This keyword this 关键字

面向对象语言中 this 表示当前对象的一个引用。

但在 JavaScript 中 this 不是固定不变的，它会随着执行环境的改变而改变。

- 在方法中，this 表示该方法所属的对象。
- 如果单独使用，this 表示全局对象。
- 在函数中，this 表示全局对象。
- 在函数中，在严格模式下，this 是未定义的 (undefined)。
- 在事件中，this 表示接收事件的元素。
- 类似 `call()` 和 `apply()` 方法可以将 this 引用到任何对象。（#1）

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

在 JavaScript 中函数也是对象，对象则有方法，`apply 和 call 就是函数对象的方法`。这两个方法异常强大，他们允许切换函数执行的上下文环境（context），即 this 绑定的对象。  
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

### JS Promise

**Promise** 是一个 ECMAScript 6 提供的类，目的是更加优雅地书写复杂的异步任务。

Promise 对象代表一个异步操作，有三种状态：**Pending**（进行中）、**Resolved**（已完成，又称 Fulfilled）和 **Rejected**（已失败）。

通过回调里的 **resolve(data)** 将这个 Promise 标记为 **resolverd**，然后进行下一步 then((data)=>{//do something})，resolve 里的参数就是你要传入 then 的数据。

```javascript
new Promise(function (resolve, reject) {
    // 要做的事情…
});
```

用于多次调用异步函数，避免回调地狱，如：

```javascript
setTimeout(function () {
    console.log("First");
    setTimeout(function () {
        console.log("Second");
        setTimeout(function () {
            console.log("Third");
        }, 3000);
    }, 4000);
}, 1000);
```

用 Promise 实现相同功能：

 **Promise 将嵌套格式的代码变成了顺序格式的代码。**

```javascript
new Promise(function (resolve, reject) {
    setTimeout(function () {
        console.log("First");
        resolve();
    }, 1000);
}).then(function () {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            console.log("Second");
            resolve();
        }, 4000);
    });
}).then(function () {
    setTimeout(function () {
        console.log("Third");
    }, 3000);
});
```

Promise 构造函数是 JavaScript 中用于创建 Promise 对象的内置构造函数。

Promise 构造函数接受一个函数作为参数，该函数是同步的并且会被立即执行，所以我们称之为**起始函数**。起始函数包含两个参数 `resolve` 和 `reject`，分别表示 `Promise` 成功和失败的状态。

起始函数执行成功时，它应该调用 `resolve` 函数并传递成功的结果。当起始函数执行失败时，它应该调用 `reject` 函数并传递失败的原因。

Promise 构造函数返回一个 Promise 对象，该对象具有以下几个方法：

- `then`：用于处理 Promise 成功状态的回调函数。
- `catch`：用于处理 Promise 失败状态的回调函数。
- `finally`：无论 Promise 是成功还是失败，都会执行的回调函数。

```javascript
// new Promise obj 需要传入一个起始函数作为参数
// 下面使用了 Javascript 匿名函数 (函数参数) =>{ 函数体 }
const promise = new Promise((resolve, reject) => {
  // 异步操作
  // 使用 setTimeout 模拟了一个异步操作。如果异步操作成功，则调用 resolve 函数并传递成功的结果；如果异步操作失败，则调用 reject 函数并传递失败的原因。
  setTimeout(() => {
    if (Math.random() < 0.5) {
      resolve('success'); // 执行成功传递给 than 方法 result
    } else {
      reject('error'); // 执行错误传递给 catch 方法 error
    }
  }, 1000);
});

// 使用 then 方法处理 Promise 成功状态的回调函数，使用 catch 方法处理 Promise 失败状态的回调函数。
promise.then(result => {
  console.log(result);
}).catch(error => {
  console.log(error);
}).finally(
// 成功与否都会执行的函数
() => { 
	console.log("End"); 
}
);
```

- resolve 和 reject 的作用域只有起始函数，不包括 then 以及其他序列；
- resolve 和 reject 并不能够使起始函数停止运行，别忘了 return。

then 方法可以接收两个回调函数作为参数，第一个回调函数是 Promise 对象的状态改变为 resoved 是调用，第二个回调函数是 Promise 对象的状态变为 rejected 时调用。**其中第二个参数可以省略**。

```javascript
var promise =new Promise(function(resolve,reject){
    //To Do 要异步执行的事情，这个异步执行的事情有可能成功执行完毕，那么Promise将是fulfilled状态，如果执行失败则是rejected;
    //下面测试代码，人为设置为rejected状态;
    reject("将当前构建的Promise对象的状态由pending（进行中）设置为rejected（已拒绝）"); //当然此处也可以设置为fulfilled(已完成)状态
})

promise.then(//调用第一个then()
    success=>{
        console.log("异步执行成功，状态为：fulfilled，成功后返回的结果是："+success);
        return(" 当前 success ");
    },
    error=>{
        console.log("异步执行失败，状态为rejected，失败后返回的结果是："+error);
        return(" 当前 error ");
    }
).then(
    //调用第二个then() 因为调用第一个then()方法返回的是一个新的promise对象，此对象的状态由上面的success或者error两个回调函数的执行情况决定的：
    //如果回调函数能正常执行完毕，则新的promise对象的状态为fulfilled，下面执行success2,如果回调函数无法正常执行，则promise状态为rejected;下面执行error2
    success2=>{
        console.log("第一个then的回调函数执行成功 成功返回结果："+success2);
        throw(" 当前 success2 ");//自定义异常抛出
    },
    error2=>{
        console.log("第一个then的回调函数执行失败 失败返回结果："+error2);
        return(" 当前 error2 ");
    }
).catch(err=>{
    //当success2或者error2执行报错时，catch会捕获异常;
    console.log("捕获异常："+err);
});

//上述代码,打印如下:
//异步执行失败，状态为rejected，失败后返回的结果是：将当前构建的Promise对象的状态由pending（进行中）设置为rejected（已拒绝）
//第一个then的回调函数执行成功 成功返回结果： 当前 error
//捕获异常： 当前 success2
```

这种返回值为一个 `Promise` 对象的函数称作 `Promise` 函数，它常常用于开发基于异步操作的库。

```javascript
function print(delay, message) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            console.log(message);
            resolve();
        }, delay);
    });
}

print(1000, "First").then(function () {
    return print(4000, "Second");
}).then(function () {
    print(3000, "Third");
});

```

### 异步函数 async function

一个返回 Promise 对象的 Promise 函数：

```javascript
function print(delay, message){
	return new Promise(
		(resolve, reject) => {
		
			setTimeout(
				() => {
					console.log(message);
					resolve();
				}
			)
			return;
		}
	)

}
```

用不同的时间将 **顺序输出** 三行文本：

```javascript
print(1000, "First").than(
	() => { return print(4000, "Second"); }
).than(
	() => { return print(3000,"Third"); }
)
```

可以使用 `await` 等待异步函数的执行完成，增加代码可读性：  
异步函数 `async function` 中可以使用 await 指令，`await` 指令后必须跟着一个 `Promise`，异步函数会在这个 `Promise` 运行中暂停，直到其运行结束再继续运行。

```javascript
async function asyncFunc(){
	await print(1000, "First");
	await print(4000, "Second");
	await print(3000, "Third");
}

asyncFunc();
```

处理异常也可以用 `thy-catch` 机制实现：

```javascript
async function asyncFunc(){
	try {
		await new Promise(
			(resolve, reject) => {
				throw "Some Error";
				// 或者 reject("Some Eroor");
			} 
		);
	} catch (err) {
		// 输出 "Some Error"
		console.log(err);
	}
}

asyncFunc();
```

如果 Promise 有一个正常的返回值，await 语句也会返回它：

```javascript
async function asyncFunc() {
    let value = await new Promise(
        function (resolve, reject) {
			// 相当于 return "Return value"
            resolve("Return value");
        }
    );
    console.log(value);
}
asyncFunc();
```
