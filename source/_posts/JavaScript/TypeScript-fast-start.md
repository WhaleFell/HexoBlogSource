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

![](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/TypeScript-fast-start.png&webp=true)

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

### 元组类型 tuple

元组（tuple） 是固定数量的不同类型的元素组合。

元组和数组的不同之处，元组中的 **元素类型是可以不同的，且数量固定**。元组的好处在于可以把 **多个元素作为一个单元传递**。如果一个 function 需要返回多个值，可以把这多个值作为元组返回，而不需要创建额外的类来表示。

```typescript
// 元组

let arr: [number, boolean, string] = [1, false, "str"]

arr[0] = 2
const lst = arr[2].split("")

// 使用 readonly 修饰符，可以将元组中的元素设置为只读

let arrReadonly: readonly [number, boolean, string] = [1, false, "str"]

let xyz: readonly [x: number, y: number, z?: number] = [1, 2, 3]

let excel: [no: number, name: string, age: number][] = [
    [1, "张三", 18],
    [2, "李四", 19],
    [3, "王五", 20],
]

type first = (typeof excel)[0][1]
```

### 枚举类型 enumerate

```typescript
// enumeration 枚举类型
// auto increment 自增序列枚举
enum Color {
    Red = 0,
    Green = 1,
    Blue = 2,
}

// enum Color {
//     DarkRed = "DarkRed",
//     DarkGreen = "DarkGreen",
//     DarkBlue = "DarkBlue",
// }

console.log("Color.Red", Color.Red)

// enum 反射 only support number
let red: number = Color.Red
let key = Color[red]
console.log(`key: ${key} value: ${red}`)

```

### 类型推断和类型别名

TS 在定义变量的时候天然支持推导其类型，如果不确定的话，会被推断成 `Any`。

```typescript
// 类型别名
type WFtype = number[] | string[]

// extends 继承 左边的值会作为右边的子类型
type num = 1 extends number ? true : false // true
```

### Never 类型

never 类型来表示不应该存在的状态。**表示永远不能达到**：

never 是最底层的类型，

```typescript
function fn(): nerver {
	throw new Error("error")
}

function fn(): never {
    while (true) {
        console.log("diediedie")
    }
}
```

never 用于 switch 兜底的场景：

```typescript
// never 用于 switch 兜底的场景
type A = "sing" | "dance" | "rap" | "basketball"

function ikun(value: A): string | never {
    switch (value) {
        case "sing":
            return "唱歌"
        case "dance":
            return "跳舞"
        case "rap":
            return "rap"
        default:
            const check: never = value // 不能将类型“string”分配给类型“never”。
            return check
    }
}
```

### Symbol 类型

象征、标志，是 es5 新增的一个数据类型。

应用：在一个对象中，key 值如果相同，就会被覆盖。如果 key 是一个 Symbol(name) 则 name 相同也不会覆盖。

```typescript
// symbol 应用
let obj = {
    name: "WF",
    age: 18,
    // [age]: 27, 相同的 key 会覆盖
    [Symbol("age")]: 27, // 不能被覆盖
}

// for in 不能遍历到 symbol
for (let key in obj) {
    console.log(key)
}

console.log(Object.keys(obj)) // 也不能遍历到 symbol
Object.getOwnPropertyNames(obj) // 也不能遍历到 symbol
Object.getOwnPropertySymbols(obj) // 只可以遍历到 symbol
// es6 新增的方法反射 Reflect.ownKeys(obj) 可以遍历到 symbol
Reflect.ownKeys(obj)

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

遇到重名 interface 时，会自动进行合并。

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

### 抽象类 基类

`abstract` 定义抽象类，**抽象类不能被实例化**，只能被继承，**子类需要实现抽象类中的抽象方法**。

`abstract` 所定义的方法，**都只能描述，不能实现**。

```typescript
abstract class animal {
    _name: string
    constructor(name: string) {
        this._name = name
    }
    abstract say(word: string): void
    getName(): string {
        return this._name
    }
}

class Cat extends animal {
    constructor(name: string) {
        super(name)
    }
    say(word: string): void {
        console.log(`${this._name} say ${word}`)
    }
}

const cat = new Cat("tom")
cat.say("hello")
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

## 生成器 迭代器

手写一个生成器，使用 `function*` 标记为**生成器**

```typescript
// 生成器
function* generator() {
    yield Promise.resolve("hello")
    yield "hyy"
    yield 123
    yield { name: "hyy" }
}

const girls = generator()

// done 表示是否迭代完成
console.log(girls.next()) // { value: Promise { 'hello' }, done: false }
console.log(girls.next()) // { value: 'hyy', done: false }
console.log(girls.next()) // { value: 123, done: false }
```

迭代器：

**es6 新增的数据类型 set map argument**

```typescript
// es6 新增的数据类型
// set 元组
let set: Set<number> = new Set([1, 2, 3, 4, 5, 6]) // 无序不重复 自动去重
// map 映射 对象 字典
// 和 object 不同的的是 key 可以是任意类型, object 的 key 只能是字符串
let map: Map<any, any> = new Map()
let arr = [1, 2, 3, 4, 5, 6]
map.set("name", "hyy")
map.set(arr, "hyy")

function args(…args: any[]) {
    console.log(arguments) // arguments 是一个类数组
}
let lst = document.querySelectorAll("div") // 类数组
```

以上类数组 他们都有迭代器 ` Symbol.iterator`，所以可以使用迭代器来遍历他们：

```typescript
const each = (value: any) => {
    let iterator: any = value[Symbol.iterator]()
    let next: any = { done: false }
    while (!next.done) {
        next = iterator.next()
        if (!next.done) {
            console.log(next.value) // current next value
        }
    }
}

// 迭代器的语法糖 for of 
// object 不能使用 for of, 因为 object 没有 Symbol.iterator 迭代器
for (let value of arr) {
    console.log(value)
}

// 解构 底层原理也是调用 Symbol.iterator
let [a, b, c] = [1, 2, 3]
let copy = […arr] // 拷贝数组
let [first, …rest] = arr // 解构赋值

// 对象支持 for of
let obj = {
    max: 100,
    current: 0,
    // 手动实现一个迭代器
    // 返回一个有 next 方法的对象
    [Symbol.iterator]() {
        return {
            max: this.max,
            current: this.current,
            next() {
                if (this.current < this.max) {
                    return { value: this.current++, done: false }
                } else {
                    return { value: undefined, done: true }
                }
            },
        }
    },
}

for (let value of obj) {
    console.log(value)
}

// 对象也可以解构
let { max, current } = obj

```

## 泛型 generic

根据传入的参数，动态定义 function 内部的类型，T 相当于一个占位符号。

```typescript
function echo<T>(arg: T): T {
    return arg
}
echo<number>(1, 2)

type A<T> = string | number | T
let a: A<boolean> = true

interface Data<T> {
    code: number
    data: T
}
let data: Data<string[]> = {
    code: 200,
    data: ["1", "2"],
}

// 泛型支持设置默认值 多个值
function add<T = number, K = number>(a: T, b: K): Array<T | K> {
    return [a, b]
}
add(false, 1)
```

应用：封装一个 `XMLHttpRequest` ，使用 **generic** 定义返回的类型，这样在 then 接收的时候就能自动提示类型。

```typescript
const axios = {
    get<T>(url: string): Promise<T> {
        return new Promise<T>((resolve, reject) => {
            let xhr = new XMLHttpRequest()
            xhr.open("GET", url, true)
            xhr.onreadystatechange = () => {
                if (xhr.readyState == 4 && xhr.status == 200) {
                    resolve(JSON.parse(xhr.responseText))
                }
            }
            xhr.send(null)
        })
    },
}

interface Data {
    message: string
    code: number
}

axios.get<Data>("https://jsonplaceholder.typicode.com/todos/1").then((res) => {
    console.log(res.code)
})
```

### 泛型约束

用于控制泛型的范围，在类型后面跟一个 extands 再跟一个约束的类型

```typescript
interface Len {
    length: number
}

// 约束泛型 T 要有 length attribute
function fn<T extends Len>(arg: T): T {
    console.log(arg.length)
    return arg
}

fn("123")
fn([1, 2, 3])
fn({ length: 10 })
fn(123) // error
```

keyof 读取对象的所有属性名 实现安全的对象访问：

```typescript
// keyof 读取对象的所有属性名 实现安全的对象访问

let obj = {
    name: "wf",
    age: 18,
    sex: "famale",
}

// 获取对象的所有属性名的 Union type
type ObjKey = keyof typeof obj

function objPick<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key]
}

objPick(obj, "name")

```

keyof 的高级用法，将 interface 的 key 变可选

```typescript
// keyof 的高级用法
// 将 interface 的 key 变可选

interface Data {
    name: string
    age: number
}

type Option = {
    [K in keyof Data]?: Data[K]
}
type B = Option<Data>
```

## tsconfig.json 配置文件

通过 `tsc --init` 生成。

tsconfig.json

```json
"compilerOptions": {
  "incremental": true, // TS编译器在第一次编译之后会生成一个存储编译信息的文件，第二次编译会在第一次的基础上进行增量编译，可以提高编译的速度
  "tsBuildInfoFile": "./buildFile", // 增量编译文件的存储位置
  "diagnostics": true, // 打印诊断信息 
  "target": "ES5", // 目标语言的版本
  "module": "CommonJS", // 生成代码的模板标准
  "outFile": "./app.js", // 将多个相互依赖的文件生成一个文件，可以用在AMD模块中，即开启时应设置"module": "AMD",
  "lib": ["DOM", "ES2015", "ScriptHost", "ES2019.Array"], // TS需要引用的库，即声明文件，es5 默认引用dom、es5、scripthost,如需要使用es的高级版本特性，通常都需要配置，如es8的数组新特性需要引入"ES2019.Array",
  "allowJS": true, // 允许编译器编译JS，JSX文件
  "checkJs": true, // 允许在JS文件中报错，通常与allowJS一起使用
  "outDir": "./dist", // 指定输出目录
  "rootDir": "./", // 指定输出文件目录(用于输出)，用于控制输出目录结构
  "declaration": true, // 生成声明文件，开启后会自动生成声明文件
  "declarationDir": "./file", // 指定生成声明文件存放目录
  "emitDeclarationOnly": true, // 只生成声明文件，而不会生成js文件
  "sourceMap": true, // 生成目标文件的sourceMap文件
  "inlineSourceMap": true, // 生成目标文件的inline SourceMap，inline SourceMap会包含在生成的js文件中
  "declarationMap": true, // 为声明文件生成sourceMap
  "typeRoots": [], // 声明文件目录，默认时node_modules/@types
  "types": [], // 加载的声明文件包
  "removeComments":true, // 删除注释 
  "noEmit": true, // 不输出文件,即编译后不会生成任何js文件
  "noEmitOnError": true, // 发送错误时不输出任何文件
  "noEmitHelpers": true, // 不生成helper函数，减小体积，需要额外安装，常配合importHelpers一起使用
  "importHelpers": true, // 通过tslib引入helper函数，文件必须是模块
  "downlevelIteration": true, // 降级迭代器实现，如果目标源是es3/5，那么迭代器会有降级的实现
  "strict": true, // 开启所有严格的类型检查
  "alwaysStrict": true, // 在代码中注入 'use strict'
  "noImplicitAny": true, // 不允许隐式的any类型
  "strictNullChecks": true, // 不允许把null、undefined赋值给其他类型的变量
  "strictFunctionTypes": true, // 不允许函数参数双向协变
  "strictPropertyInitialization": true, // 类的实例属性必须初始化
  "strictBindCallApply": true, // 严格的bind/call/apply检查
  "noImplicitThis": true, // 不允许this有隐式的any类型
  "noUnusedLocals": true, // 检查只声明、未使用的局部变量(只提示不报错)
  "noUnusedParameters": true, // 检查未使用的函数参数(只提示不报错)
  "noFallthroughCasesInSwitch": true, // 防止switch语句贯穿(即如果没有break语句后面不会执行)
  "noImplicitReturns": true, //每个分支都会有返回值
  "esModuleInterop": true, // 允许export=导出，由import from 导入
  "allowUmdGlobalAccess": true, // 允许在模块中全局变量的方式访问umd模块
  "moduleResolution": "node", // 模块解析策略，ts默认用node的解析策略，即相对的方式导入
  "baseUrl": "./", // 解析非相对模块的基地址，默认是当前目录
  "paths": { // 路径映射，相对于baseUrl
    // 如使用jq时不想使用默认版本，而需要手动指定版本，可进行如下配置
    "jquery": ["node_modules/jquery/dist/jquery.min.js"]
  },
  "rootDirs": ["src","out"], // 将多个目录放在一个虚拟目录下，用于运行时，即编译后引入文件的位置可能发生变化，这也设置可以虚拟src和out在同一个目录下，不用再去改变路径也不会报错
  "listEmittedFiles": true, // 打印输出文件
  "listFiles": true// 打印编译的文件(包括引用的声明文件)
}
 
// 指定一个匹配列表（属于自动指定该路径下的所有ts相关文件）
"include": [
   "src/**/*"
],
// 指定一个排除列表（include的反向操作）
 "exclude": [
   "demo.ts"
],
// 指定哪些文件使用该配置（属于手动一个个指定文件）
 "files": [
   "demo.ts"
]
```

## Namespace 命名空间

编程中，无法避免 **同名全局变量**（global variable）造成的污染 pollution，namespace 避免这个问题的出现。相当于闭包，把函数的变量隔离出来。

`namespace` 支持：嵌套、抽离、导出、简化、合并

```typescript
// namespace 命名空间
// 嵌套 抽离 导出 简化 合并

// namespace 所有的东西包括 variable 和 function 必须要 export 导出才能访问
// 同名 namespace 会合并
// 支持把整个 namespace export 导出
// 导入简写 
import a=Test.fn from './vue'
a()

export namespace Test {
    export namespace A {
        export let a = 1
        export function fn() {
            console.log("fn")
        }
    }
}
```

应用场景：跨端项目，同一个功能，不同的 platform 有不同的实现，同一个 function 名字相同，可以用 namespace 划分不同平台的实现。

```typescript
namespace ios {
    export const pushNotification = (msg: string, type: number) => {
        console.log("IOS pushNotification")
    }
}

namespace android {
    export const pushNotification = (msg: string) => {
        console.log("Android pushNotification")
    }
    export const callPhone = (phone: string) => {
        console.log("Android callPhone")
    }
}
```

## 三斜线指令

三斜线指令是包含单个 XML 标签的单行注释。注释的内容会做为编译器指令使用。

三斜线指令 **仅可放在包含它的文件的最顶端**。

`///<reference path＝＂…”/>`

指令是三斜线指令中最常见的一种。它用于声明文件间的依赖。三斜线引用**告诉编译器在编译过程中要引入的额外的文件**。

也可以把它理解能 import，它可以告诉编译器在 **编译过程中要引入的额外的文件。**、

也可以引用声明 declare 文件：`/// <reference type="node.d.ts">`

## 声明文件 declare d.ts

初始化一个项目 

```shell
tsc --init
npm init // generate package.json
```

当使用第三方库时，我们需要引用它的声明文件，才能获得对应的代码补全、接口提示等功能。声明文件不包含具体实现，只是一个类型 hint。

```typescript
declare var 声明全局变量
declare function 声明全局方法
declare class 声明全局类
declare enum 声明全局枚举类型
declare namespace 声明（含有子属性的）全局对象
interface 和 type 声明全局类型
/// <reference /> 三斜线指令
```

一些老的库都是用 js ，要另外安装对应的 `d.ts` 声明库才能使用 ts 的语法提示：

```shell
npm install @types/node -D
```

手动编写 declare 文件：

index.ts

```typescript
import express from 'express'
const app = express()
const router = express.Router()
app.use('/api', router)
router.get('/list', (req, res) => {
    res.json({
        code: 200
    })
})
 
app.listen(9001,()=>{
    console.log(9001)
})
```

express.d.ts

```typescript
declare module 'express' {
    interface Router {
        get(path: string, cb: (req: any, res: any) => void): void
    }
    interface App {
        use(path: string, router: any): void
        listen(port: number, cb?: () => void): void
    }
    interface Express {
        (): App
    }
    const express: Express
    export default express
}
```

## Mixins 混入

### Object 混入

```typescript
interface Name {
    name: string
}
interface Age {
    age: number
}
interface Sex {
    sex: number
}
 
let people1: Name = { name: "小满" }
let people2: Age = { age: 20 }
let people3: Sex = { sex: 1 }

// ES6 新方法 Object.assign 浅拷贝 只能脱离第一层引用
const people = Object.assign(people1, people2, people3)
// ES6 对象解构 浅拷贝 只能脱离第一层引用
const people = {...people1, ...people2, ...people3}
// ES6 深拷贝 结构克隆
structedClone(a)
```

### Class 类的混入

一般用于 Plugins 依赖注入，类似 `app=MixinsPlugin(app)`：

ref： [TypeScript: Documentation - Mixins](https://www.typescriptlang.org/docs/handbook/mixins.html)

```typescript
// class mixin

// app
class APP {
    run() {
        console.log("run")
    }
}

// plugin
class Loger {
    log(msg: string) {
        console.log(msg)
    }
}

// plugin
class HTML {
    render(HTML: string) {
        console.log("render", HTML)
    }
}

// 将APP类注入 Loger 和 HTML 类

// 写一个类约束,为构造函数可以被new
// 类是对象的构造器
// type Constructor<T extends object> = new (…args: any[]) => T
// or
type Constructor<T = {}> = new (…args: any[]) => T

// 需要返回一个新的类，混入上面的3个类
// Mixins may not declare private/protected properties 混入不能声明私有/受保护的属性
function pluginMinxin<T extends Constructor<APP>>(Base: T) {
    return class extends Base {
        private Loger = new Loger()
        private HTML = new HTML()
        constructor(…args: any[]) {
            super(…args)
        }
        // 写混入的方法
        run(): void {
            this.Loger.log("run")
        }
        render() {
            this.Loger.log("render")
            this.HTML.render("render")
        }
    }
}

const mixins = pluginMinxin(APP) // 将类 mixin 返回混合后的类
const app = new mixins() // 实例化 mixin 后的类
app.run()
```

## JS 原型链

好像不是 TS 的知识，但为了更好的理解原理我放到这里来了。

### 构造函数

如果一个函数用 `new xxx()` 的方式调用，这个函数就称为**构造函数**，相当于 ES6 中的 `class` 语法糖。  
在构造函数中的 this 原先是 null，可以在函数内部通过 `this.xx` 的方式赋予 `value` 和 `method`，new 这个函数返回的就是 this 这个对象。

```javascript
let People = function(name, age){
	this.name = name
	this.age = age
	this.say = ()=>{
		console.log(`${this.name} say she had just turned ${this.age} y.o.`)
	}
}

let girl = new People()
girl.say()
```

使用 class 语法糖：

```typescript
class People() {
	constructor(name: string, age: number) {
		this.name:string = name
		this.age:number = age
	}
	public say():void {
		console.log(`${this.name} say she had just turned ${this.age} y.o.`)
	}
}

let girl = new People()
girl.say()
```

### `prototype` Attribute

`prototype` 是 **构造函数**、**class 类**，独有的一个 **对象**。`prototype` 是 **函数才会有的属性**

```javascript
function Person() {

}
// 虽然写在注释里，但是你要注意：
// prototype是函数才会有的属性 
Person.prototype.name = 'Kevin';
var person1 = new Person();
var person2 = new Person();
console.log(person1.name) // Kevin
console.log(person2.name) // Kevin
```

函数的 prototype 属性指向了一个 **原型对象**，这个对象正是调用该构造函数而创建的 **实例** 的原型，也就是这个例子中的 person1 和 person2 的原型。

那什么是原型呢？你可以这样理解：每一个 JavaScript 对象 (null 除外) 在创建的时候就会与之关联另一个对象，这个对象就是我们所说的 **原型**，每一个对象都会从原型 " 继承 " 属性。

### `__proto__` Attribute

`__proto__` 这是每一个 JavaScript **对象** (除了 null ) 都具有的一个属性，叫 **proto**，这个属性会指向该对象的原型。

`__proto__` 被称为 **对象** 的 **隐式原型**，对应的是构造函数（类）的 `prototype` 原型**对象**

```javascript
function Person() {

}
// 实例化构造函数(类)返回对象
var person = new Person();
// 对象的 __proto__ 属性指向构造函数(类)的 prototype
console.log(person.__proto__ === Person.prototype); // true
```

![](TypeScript-fast-start-1.png)

### Constructor 构造器

每个原型对象都有一个 **constructor** 属性指向关联的构造函数。

```javascript
function Person() {

}
console.log(Person === Person.prototype.constructor); // true
```

![](TypeScript-fast-start-3.png)  
综上：

```javascript
function Person() {

}

var person = new Person();

console.log(person.__proto__ === Person.prototype) // true
console.log(Person.prototype.constructor === Person) // true

// 顺便学习一个ES5的方法,可以获得对象的原型
console.log(Object.getPrototypeOf(person) === Person.prototype) // true
```

### 原型链

构造函数（类）的原型对象也是一个对象 **object**，所以他也具有 `__proto__` 隐式原型属性，指向 object 对象的构造函数（类）的原型对象 `Object.prototype` 。

```javascript
function Person(){

}

Person.prototype // 构造函数(类)的原型对象
Person.prototype.__proto__ // 原型对象的隐式原型 指向 Object.prototype
```

所以对象的隐式原型的原型对象构成一条原型链：

```javascript
function Test(name){
	this.name = name
}

const obj = new test("hyy")
Test.prototype === obj.prototype
obj.prototype.__proto__ === Object.prototype
Test.prototype.__proto__ === Object.prototype

obj {
	a:1
	__proto__: Test.prototype = {
		b:2
		__proto__: Object.prototype = {
			__proto__:null // 原型链顶层
		}
	}
}
```

对象寻找数值，是原型链从下到上一直找到原型链顶端，用 **冒泡** 的方式查找的。

![](TypeScript-fast-start-3-1.png)

## 【Experiment】装饰器 Decorator

在 tsconfig.json 启动 experiment 实验功能：

```js
"experimentalDecorators": true,
"emitDecoratorMetadata": true,
```

### Class decorator

类装饰器，可以在不修改类的情况下，通过操作类（构造函数）的原型对象 `prototype` 向类添加 method 和 attribute。

```typescript
const watcher: ClassDecorator = (target: Function) => {
	// target 是构造函数(类)
	target.prototype.getParams = <T>(params: T):T => {
        return params
    }
}

@watcher
class A {
    constructor() {
 
    }
}

const a = new A();
console.log((a as any).getParams('123'));
// 装饰器其实就是一个语法糖，本质上是
const wrapper = watcher(A) // 将源对象放入装饰器函数
const a = new wrapper() // 返回装饰过后的构造函数
```

decorator factory 装饰器工厂：允许往装饰器内传参，也就是一个高阶函数闭包，返回一个 `ClassDecorator`

```typescript
const watcher = (name: string): ClassDecorator => {
    return (target: Function) => {
        target.prototype.getParams = <T>(params: T): T => {
            return params
        }
        target.prototype.getOptions = (): string => {
            return name
        }
    }
}
 
@watcher('name')
class A {
    constructor() {
 
    }
}
 
const a = new A();
console.log((a as any).getOptions('123'));
```

### Method decorator 方法装饰器

```typescript
const met: MethodDecorator = (target, key, descriptor) => {
	// 接收三个参数
	// 1. static method -> 类的构造函数 methods -> 类的原型对象 prototype
	// 2. 成员的名字
	// 3. 成员的属性描述符 
	console.log(args);
}
 
class A {
    constructor() {
 
    }
    @met
    getName ():string {
        return '小满'
    }
}
 
 
const a = new A();
```

返回的参数：可以在 `descriptor` 更改 value 的数值，达到直接改传参的目的。

```json
[
  {},
  'setParasm',
  {
    value: [Function: setParasm],
    writable: true,
    enumerable: false,
    configurable: true
  }
]
```

## 通过 Rollup 构建 TS 项目

```shell
npm init -y # Generate package.json  
tsc --init # Generate tsconfig.json
npm i rollup -g
```

## TS 实现发布订阅模式

发布订阅模式于类似 mqtt 物联网，在前端中 `addEventListener` 和 `vue eventBus` 也是发布订阅模式。

发布订阅模式有：发布者、订阅者、调度者

## End

至此，我应该了解了 `TypeScript` 这个语言的大概，继续学习 Vue 去了。
