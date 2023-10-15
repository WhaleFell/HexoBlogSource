---
title: TypeScript 快速入门☞北
date: 2023-09-01 21:50:74
updated: 2023-09-01 21:50:74
categories: JavaScript
tags: [Vue, TypeScript, TS, Javascript]
description: 
thumbnail: 
banner_img: 
---

# TypeScript 快速入门☞北

因为在学习 Vue Framework 的过程中，碰到了很多 TypeScript 之类的知识和语法以及 Vue 源码也是通过 ts 写的，所以有必要去了解一下 TypeScript 这个语言语法。

reference: [TypeScript 教程 | 菜鸟教程](https://www.runoob.com/typescript/ts-tutorial.html)

最近发现一本好书：[TypeScript 入门教程](http://ts.xcatliu.com/)
[阮一峰](https://www.ruanyifeng.com/) ES6 入门教程：[ES6 入门教程](https://es6.ruanyifeng.com/#docs/class)

1. TypeScript ==> JavaScript 的一个 **超集**，支持 ECMAScript 6 标准
2. TypeScript 由 **微软** 爸爸开发。
3. TypeScript 设计目标是开发大型应用，它可以 **编译成纯 JavaScript**，编译出来的 JavaScript 可以运行在任何浏览器上。

TypeScript 是一种给 JavaScript 添加特性的语言扩展，他主要添加了 对 JS **类型** 的支持 ，相当于 Python 的 `function hint` (函数暗示) `Pydantic BaseModel`、`typing` 等给弱类型的动态语言 **提供类型支持**。

## INSTALL

TS 基于 NodeJS

```shell
npm config set registry https://registry.npmmirror.com # 换 npm 源
npm install -g typescript

tsc -v # 查看 version
```

编写 main.ts

```typescript
var message:string = "Hello World"
console.log(message)
```

然后执行以下命令将 TypeScript 转换为 JavaScript 代码：

```shell
tsc main.ts
node main.js # 执行编译完成的 JS 文件
```

TypeScript ==> TypeScript Compiler ==> JavaScript

## 数据类型

```typescript
let anywhere: any = null; // any 任何类型
let decLiteral: number = 6;    // 数字类型
let name: string = "Runoob"; // 字符串
let flag: boolean = true; // 布尔
let array:number[] = [1, 2]; // 数组
// 元组
let x: [string, number];
x = ['Runoob', 1];

// enumerate 枚举
enum Color {Red, Green, Blue};
let c: Color = Color.Blue;
console.log(c);    // 输出 2

// void 空的 用于标记方法返回值的类型，表示没有返回值
function hello(): void {
    alert("Hello Runoob");
}
// never 是其它类型（包括 null 和 undefined）的子类型，代表从不会出现的值。
// 函数中它通常表现为抛出异常或无法执行到终止点 例如无限循环
never
// 返回值为 never 的函数可以是无法被执行到的终止点的情况
function loop(): never {
    while (true) {}
}

// 可以用 | 来支持多种类型
// 启用 --strictNullChecks
let x: number | null | undefined;
x = 1; // 编译正确
x = undefined;    // 编译正确
x = null;    // 编译正确
```

## 各种循环 loop

TypeScript 还支持 for…of 、forEach、every 和 some 循环。

```typescript
let someArray = [1, "string", false];
 
for (let entry of someArray) {
    console.log(entry); // 1, "string", false
}

let list = [4, 5, 6];
list.forEach((val, idx, array) => {
    // val: 当前值
    // idx：当前index
    // array: Array
});

let list = [4, 5, 6];
list.every((val, idx, array) => {
    // val: 当前值
    // idx：当前index
    // array: Array
    return true; // Continues
    // Return false will quit the iteration
});
```

## Function 函数

```typescript
function function_name():return_type { 
    // 语句
    return value; 
}

// 带参数
function add(x: number, y: number): number {
    return x + y;
}
console.log(add(1,2))

// parameter? 可选参数
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}
 
let result1 = buildName("Bob");  // 正确
let result2 = buildName("Bob", "Adams", "Sr.");  // 错误，参数太多了
let result3 = buildName("Bob", "Adams");  // 正确

// set default parameter
function calculate_discount(price:number,rate:number = 0.50) { 
    var discount = price * rate; 
    console.log("计算结果: ",discount); 
} 
calculate_discount(1000) 
calculate_discount(1000,0.30)

// 剩余参数 ...parameter 得到一个参数 list Array
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
}
  
let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");

// Lambda 函数 箭头函数
var foo = (x:number)=>10 + x 
console.log(foo(100))      //输出结果为 110

```

## Interface 接口

对象实现接口。类是对象的构造器。

与 Golang 接口的概念一样，接口是 **method** 和 **attribute** 的集合，需要有对应的类去实现，如果类实现了接口中的所有 methods 就可以说这个类实现了这个接口。

```typescript
interface IPerson { 
    firstName:string, 
    lastName:string, 
    sayHi: ()=>string 
} 

// customer 类实现了 IPerson 接口
var customer:IPerson = { 
    firstName:"Tom",
    lastName:"Hanks", 
    sayHi: ():string =>{return "Hi there"} 
} 
 
console.log("Customer 对象 ") 
console.log(customer.firstName) 
console.log(customer.lastName) 
console.log(customer.sayHi())  
```

interface 中使用 union type

```typescript
interface RunOptions { 
    program:string; 
    // commandline 可以是一个 字符串列表，可以是一个字符串，也可以是一个 return 字符串的函数
    commandline:string[]|string|(()=>string); 
} 
 
// commandline 是字符串
var options:RunOptions = {program:"test1",commandline:"Hello"}; 
console.log(options.commandline)  
 
// commandline 是字符串数组
options = {program:"test1",commandline:["Hello","World"]}; 
console.log(options.commandline[0]); 
console.log(options.commandline[1]);  
 
// commandline 是一个函数表达式
options = {program:"test1",commandline:()=>{return "**Hello World**";}}; 
 
var fn:any = options.commandline; 
console.log(fn());
```

接口中可以为数组的 index 和 content 设置类型

```typescript
interface ages { 
   [index:string]:number 
} 
 
var agelist:ages; 
 // 类型正确 
agelist["runoob"] = 15  
 
// 类型错误，输出  error TS2322: Type '"google"' is not assignable to type 'number'.
// agelist[2] = "google"
```

### 接口继承

接口可以通过其他接口来扩展自己，可以继承多个接口，使用 `extends` 语句

```typescript
interface Person { 
   age:number 
} 

// Musician 继承了 Person 接口，有 Person 接口的所有 methods 和 attribute
interface Musician extends Person { 
   instrument:string 
} 
 
var drummer = <Musician>{}; 
drummer.age = 27 
drummer.instrument = "Drums" 
console.log("年龄:  "+drummer.age)
console.log("喜欢的乐器:  "+drummer.instrument)
```

允许多继承：

```typescript
interface IParent1 { 
    v1:number 
} 
 
interface IParent2 { 
    v2:number 
} 
 
interface Child extends IParent1, IParent2 { } 
var Iobj:Child = { v1:12, v2:23} 
console.log("value 1: "+Iobj.v1+" value 2: "+Iobj.v2)
```

## Class 类

Class 类是对象的构造器：与 JS 的使用方法相同

```typescript
class Car { 
   // 字段
   engine:string; 
   
   // 构造函数
   constructor(engine:string) { 
      this.engine = engine 
   }  
   
   // 方法
   disp():void { 
      console.log("函数中显示发动机型号  :   "+this.engine) 
   } 
} 
 
// 创建一个对象
var obj = new Car("XXSY1")
 
// 访问字段
console.log("读取发动机型号 :  "+obj.engine)  
 
// 访问方法
obj.disp()
```

类的继承：

```typescript
class Shape { 
   Area:number // 指定 attribute 的 type
   
   constructor(a:number) { 
      this.Area = a 
   } 
} 
 
class Circle extends Shape { 
   disp():void { 
      console.log("圆的面积:  "+this.Area) 
   } 
}
  
var obj = new Circle(223); 
obj.disp()
```

需要注意的是 **子类只能继承一个父类**，TypeScript **不支持继承多个类**，但支持多重继承：

```typescript
class Root { 
   str:string; 
} 
 
class Child extends Root {} 
class Leaf extends Child {} // 多重继承，继承了 Child 和 Root 类
 
var obj = new Leaf(); 
obj.str ="hello" 
console.log(obj.str)
```

static 静态 methods 、attribute，可直接通过类名来调用，不需要 `New`

```typescript
class StaticMem {  
   static num:number; 
   
   static disp():void { 
      console.log("num 值为 "+ StaticMem.num) 
   } 
} 
 
StaticMem.num = 12     // 初始化静态变量
StaticMem.disp()       // 调用静态方法
```

instanceof [/ˈɪnstəns/] 运算符，用于判断对象是否为指定类：

```typescript
class Person{ } 
var obj = new Person() 
var isPerson = obj instanceof Person; 
console.log("obj 对象是 Person 类实例化来的吗？ " + isPerson);
```

类的访问控制：

- **public（默认）** : 公有，可以在任何地方被访问。
- **protected** : 受保护，可以被其 **自身** 以及其 **子类** 访问。
- **private** : **私有**，只能被其定义所在的类访问。

```typescript
class Encapsulate { 
   str1:string = "hello" 
   private str2:string = "world" 
}
 
var obj = new Encapsulate() 
console.log(obj.str1)     // 可访问 
console.log(obj.str2)   // 编译错误， str2 是私有的

```

### 类实现接口

**对象实现接口。类是对象的构造器。**

使用 implements [英 /ˈɪmplɪment/ vt. 使生效 n.用具] 关键字

```typescript
interface ILoan { 
   interest:number 
} 
 
class AgriLoan implements ILoan { 
   interest:number 
   rebate:number 
   
   constructor(interest:number,rebate:number) { 
      this.interest = interest 
      this.rebate = rebate 
   } 
} 
 
var obj = new AgriLoan(10,1) 
console.log("利润为 : "+obj.interest+"，抽成为 : "+obj.rebate )
```

## Dock Typing 鸭子类型

鸭子类型（英语：duck typing）是动态类型的一种风格，是多态 (polymorphism [英 /ˌpɒlɪ'mɔːfɪz(ə)m/]) 的一种形式。

> " 当看到一只鸟 **走** 起来像鸭子、**游泳** 起来像鸭子、**叫** 起来也像鸭子，那么这只鸟就可以被称为 **鸭子**。" ——《落落前传》

在 **鸭子类型** 中，关注的是 object 的 方法 `methods` ，而不是 object 的类型。  
换言之如果一个类型 A 实现了 `run()` 和 `eat()` 的方法（即实现了行为接口）就可以通过接口类型调用这个方法，**不需要知道类型是什么**。

```typescript
interface IPoint { 
    x:number 
    y:number 
} 
// p1： 实现了 IPoint 接口的对象，即有 x,y 两个 parameter
function addPoints(p1:IPoint,p2:IPoint):IPoint { 
    var x = p1.x + p2.x 
    var y = p1.y + p2.y 
    return {x:x, y:y} 
} 
 
// 正确
var newPoint = addPoints({x:3,y:4},{x:5,y:1})  
 
// 错误 
var newPoint2 = addPoints({x:1},{x:4,y:3})

```

## 命名空间

命名空间可以解决重复命名的问题。

```typescript
// 定义了一个命名空间 SomeNameSpaceName
namespace SomeNameSpaceName { 
   // export 用于在外部导入 SomeNameSpaceName 中的类和接口
   export interface ISomeInterfaceName {      }  
   export class SomeClassName {      }  
}
```

## Module 模块

1. 可以更改的组织代码
2. 模块在自身的作用域执行，定义在模块里面的变量、函数和类在外部是不可见的，除非使用 `export` 导出，并在外部使用 `import` 导入。

SomeInterFace.ts

```typescript
// 使用 export 导出
export interface SomeInterface { 
   // 代码部分
}
```

mian.ts

```typescript
// 使用 import 导入
import someInterfaceRef = require("./SomeInterface");
```

使用 tsc 编译代码：

```typescript
tsc --module amd main.ts
// 或者
tsc --module commonjs TestShape.ts
```

## end

至此，我应该了解了 `TypeScript` 这个语言的大概，继续学习 Vue 去了。