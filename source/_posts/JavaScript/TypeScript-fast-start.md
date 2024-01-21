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

reference:

1. [TypeScript 教程 | 菜鸟教程](https://www.runoob.com/typescript/ts-tutorial.html)
2. 最近发现一本好书：[TypeScript 入门教程](http://ts.xcatliu.com/)  
3. [阮一峰](https://www.ruanyifeng.com/) ES6 入门教程：[ES6 入门教程](https://es6.ruanyifeng.com/#docs/class)
4. 2024/1/21 更：最近又发现一个宝藏 up 主的视频 **小满 zc**：[小满zc自残 TypeScript 基础教程全集（完结）](https://www.bilibili.com/video/BV1wR4y1377K)
---

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

安装 `ts-node` 可以直接运行 ts 文件

```shell
npm install ts-node -g
ts-node index.ts
```

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

### 数据类型的层级关系

1. 顶级类型 (top type) ：any unknown (**unknown 只能赋值给自身或者 Any 且如果赋值给对象不能读取其 attr**)
2. Object 所有对象的基类
3. Number String Boolean 对象
4. number string boolean (instantiation object 实例化对象)

### Object 和 object

object 表示引用类型，常用于**泛型约束**。

```javascript
// Object 是 TS 中的基类，任何类型都可以是一个 Object 常常用于泛型约束
a: Object = "123"
a: Object = {"age":"12"}
// 也可以用 {} 空对象表示
a: {} = "123"
// {} 类型不可更改，少用为好

// object 只能用于引用类型不能用于原始类型
a: object = "2121" // error:string not reference object
a: object = [12,12,1,2] // correct list is reference object
```

### 数组类型 array

```javascript
let arr: number[] = [1,2,3,4]
// 使用泛型约束
let array: Array<boolean> = [true, false]
// 定义多维数组
let arr: number[][] = [[1], [2], [3]]
let arr: Array<Array<number>> = [[1], [2], [3]]

// es6 多参数
function mutil(...args:any[]) {
	console.log(args)
}
```

在函数内部有一个特殊的 value —— **arguments**，用于记录传入参数的列表，但不是一个常规的 array 类型。

```javascript
function func(...args){
	console.log(arguments)
	let a: IArguments = arguments
}
// arguments 其实是一个 object 内部实现了这样一个 interface:

interface Arguments {
	callee: Function
	length: number
	[index:number]: any
}

```

### 联合类型 类型断言 交叉类型

联合类型

```javascript
let phone: number | string = "020-12123"

let fn = function (type: number | boolean):boolean {
	return !!type // 将类型强行转成 boolean 类型，比如 0=>false 1=>true
}
```

交叉类型

```javascript
interface Pople {
	name: string
	age: number
}

// Man 继承自 Pople
interface Man extands Pople {
	sex: number | string
}

// 使用 &
const wf = (man: Pople & Man):void => {
	console.log(man);
}
 
wf({"name":"hyy", age:18, sex: "famale"})
```

**类型断言**：在联合类型的参数中，用 as 指明是哪个类型。类型断言只能欺骗 ts 的 **静态类型检查**，以编译成功，但无法规避运行时的类型错误。

```javascript
let fn = function (num: number | string):void {
	console.log((num as string).length)
	// another way...
	console.log(<string>num.length)
}
fn("1234")

// 任何东西都可以断言成 any 类型
(windows as any).adc = 123
```

### TS 内置对象

JavaScript 中有很多内置对象，它们可以直接在 TypeScript 中当作定义好的类型。

```javascript
// new 实例化一个对象，返回的值的类型就是这个对象
let num: Number = new Number(1)
let xhr: XMLHttpRequest = new XMLHttpRequest()

// HTML(Input)Element
let div = document.querySelector('footer') as HTMLElement
// 选择的 HTML dom 元素不确定时
let div: NodeListOf<HTMLDivElement | HTMLElement> =  document.querySelectorALL('div')

// browser 里面的类型
let local: Storage = localStorage
let lo:Location = location
let promise:Promise<string> = new Promise((r) => r("WF"))
promise.than(res=>{
	res.replace()
})

// Math 数学运算 Math.random() 生成 0-1 的随机数
Math.floor( (Math.random()*100) / screen.width)
```

## 流程控制

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

**ts 可以定义 object 内 this 的类型**，但在 origin JS 中无法使用，必须是 method 的第一个参数定义 this 的类型，传参时不用传 this ，和 Python 中 class 的 self 一样。

```javascript
interface Obj{
	user: number[]
	add: (this: Obj, num: number) => void
}

let obj: Obj = {
	user: [1, 2, 3]
	add(this: Obj, num: number) {
		this.user.push(num)
	}
}
```

**函数重载**：重载函数传入和返回的数据类型，在**实现函数**内部判断所属的类型进行操作

```typescript
let user:number[] = [1, 2, 3]

functuon findNum():number[] // 如果没有传入东西就是全部
function findNum(id: number):number[] // 如果传入了 id 就是单个查询
function findNum(ids?:number | number[]):number[]{
	// 实现函数
	if (typeof ids == 'number'){ // 判断传入了一个 id
		return user.filter(v => v==ids) // array filter 返回符合 true 的数组
	} eles if (Array.isArray(ids)) { // 判断传入了一个 array
		user.push(...ids)
		return user
	} eles { // 判断什么都没传入
		return user
	}
}
```

## Interface 接口

对象实现接口。类是对象的构造器。

与 Golang 接口的概念一样，接口是 **method** 和 **attribute** 的集合，需要有对应的类去实现，如果类实现了接口中的所有 methods 就可以说这个类实现了这个接口。

**interface 接口来实现一种约束**，让 对象/类 的结构满足约束的格式。

interface 接口命名**第一个字母要大写**。

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
    // 只读
    readonly callback: ()=>boolean
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

**索引签名**：接口中可以为数组的 index 和 content 设置类型

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

可选的值加 `?`

```javascript
interface Animal {
	name: string
	age: number
	type?: string
}
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

### Interface 接口定义函数类型

```javascript
interface Fn {
	(name: string):number[]
}

const fn:Fn = function (name: string){
	return [1,2,3]
}
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
      console.log("函数中显示发动机型号:"+this.engine) 
   } 
} 
 
// 创建一个对象
var obj = new Car("XXSY1")
 
// 访问字段
console.log("读取发动机型号:"+obj.engine)  
 
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

### 实现一个 Vue Virtual Dom 虚拟 DOM

```typescript
// // 1. class 的基本用法，继承和类型约束 implements
// // 2. class 的修饰符 public private protected readonly static
// // 3. super() 调用父类的构造函数
// // 4. static 静态属性和方法
// // 5. getter 和 setter
// // private 只能在类的内部访问，子类和实例化类也不能访问
// // protected 只能在类的内部和子类中访问，实例化类不能访问

interface Options {
    el: string | HTMLElement
}

interface VueCls {
    options: Options
    init(): void
}

interface Vnode {
    tag: string
    text?: string
    children?: Vnode[]
}

// virtual dom 虚拟 DOM 简单实现

class Dom {
    constructor() {
        console.log("Dom constructor")
    }

    // 静态方法 只能通过 Dom.version() 调用
    static version() {
        return "0.0.1"
    }

    // 创建元素
    private createElement(el: string): HTMLElement {
        return document.createElement(el)
    }
    // 填充文本
    private setText(el: HTMLElement, text: string | null): void {
        el.textContent = text
    }
    // 渲染函数
    protected render(data: Vnode): HTMLElement {
        let root = this.createElement(data.tag)
        if (data.children && Array.isArray(data.children)) {
            data.children.forEach((item) => {
                let child = this.render(item) // 递归渲染子节点
                root.appendChild(child)
            })
        } else {
            this.setText(root, data.text ? data.text : null)
        }
        return root
    }
}

// Vue 类 继承 Dom 类 实现 VueCls 接口
class Vue extends Dom implements VueCls {
    options: Options
    constructor(options: Options) {
        super() // 调用父类的构造函数 prototype.constructor.call(this)
        this.options = options
        this.init()
    }
    init(): void {
        // 虚拟 dom 就是通过 js 渲染真实的 dom HTML
        let data: Vnode = {
            tag: "div",
            children: [
                {
                    tag: "p",
                    text: "I am child 1",
                },
                {
                    tag: "p",
                    text: "I am child 2",
                },
            ],
        }
        let app =
            typeof this.options.el === "string"
                ? document.querySelector(this.options.el)
                : this.options.el
        app?.appendChild(this.render(data))
    }
}

let vue = new Vue({ el: "#app" })
```

### 类的 getter 和 setter

```typescript
// 类 get set 方法
class Ref {
    private _value: any // 维护一个内部变量
    constructor(value: any) {
        this._value = value
    }

    get value() {
        console.log("get value")
        return "get value: " + this._value
    }

    set value(newValue: any) {
        console.log("set value")
        this._value = "set value: " + newValue
    }
}

const ref = new Ref("hyy")
ref.value = "hyy"
console.log(ref.value)
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

## End

至此，我应该了解了 `TypeScript` 这个语言的大概，继续学习 Vue 去了。
