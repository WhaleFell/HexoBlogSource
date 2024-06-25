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

# TypeScript 快速入门 ☞ 北

因为在学习 Vue Framework 的过程中,碰到了很多 TypeScript 之类的知识和语法以及 Vue 源码也是通过 ts 写的,所以有必要去了解一下 TypeScript 这个语言语法.

reference:

1. [TypeScript 教程 | 菜鸟教程](https://www.runoob.com/typescript/ts-tutorial.html)
2. 最近发现一本好书:[TypeScript 入门教程](http://ts.xcatliu.com/)
3. [阮一峰](https://www.ruanyifeng.com/) ES6 入门教程:[ES6 入门教程](https://es6.ruanyifeng.com/#docs/class)
4. 2024/1/21 更:最近又发现一个宝藏 up 主的视频 **小满 zc**:[小满 zc 自残 TypeScript 基础教程全集(完结)](https://www.bilibili.com/video/BV1wR4y1377K)

---

1. TypeScript ==> JavaScript 的一个 **超集**,支持 ECMAScript 6 标准
2. TypeScript 由 **微软** 爸爸开发.
3. TypeScript 设计目标是开发大型应用,它可以 **编译成纯 JavaScript**,编译出来的 JavaScript 可以运行在任何浏览器上.

TypeScript 是一种给 JavaScript 添加特性的语言扩展,他主要添加了 对 JS **类型** 的支持 ,相当于 Python 的 `function hint` (函数暗示) `Pydantic BaseModel`、`typing` 等给弱类型的动态语言 **提供类型支持**.

## INSTALL

TS 基于 NodeJS

```shell
npm config set registry https://registry.npmmirror.com # 换 npm 源
npm install -g typescript

tsc -v # 查看 version
```

编写 main.ts

```typescript
var message: string = "Hello World"
console.log(message)
```

然后执行以下命令将 TypeScript 转换为 JavaScript 代码:

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
let anywhere: any = null // any 任何类型
let decLiteral: number = 6 // 数字类型
let name: string = "Runoob" // 字符串
let flag: boolean = true // 布尔
let array: number[] = [1, 2] // 数组
// 元组
let x: [string, number]
x = ["Runoob", 1]

// enumerate 枚举
enum Color {
  Red,
  Green,
  Blue
}
let c: Color = Color.Blue
console.log(c) // 输出 2

// void 空的 用于标记方法返回值的类型,表示没有返回值
function hello(): void {
  alert("Hello Runoob")
}
// never 是其它类型(包括 null 和 undefined)的子类型,代表从不会出现的值.
// 函数中它通常表现为抛出异常或无法执行到终止点 例如无限循环
never
// 返回值为 never 的函数可以是无法被执行到的终止点的情况
function loop(): never {
  while (true) {}
}

// 可以用 | 来支持多种类型
// 启用 --strictNullChecks
let x: number | null | undefined
x = 1 // 编译正确
x = undefined // 编译正确
x = null // 编译正确
```

### 数据类型的层级关系

1. 顶级类型 (top type) :any unknown (**unknown 只能赋值给自身或者 Any 且如果赋值给对象不能读取其 attr**)
2. Object 所有对象的基类
3. Number String Boolean 对象
4. number string boolean (instantiation object 实例化对象)

![](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/TypeScript-fast-start.png&webp=true)

### Object 和 object

object 表示引用类型,常用于**泛型约束**.

```javascript
// Object 是 TS 中的基类,任何类型都可以是一个 Object 常常用于泛型约束
a: Object = "123"
a: Object = {"age":"12"}
// 也可以用 {} 空对象表示
a: {} = "123"
// {} 类型不可更改,少用为好

// object 只能用于引用类型不能用于原始类型
a: object = "2121" // error:string not reference object
a: object = [12,12,1,2] // correct list is reference object
```

### 数组类型 array

```javascript
let arr: number[] = [1, 2, 3, 4]
// 使用泛型约束
let array: Array<boolean> = [true, false]
// 定义多维数组
let arr: number[][] = [[1], [2], [3]]
let arr: Array<Array<number>> = [[1], [2], [3]]

// es6 多参数
function mutil(...args: any[]) {
  console.log(args)
}
```

在函数内部有一个特殊的 value —— **arguments**,用于记录传入参数的列表,但不是一个常规的 array 类型.

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

元组(tuple) 是固定数量的不同类型的元素组合.

元组和数组的不同之处,元组中的 **元素类型是可以不同的,且数量固定**.元组的好处在于可以把 **多个元素作为一个单元传递**.如果一个 function 需要返回多个值,可以把这多个值作为元组返回,而不需要创建额外的类来表示.

```typescript
// 元组

let arr: [number, boolean, string] = [1, false, "str"]

arr[0] = 2
const lst = arr[2].split("")

// 使用 readonly 修饰符,可以将元组中的元素设置为只读

let arrReadonly: readonly [number, boolean, string] = [1, false, "str"]

let xyz: readonly [x: number, y: number, z?: number] = [1, 2, 3]

let excel: [no: number, name: string, age: number][] = [
  [1, "张三", 18],
  [2, "李四", 19],
  [3, "王五", 20]
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
  Blue = 2
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

TS 在定义变量的时候天然支持推导其类型,如果不确定的话,会被推断成 `Any`.

```typescript
// 类型别名
type WFtype = number[] | string[]

// extends 继承 左边的值会作为右边的子类型
type num = 1 extends number ? true : false // true
```

### Never 类型

never 类型来表示不应该存在的状态.**表示永远不能达到**:

never 是最底层的类型,

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

never 用于 switch 兜底的场景:

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
      const check: never = value // 不能将类型"string"分配给类型"never".
      return check
  }
}
```

### Symbol 类型

象征、标志,是 es5 新增的一个数据类型.

应用:在一个对象中,key 值如果相同,就会被覆盖.如果 key 是一个 Symbol(name) 则 name 相同也不会覆盖.

```typescript
// symbol 应用
let obj = {
  name: "WF",
  age: 18,
  // [age]: 27, 相同的 key 会覆盖
  [Symbol("age")]: 27 // 不能被覆盖
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
let phone: number | string = '020-12123'

let fn = function (type: number | boolean): boolean {
  return !!type // 将类型强行转成 boolean 类型,比如 0=>false 1=>true
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

**类型断言**:在联合类型的参数中,用 as 指明是哪个类型.类型断言只能欺骗 ts 的 **静态类型检查**,以编译成功,但无法规避运行时的类型错误.

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

JavaScript 中有很多内置对象,它们可以直接在 TypeScript 中当作定义好的类型.

```javascript
// new 实例化一个对象,返回的值的类型就是这个对象
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

TypeScript 还支持 for…of 、forEach、every 和 some 循环.

```typescript
let someArray = [1, "string", false]

for (let entry of someArray) {
  console.log(entry) // 1, "string", false
}

let list = [4, 5, 6]
list.forEach((val, idx, array) => {
  // val: 当前值
  // idx:当前index
  // array: Array
})

let list = [4, 5, 6]
list.every((val, idx, array) => {
  // val: 当前值
  // idx:当前index
  // array: Array
  return true // Continues
  // Return false will quit the iteration
})
```

## Function 函数

```typescript
function function_name(): return_type {
  // 语句
  return value
}

// 带参数
function add(x: number, y: number): number {
  return x + y
}
console.log(add(1, 2))

// parameter? 可选参数
function buildName(firstName: string, lastName?: string) {
  if (lastName) return firstName + " " + lastName
  else return firstName
}

let result1 = buildName("Bob") // 正确
let result2 = buildName("Bob", "Adams", "Sr.") // 错误,参数太多了
let result3 = buildName("Bob", "Adams") // 正确

// set default parameter
function calculate_discount(price: number, rate: number = 0.5) {
  var discount = price * rate
  console.log("计算结果: ", discount)
}
calculate_discount(1000)
calculate_discount(1000, 0.3)

// 剩余参数 ...parameter 得到一个参数 list Array
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ")
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie")

// Lambda 函数 箭头函数
var foo = (x: number) => 10 + x
console.log(foo(100)) //输出结果为 110
```

**ts 可以定义 object 内 this 的类型**,但在 origin JS 中无法使用,必须是 method 的第一个参数定义 this 的类型,传参时不用传 this ,和 Python 中 class 的 self 一样.

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

**函数重载**:重载函数传入和返回的数据类型,在**实现函数**内部判断所属的类型进行操作

```typescript
let user:number[] = [1, 2, 3]

function findNum(): number[] // 如果没有传入东西就是全部
function findNum(id: number): number[] // 如果传入了 id 就是单个查询
function findNum(ids?:number | number[]): number[] {
 // 实现函数
 if (typeof ids == 'number'){ // 判断传入了一个 id
  return user.filter(v => v == ids) // array filter 返回符合 true 的数组
 } eles if (Array.isArray(ids)) { // 判断传入了一个 array
  user.push(...ids)
  return user
 } eles { // 判断什么都没传入
  return user
 }
}
```

## Interface 接口

对象实现接口.类是对象的构造器.

与 Golang 接口的概念一样,接口是 **method** 和 **attribute** 的集合,需要有对应的类去实现,如果类实现了接口中的所有 methods 就可以说这个类实现了这个接口.

**interface 接口来实现一种约束**,让 对象/类 的结构满足约束的格式.

interface 接口命名 **第一个字母要大写**.

遇到重名 interface 时,会自动进行合并.

```typescript
interface IPerson {
  firstName: string
  lastName: string
  sayHi: () => string
}

// customer 类实现了 IPerson 接口
var customer: IPerson = {
  firstName: "Tom",
  lastName: "Hanks",
  sayHi: (): string => {
    return "Hi there"
  }
}

console.log("Customer 对象 ")
console.log(customer.firstName)
console.log(customer.lastName)
console.log(customer.sayHi())
```

interface 中使用 union type

```typescript
interface RunOptions {
  program: string
  // commandline 可以是一个 字符串列表,可以是一个字符串,也可以是一个 return 字符串的函数
  commandline: string[] | string | (() => string)
  // 只读
  readonly callback: () => boolean
}

// commandline 是字符串
var options: RunOptions = { program: "test1", commandline: "Hello" }
console.log(options.commandline)

// commandline 是字符串数组
options = { program: "test1", commandline: ["Hello", "World"] }
console.log(options.commandline[0])
console.log(options.commandline[1])

// commandline 是一个函数表达式
options = {
  program: "test1",
  commandline: () => {
    return "**Hello World**"
  }
}

var fn: any = options.commandline
console.log(fn())
```

**索引签名**:接口中可以为数组的 index 和 content 设置类型

```typescript
interface ages {
  [index: string]: number
}

var agelist: ages
// 类型正确
agelist["runoob"] = 15

// 类型错误,输出  error TS2322: Type '"google"' is not assignable to type 'number'.
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

接口可以通过其他接口来扩展自己,可以继承多个接口,使用 `extends` 语句

```typescript
interface Person {
  age: number
}

// Musician 继承了 Person 接口,有 Person 接口的所有 methods 和 attribute
interface Musician extends Person {
  instrument: string
}

var drummer = <Musician>{}
drummer.age = 27
drummer.instrument = "Drums"
console.log("年龄:  " + drummer.age)
console.log("喜欢的乐器:  " + drummer.instrument)
```

允许多继承:

```typescript
interface IParent1 {
  v1: number
}

interface IParent2 {
  v2: number
}

interface Child extends IParent1, IParent2 {}
var Iobj: Child = { v1: 12, v2: 23 }
console.log("value 1: " + Iobj.v1 + " value 2: " + Iobj.v2)
```

### Interface 接口定义函数类型

```javascript
interface Fn {
  (name: string): number[];
}

const fn: Fn = function (name: string) {
  return [1, 2, 3]
}
```

## Class 类

Class 类是对象的构造器:与 JS 的使用方法相同

```typescript
class Car {
  // 字段
  engine: string

  // 构造函数
  constructor(engine: string) {
    this.engine = engine
  }

  // 方法
  disp(): void {
    console.log("函数中显示发动机型号:" + this.engine)
  }
}

// 创建一个对象
var obj = new Car("XXSY1")

// 访问字段
console.log("读取发动机型号:" + obj.engine)

// 访问方法
obj.disp()
```

类的继承:

```typescript
class Shape {
  Area: number // 指定 attribute 的 type

  constructor(a: number) {
    this.Area = a
  }
}

class Circle extends Shape {
  disp(): void {
    console.log("圆的面积:  " + this.Area)
  }
}

var obj = new Circle(223)
obj.disp()
```

需要注意的是 **子类只能继承一个父类**,TypeScript **不支持继承多个类**,但支持多重继承:

```typescript
class Root {
  str: string
}

class Child extends Root {}
class Leaf extends Child {} // 多重继承,继承了 Child 和 Root 类

var obj = new Leaf()
obj.str = "hello"
console.log(obj.str)
```

static 静态 methods 、attribute,可直接通过类名来调用,不需要 `New`

```typescript
class StaticMem {
  static num: number

  static disp(): void {
    console.log("num 值为 " + StaticMem.num)
  }
}

StaticMem.num = 12 // 初始化静态变量
StaticMem.disp() // 调用静态方法
```

instanceof [/ˈɪnstəns/] 运算符,用于判断对象是否为指定类:

```typescript
class Person {}
var obj = new Person()
var isPerson = obj instanceof Person
console.log("obj 对象是 Person 类实例化来的吗? " + isPerson)
```

类的访问控制:

- **public(默认)** : 公有,可以在任何地方被访问.
- **protected** : 受保护,可以被其 **自身** 以及其 **子类** 访问.
- **private** : **私有**,只能被其定义所在的类访问.

```typescript
class Encapsulate {
  str1: string = "hello"
  private str2: string = "world"
}

var obj = new Encapsulate()
console.log(obj.str1) // 可访问
console.log(obj.str2) // 编译错误, str2 是私有的
```

### 类实现接口

**对象实现接口.类是对象的构造器.**

使用 implements [英 /ˈɪmplɪment/ vt. 使生效 n.用具] 关键字

```typescript
interface ILoan {
  interest: number
}

class AgriLoan implements ILoan {
  interest: number
  rebate: number

  constructor(interest: number, rebate: number) {
    this.interest = interest
    this.rebate = rebate
  }
}

var obj = new AgriLoan(10, 1)
console.log("利润为 : " + obj.interest + ",抽成为 : " + obj.rebate)
```

### 实现一个 Vue Virtual Dom 虚拟 DOM

```typescript
// 1. class 的基本用法,继承和类型约束 implements
// 2. class 的修饰符 public private protected readonly static
// 3. super() 调用父类的构造函数
// 4. static 静态属性和方法
// 5. getter 和 setter
// private 只能在类的内部访问,子类和实例化类也不能访问
// protected 只能在类的内部和子类中访问,实例化类不能访问

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
          text: "I am child 1"
        },
        {
          tag: "p",
          text: "I am child 2"
        }
      ]
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

`abstract` 定义抽象类,**抽象类不能被实例化**,只能被继承,**子类需要实现抽象类中的抽象方法**.

`abstract` 所定义的方法,**都只能描述,不能实现**.

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

### Initialization Order 初始化顺序

In some case, 继承类会出现 **反人类** 的行为:

```typescript
class Base {
  name = "base"
  constructor() {
    console.log("My name is " + this.name)
  }
}
class Derived extends Base {
  name = "derived"
} // Prints "base", not "derived"const d = new Derived();
```

What happened here? The order of class initialization, as defined by JavaScript(由JS定义), is:

1. Initialize base class fields (初始化基类字段)
2. Run base class constructor (运行基类构造函数)
3. Initialize derived(v.得到 n.源于) class fields (初始化派生类字段)
4. Run derived class constructor (运行派生类构造函数)

### The confusing `this` (容易混淆的 `this`)

JavaScript's handling of this is indeed unusual (JS 对于 This 的处理的确很不寻常)

调用函数时, 函数内 this 的值取决于函数的 **调用方式**.在此示例中,由于该函数是通过 obj 引用调用的,因此其 this 的值为 obj 而不是类实例.

由谁调用的函数, this 就是谁.

当使用箭头函数(arrow function)时, `this` 的值取决于函数的**定义位置**. 箭头函数不会绑定 this,而是从定义时的作用域中继承 `this`.

使用了箭头函数, 就不能在派生类(derived class) 中使用 super 调用定义的 箭头函数. 因为 **原型链** 中没有箭头函数的定义. (可能是没有绑定到基类的 this)

> You can't use `super.getName()` in a derived class, because there's no entry in the prototype chain(原型链) to fetch the base class method from.

```typescript
class MyClass {
  name = "MyClass"
  getName() {
    return this.name
  }
}
const c = new MyClass()
const obj = {
  name: "obj",
  getName: c.getName // bind this to the nearest object when be called
}

// called by obj, `this` is obj.
// Prints "obj", not "MyClass"
console.log(obj.getName())

// use arrow function to void this confusion
class MyClass {
  name = "MyClass"
  getName = () => {
    return this.name
  }
}
const c = new MyClass()
const g = c.getName
// Prints "MyClass" instead of crashing
console.log(g())
```

### Explicitly Declared `this` parameters 显式声明 class 中的 this

在方法或函数定义中,名为 this 的初始参数在 TypeScript 中具有特殊含义.这些参数在编译期间被删除.

```typescript
// TypeScript input with 'this' parameter
function fn(this: SomeType, x: number) {
  /* ... */
}
// JavaScript output
function fn(x) {
  /* ... */
}
```

TypeScript 会检查函数是否以指定的 this 上下文调用, 可以在方法定义中添加一个 this 参数来静态强制(statically enforce) 调用该方法, 而不是使用箭头函数:

```typescript
class MyClass {
  name = "MyClass"
  getName(this: MyClass) {
    return this.name
  }
}
const c = new MyClass()
// OK
c.getName()

// Error, would crash:
// The 'this' context of type 'void' is not assignable(adj. 可分配的) to method's 'this' of type 'MyClass'.
const g = c.getName
console.log(g())
```

这样, 函数也可以正确的在 派生类(derived class) 中使用 super 调用.

### Constructor Signature 构造函数签名

JavaScript 类使用 new 运算符进行实例化. 给定类本身的类型, 使用 `InstanceType` 实用程序类型(utility type models) 获取实例类型.

```typescript
class Point {
  createdAt: number
  x: number
  y: number
  constructor(x: number, y: number) {
    this.createdAt = Date.now()
    this.x = x
    this.y = y
  }
}

// get class instance type
type PointInstance = InstanceType<typeof Point>

function moveRight(point: PointInstance) {
  point.x += 5
}

const point = new Point(3, 4)
moveRight(point)
point.x // => 8
```

## Dock Typing 鸭子类型

鸭子类型(英语:duck typing)是动态类型的一种风格,是多态 (polymorphism [英 /ˌpɒlɪ'mɔːfɪz(ə)m/]) 的一种形式.

> " 当看到一只鸟 **走** 起来像鸭子、**游泳** 起来像鸭子、**叫** 起来也像鸭子,那么这只鸟就可以被称为 **鸭子**." ——<落落前传>

在 **鸭子类型** 中,关注的是 object 的 方法 `methods` ,而不是 object 的类型.  
换言之如果一个类型 A 实现了 `run()` 和 `eat()` 的方法(即实现了行为接口)就可以通过接口类型调用这个方法,**不需要知道类型是什么**.

```typescript
interface IPoint {
  x: number
  y: number
}
// p1: 实现了 IPoint 接口的对象,即有 x,y 两个 parameter
function addPoints(p1: IPoint, p2: IPoint): IPoint {
  var x = p1.x + p2.x
  var y = p1.y + p2.y
  return { x: x, y: y }
}

// 正确
var newPoint = addPoints({ x: 3, y: 4 }, { x: 5, y: 1 })

// 错误
var newPoint2 = addPoints({ x: 1 }, { x: 4, y: 3 })
```

## 命名空间

命名空间可以解决重复命名的问题.

```typescript
// 定义了一个命名空间 SomeNameSpaceName
namespace SomeNameSpaceName {
  // export 用于在外部导入 SomeNameSpaceName 中的类和接口
  export interface ISomeInterfaceName {}
  export class SomeClassName {}
}
```

## Module 模块

1. 可以更改的组织代码
2. 模块在自身的作用域执行,定义在模块里面的变量、函数和类在外部是不可见的,除非使用 `export` 导出,并在外部使用 `import` 导入.

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
import someInterfaceRef = require("./SomeInterface")
```

使用 tsc 编译代码:

```typescript
tsc --module amd main.ts
// 或者
tsc --module commonjs TestShape.ts
```

## 生成器 迭代器

手写一个生成器,使用 `function*` 标记为**生成器**

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

迭代器:

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

function args(...args: any[]) {
  console.log(arguments) // arguments 是一个类数组
}
let lst = document.querySelectorAll("div") // 类数组
```

以上类数组 他们都有迭代器 `Symbol.iterator`,所以可以使用迭代器来遍历他们:

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
// object 使用  `key for in object` 遍历
// 或者 `[key, value] for of Object.entries(obj)`
for (let value of arr) {
    console.log(value)
}

// 解构 底层原理也是调用 Symbol.iterator
let [a, b, c] = [1, 2, 3]
let copy = [...arr] // 拷贝数组
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

根据传入的参数,动态定义 function 内部的类型,T 相当于一个占位符号.

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
  data: ["1", "2"]
}

// 泛型支持设置默认值 多个值
function add<T = number, K = number>(a: T, b: K): Array<T | K> {
  return [a, b]
}
add(false, 1)
```

应用:封装一个 `XMLHttpRequest` ,使用 **generic** 定义返回的类型,这样在 then 接收的时候就能自动提示类型.

```typescript
const axios = {
  get<T>(url: string): Promise<T> {
    return new Promise<T>((resolve, reject) => {
      let xhr = new XMLHttpRequest()
      xhr.open("GET", url, true)
      xhr.onreadystatechange = () => {
        if (xhr.readyState == 4 && xhr.status == 200) {
          // return response
          resolve(JSON.parse(xhr.responseText))
        }
      }
      xhr.send(null)
    })
  }
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

用于控制泛型的范围,在类型后面跟一个 extands 再跟一个约束的类型

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

keyof 读取对象的所有属性名 实现安全的对象访问:

```typescript
// keyof 读取对象的所有属性名 实现安全的对象访问

let obj = {
  name: "wf",
  age: 18,
  sex: "famale"
}

// 获取对象的所有属性名的 Union type
type ObjKey = keyof typeof obj

function objPick<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]
}

objPick(obj, "name")
```

keyof 的高级用法,将 interface 的 key 变可选

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

通过 `tsc --init` 生成.

tsconfig.json

```json
"compilerOptions": {
  "incremental": true, // TS编译器在第一次编译之后会生成一个存储编译信息的文件,第二次编译会在第一次的基础上进行增量编译,可以提高编译的速度
  "tsBuildInfoFile": "./buildFile", // 增量编译文件的存储位置
  "diagnostics": true, // 打印诊断信息
  "target": "ES5", // 目标语言的版本
  "module": "CommonJS", // 生成代码的模板标准
  "outFile": "./app.js", // 将多个相互依赖的文件生成一个文件,可以用在AMD模块中,即开启时应设置"module": "AMD",
  "lib": ["DOM", "ES2015", "ScriptHost", "ES2019.Array"], // TS需要引用的库,即声明文件,es5 默认引用dom、es5、scripthost,如需要使用es的高级版本特性,通常都需要配置,如es8的数组新特性需要引入"ES2019.Array",
  "allowJS": true, // 允许编译器编译JS,JSX文件
  "checkJs": true, // 允许在JS文件中报错,通常与allowJS一起使用
  "outDir": "./dist", // 指定输出目录
  "rootDir": "./", // 指定输出文件目录(用于输出),用于控制输出目录结构
  "declaration": true, // 生成声明文件,开启后会自动生成声明文件
  "declarationDir": "./file", // 指定生成声明文件存放目录
  "emitDeclarationOnly": true, // 只生成声明文件,而不会生成js文件
  "sourceMap": true, // 生成目标文件的sourceMap文件
  "inlineSourceMap": true, // 生成目标文件的inline SourceMap,inline SourceMap会包含在生成的js文件中
  "declarationMap": true, // 为声明文件生成sourceMap
  "typeRoots": [], // 声明文件目录,默认时node_modules/@types
  "types": [], // 加载的声明文件包
  "removeComments":true, // 删除注释
  "noEmit": true, // 不输出文件,即编译后不会生成任何js文件
  "noEmitOnError": true, // 发送错误时不输出任何文件
  "noEmitHelpers": true, // 不生成helper函数,减小体积,需要额外安装,常配合importHelpers一起使用
  "importHelpers": true, // 通过tslib引入helper函数,文件必须是模块
  "downlevelIteration": true, // 降级迭代器实现,如果目标源是es3/5,那么迭代器会有降级的实现
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
  "esModuleInterop": true, // 允许export=导出,由import from 导入
  "allowUmdGlobalAccess": true, // 允许在模块中全局变量的方式访问umd模块
  "moduleResolution": "node", // 模块解析策略,ts默认用node的解析策略,即相对的方式导入
  "baseUrl": "./", // 解析非相对模块的基地址,默认是当前目录
  "paths": { // 路径映射,相对于baseUrl
    // 如使用jq时不想使用默认版本,而需要手动指定版本,可进行如下配置
    "jquery": ["node_modules/jquery/dist/jquery.min.js"]
  },
  "rootDirs": ["src","out"], // 将多个目录放在一个虚拟目录下,用于运行时,即编译后引入文件的位置可能发生变化,这也设置可以虚拟src和out在同一个目录下,不用再去改变路径也不会报错
  "listEmittedFiles": true, // 打印输出文件
  "listFiles": true// 打印编译的文件(包括引用的声明文件)
}

// 指定一个匹配列表(属于自动指定该路径下的所有ts相关文件)
"include": [
   "src/**/*"
],
// 指定一个排除列表(include的反向操作)
 "exclude": [
   "demo.ts"
],
// 指定哪些文件使用该配置(属于手动一个个指定文件)
 "files": [
   "demo.ts"
]
```

## Namespace 命名空间

编程中,无法避免 **同名全局变量**(global variable)造成的污染 pollution,namespace 避免这个问题的出现.相当于闭包,把函数的变量隔离出来.

`namespace` 支持:嵌套、抽离、导出、简化、合并

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

应用场景:跨端项目,同一个功能,不同的 platform 有不同的实现,同一个 function 名字相同,可以用 namespace 划分不同平台的实现.

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

三斜线指令是包含单个 XML 标签的单行注释.注释的内容会做为编译器指令使用.

三斜线指令 **仅可放在包含它的文件的最顶端**.

`///<reference path＝＂…"/>`

指令是三斜线指令中最常见的一种.它用于声明文件间的依赖.三斜线引用**告诉编译器在编译过程中要引入的额外的文件**.

也可以把它理解能 import,它可以告诉编译器在 **编译过程中要引入的额外的文件.**、

也可以引用声明 declare 文件:`/// <reference type="node.d.ts">`

## 声明文件 declare d.ts

初始化一个项目

```shell
tsc --init
npm init // generate package.json
```

当使用第三方库时,我们需要引用它的声明文件,才能获得对应的代码补全、接口提示等功能.声明文件不包含具体实现,只是一个类型 hint.

```typescript
declare var 声明全局变量
declare function 声明全局方法
declare class 声明全局类
declare enum 声明全局枚举类型
declare namespace 声明(含有子属性的)全局对象
interface 和 type 声明全局类型
/// <reference /> 三斜线指令
```

一些老的库都是用 js ,要另外安装对应的 `d.ts` 声明库才能使用 ts 的语法提示:

```shell
npm install @types/node -D
```

手动编写 declare 文件:

index.ts

```typescript
import express from "express"
const app = express()
const router = express.Router()
app.use("/api", router)
router.get("/list", (req, res) => {
  res.json({
    code: 200
  })
})

app.listen(9001, () => {
  console.log(9001)
})
```

express.d.ts

```typescript
declare module "express" {
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
const people = { ...people1, ...people2, ...people3 }
// ES6 深拷贝 结构克隆
structedClone(a)
```

### Class 类的混入

一般用于 Plugins 依赖注入,类似 `app=MixinsPlugin(app)`:

ref: [TypeScript: Documentation - Mixins](https://www.typescriptlang.org/docs/handbook/mixins.html)

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

// 需要返回一个新的类,混入上面的3个类
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

## JS 原型链 (JS prototype chain)

好像不是 TS 的知识,但为了更好的理解原理我放到这里来了.

### 构造函数

如果一个函数用 `new xxx()` 的方式调用,这个函数就称为**构造函数**,相当于 ES6 中的 `class` 语法糖.  
在构造函数中的 this 原先是 null,可以在函数内部通过 `this.xx` 的方式赋予 `value` 和 `method`,new 这个函数返回的就是 this 这个对象.

```javascript
let People = function (name, age) {
  this.name = name
  this.age = age
  this.say = () => {
    console.log(`${this.name} say she had just turned ${this.age} y.o.`)
  }
}

let girl = new People()
girl.say()
```

使用 class 语法糖:

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

`prototype` 是 **构造函数**、**class 类**,独有的一个 **对象**.`prototype` 是 **函数才会有的属性**

```javascript
function Person() {}
// Note: prototype 函数内置的属性
Person.prototype.name = "Kevin"
var person1 = new Person()
var person2 = new Person()
console.log(person1.name) // Kevin
console.log(person2.name) // Kevin
```

函数的 prototype 属性指向了一个 **原型对象**,这个对象正是调用该构造函数而创建的 **实例** 的原型,也就是这个例子中的 person1 和 person2 的原型.

那什么是原型呢?你可以这样理解:每一个 JavaScript 对象 (null 除外) 在创建的时候就会与之关联另一个对象,这个对象就是我们所说的 **原型**,每一个对象都会从原型 " 继承 " 属性.

### `__proto__` Attribute

`__proto__` 这是每一个 JavaScript **对象** (除了 null ) 都具有的一个属性,叫 **proto**,这个属性会指向该对象的原型.

`__proto__` 被称为 **对象** 的 **隐式原型**,对应的是构造函数(类)的 `prototype` 原型**对象**

```javascript
function Person() {}
// 实例化构造函数(类)返回对象
var person = new Person()
// 对象的 __proto__ 属性指向构造函数(类)的 prototype
console.log(person.__proto__ === Person.prototype) // true
```

![](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/TypeScript-fast-start-1.png&webp=true)

### Constructor 构造器

每个原型对象都有一个 **constructor** 属性指向关联的构造函数.

```javascript
function Person() {}
console.log(Person === Person.prototype.constructor) // true
```

![](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/TypeScript-fast-start-2.png&webp=true)  
综上:

```javascript
function Person() {}

var person = new Person()

console.log(person.__proto__ === Person.prototype) // true
console.log(Person.prototype.constructor === Person) // true

// 一个ES5的方法 `Object.getPrototypeOf()`, 可以获得对象的原型
console.log(Object.getPrototypeOf(person) === Person.prototype) // true
```

### 原型链

构造函数(类)的原型对象也是一个对象 **object**,所以他也具有 `__proto__` 隐式原型属性,指向 object 对象的构造函数(类)的原型对象 `Object.prototype` .

```javascript
function Person() {}

Person.prototype // 构造函数(类)的 **原型对象**
Person.prototype.__proto__ // 原型对象的隐式原型 指向 Object.prototype
```

所以对象的隐式原型的原型对象构成一条原型链:

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

对象寻找数值,是原型链从下到上一直找到原型链顶端,用 **冒泡** 的方式查找的.

![TypeScript-fast-start-3.png](https://api.whaleluo.top/onedrive/file/?path=/PicStorage/blog/JS/TypeScript-fast-start-3.png&webp=true)

## 【Experiment】装饰器 Decorator

在 tsconfig.json 启动 experiment 实验功能:

```js
"experimentalDecorators": true,
"emitDecoratorMetadata": true,
```

### Class decorator

类装饰器,可以在不修改类的情况下,通过操作类(构造函数)的原型对象 `prototype` 向类添加 method 和 attribute.

```typescript
const watcher: ClassDecorator = (target: Function) => {
  // target 是构造函数(类)
  target.prototype.getParams = <T>(params: T): T => {
    return params
  }
}

@watcher
class A {
  constructor() {}
}

const a = new A()
console.log((a as any).getParams("123"))
// 装饰器其实就是一个语法糖,本质上是
const wrapper = watcher(A) // 将源对象放入装饰器函数
const a = new wrapper() // 返回装饰过后的构造函数
```

decorator factory 装饰器工厂:允许往装饰器内传参,也就是一个高阶函数闭包,返回一个 `ClassDecorator`

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

@watcher("name")
class A {
  constructor() {}
}

const a = new A()
console.log((a as any).getOptions("123"))
```

### Method decorator 方法装饰器

```typescript
const met: MethodDecorator = (target, key, descriptor) => {
  // 接收三个参数
  // 1. static method -> 类的构造函数 methods -> 类的原型对象 prototype
  // 2. 成员的名字
  // 3. 成员的属性描述符
  console.log(args)
}

class A {
  constructor() {}
  @met
  getName(): string {
    return "小满"
  }
}

const a = new A()
```

返回的参数:可以在 `descriptor` 更改 value 的数值,达到直接改传参的目的.

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

发布订阅模式于类似 mqtt 物联网,在前端中 `addEventListener` 和 `vue eventBus` 也是发布订阅模式.

发布订阅模式有:发布者、订阅者、调度者

## ES6 新类型 weakMap weakSet set map

### Set

集合是由一组无序且唯一 (即不能重复) 的项组成的,可以想象成集合是一个既没有重复元素,也没有顺序概念的数组.

1. attribute

   - size: 返回 set 数据结构的数据长度

2. methods

   - add(value):添加某个值,返回 Set 结构本身.
   - delete(value):删除某个值,返回一个布尔值,表示删除是否成功.
   - has(value):返回一个布尔值,表示该值是否为 Set 的成员.
   - clear():清除所有成员,无返回值.

```typescript
let set: Set<number> = new Set([1, 2, 3, 4])
set.add(5)
set.has(5)
set.delete(5)
set.size //4
```

数组去重:

```typescript
let arr = [...new Set([1, 1, 1, 2, 2, 3, 4, 5, 5, 5, 5])]

console.log(arr) //[ 1, 2, 3, 4, 5 ]
```

### Map

它类似于对象,也是**键值对** (key-value) 的集合,但是 key 的 type 不限于 string,各种类型的值(包括 **object** 等 **引用类型**)都可以当作 key,是一种更完善的 Hash 结构实现.如果你需要"**键值对**"的数据结构,Map 比 Object 更合适.

操作方法同 set

```typescript
let obj = { name: "小满" }
let map: Map<object, Function> = new Map()

// methods
map.set(obj, () => 123)
map.get(obj)
map.has(obj)
map.delete(obj)
map.size
```

### WeakSet WeakMap

weak: adj. 弱的,weakSet 和 weakMap 的键都是**弱引用**,**不会计入垃圾回收**.

Javascript V8 引擎 GC 是通过 **计数引用** 进行清除的.

首先 obj 引用了这个对象 +1,aahph 也引用了 + 1,wmap 也引用了,但是不会 + 1,因为他是弱引用,不会计入垃圾回收,因此 obj 和 aahph 释放了该引用 weakMap 也会随着消失的,但是有个问题你会发现控制台能输出,值是取不到的.

因为 V8 的 GC 回收是需要一定时间的,需要延长到 最少 200ms weakMap 才会回收,为了避免这个问题 weakMap 不允许取键值,也不允许遍历,同理 weakSet 也一样.

```typescript
let obj: any = { name: "hyy 自残" } // 引用计数 +1
let aahph: any = obj // 引用计数 +2
let wmap: WeakMap<object, string> = new WeakMap() // 弱引用 weak

wmap.set(obj, "Finland") // WeakMap 的 key 是弱引用,所以 obj 不会计数,obj 当前引用计数为 2

obj = null // 对象置空,引用 -1
aahph = null // 对象置空,引用 -1

//v8 GC 不稳定 最少200ms
setTimeout(() => {
  console.log(wmap)
}, 500)
```

## Function 函数的二义性 箭头函数

一个函数有两种意义:1. 作为一个普通函数调用 2. 作为对象的构造函数 object construct function

开发者标准:如果一个**普通函数开头大写**,declare 声明这个函数就是对象构造函数.

```javascript
// 函数的二义性
function fn() {
  // 函数内的 this 指向它自身
  this.name = "hyy"
  this.say = () => {
    console.log("function object say hyy")
  }
  console.log("function log")
}

// 作为函数调用
fn()
// 作为构造函数 实例化 作为对象
fnObject = new fn()
fnObject.name = "hyy"
console.log(fnObject)
// output:
// fn {name: 'hyy', say: ƒ}
// name: "hyy"
// say: () => { console.log("function object say hyy") }
// constructor: ƒ fn()
// [[Prototype]]: Object // 对象的隐式原型
```

在 ts 中,如果需要构建对象,可以使用 class 定义一个类,通过 new 实例化类来拿到类实例化后的对象:

```typescript
class Vue extands Object {
 construct(name:string) {
  // 构造函数
  this.name = name // 类 attribute
 }
 // 类方法 methods
 say() {
  console.log(`I am ${this.name}`)
 }
 // 原型对象
 prototype:{
  __proto__: Object.prototype { // 隐式原型
   __proto__: null
  }
 }
}

vue = new Vue("hyy")
console.log(vue)
// {name:"hyy", say: ƒ}
```

### 箭头函数 Arrow Function

如果想要消除函数的**二义性**需要使用 **箭头函数** `const fn = (x, y)=> x+y`

**this** 指向的不同:

- 普通 function 定义的函数 **this 指向它自身**.
- 箭头函数 **this 是指向外面环境**的,所以箭头函数本身无法去创建属性.

箭头函数没有 **prototype** 原型对象,所以它不能被 new 成为对象构造函数.

```typescript
// Arrow Function
// this 为静态 (static), 由定义时所在的上下文决定
// this 指向外面的环境, 在 browser 中 this 是 windows
const fn = () => {
  console.log(this)
}
console.log(fn) // 没有 prototype

// Function declaration
// this 为动态 (dynamic), 由调用方式决定
function fn() {
  console.log(this)
}

fn.prototype // 有 prototype
// 使用 bind 改变 this 指向
fn.bind({ name: "hyy" })()
// 包装在 object 中 / wrapping in object 也可以改变 this 指向
obj = {
  name: "obj name",
  fn: fn
}
obj.fn() // this 指向 obj
```

箭头函数与 this 绑定:

**Note**: 箭头函数(Arrow Functions) 和 普通函数 (Function Declarations) (Function Expressions) 在 this 的处理上有一个很大的不同:

- 普通函数: this 的值是在 **运行时根据调用方式动态确定的** .比如在对象方法中, this 通常指向调用该方法的对象.
- 箭头函数: 箭头函数 **没有自己的 this 绑定**, 它会捕获其 **定义时所在上下文** 的 this 值,作为自己的 this.这意味着箭头函数的 this 是静态的,不会根据调用方式改变.

## TS 进阶代理&反射 proxy & Reflect

**Proxy**  对象用于创建一个对象的代理,从而实现基本操作的拦截和自定义(如属性查找、赋值、枚举、函数调用等).

Vue3 正是通过 **Proxy** 代理对象实现页面的响应式的.

```typescript
type Person = {
  name: string
  age: number
  text: string
}

// 返回一个 proxy 对象
// 传入:
// target 目标对象,可以是任何类型的对象,包括原生数组,函数
// handler 一个以 func 作为 attribute 的 object
// 定义了在操作 proxy 对象时的行为,例如 get set
const proxy = (object: any, key: any) => {
  return new Proxy(object, {
    get(target, prop, receiver) {
      console.log(`get key======>${key}`)
      return Reflect.get(target, prop, receiver)
    },

    set(target, prop, value, receiver) {
      console.log(`set key======>${key}`)
      return Reflect.set(target, prop, value, receiver)
    }
  })
}

const logAccess = (object: Person, key: "name" | "age" | "text") => {
  return proxy(object, key)
}

// 使用 keyof + generics 泛型优化

const logAccess = <T>(object: T, key: keyof T): T => {
  return proxy(object, key)
}

let man: Person = logAccess(
  {
    name: "hyy",
    age: 18,
    text: "刚满18岁"
  },
  "age"
)

man.age = 30

console.log(man)
```

### Reflect

与大多数全局对象不同 `Reflect` 并非一个构造函数,所以不能通过 [new 运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) 对其进行调用.  
要将 `Reflect` 对象作为一个函数来调用.`Reflect` 的所有属性和方法都是静态的(就像 [Math](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math "Math") 对象)

API:

Reflect.get 方法查找并返回 target 对象的 name 属性,如果没有该属性返回 undefined

```javascript
Reflect.get(target, name, receiver)
```

Reflect.set 方法设置 target 对象的 name 属性等于 value.

```javascript
Reflect.set(target, name, value, receiver)
```

### 实现 mobx observer 观察者模式

```javascript
// func set
const list: Set<Function> = new Set()

// add func in func set
const autorun = (cb: Function) => {
    if (cb) {
        list.add(cb)
    }
}

const observable = <T extends object>(params: T) => {
    return new Proxy(params, {
        set(target, key, value, receiver) {
            const result = Reflect.set(target, key, value, receiver)
            // run funcs in set when object call set function.
            list.forEach(fn => fn())
            console.log(list)
            return result
        }
    })
}

const person = observable({ name: "光头强", attr: "惹我光头强" })

autorun(()=>{
    console.log('熊熊变绵🐏')
})

// console.log("我变化了") when proxy object was changed.
person.attr = '揍你没商量'
```

## Type Guards 类型守卫

在 TypeScript 中,类型守卫(`Type Guards`)是一种用于在 **运行时检查类型** 的机制.它们允许你在代码中执行特定的检查,以确定变量的类型,并在需要时执行相应的操作.

### Typeof 类型收缩

声明一个函数可以接受任意类型,并返回一个 boolean,只筛选出字符串类型,进行类型收缩.

```typescript
const isString = (str: any): boolean => {
  return typeof str === "string"
}

// typeof returns a type represented(vt. 表现) as string
const str = "Hello"
console.log(typeof str) // 输出: "string"

const num = 42
console.log(typeof num) // 输出: "number"

const bool = true
console.log(typeof bool) // 输出: "boolean"
```

typeof 只能返回有限的字符串类型,包括 "string"、"number"、"boolean"、"symbol"、"undefined" 和 "object".对于函数、数组、null 等类型,typeof 也会返回 "object".因此,typeof 对于复杂类型和自定义类型的判断是有限的.

### Instanceof

使用 instanceof 类型守卫可以 **检查一个对象是否是特定类的实例**.

```typescript
const isArr = (value: unknown): number | unknow => {
  if (value instanceof Array) {
    value.length
  }
}
```

`instanceof` 操作符用于**检查一个对象是否是某个类的实例**.它通过 **检查对象的原型链** 来确定对象是否由指定的类创建.

`instanceof` 操作符主要用于检查对象是否是特定类的实例,它 **无法检查基本类型**.此外,它也**无法检查对象是通过字面量创建**的,因为字面量对象没有显式的构造函数.

The left-hand side of an 'instanceof' expression must be of type **'any'**, an **object type** or a **type parameter**.  
'instanceof' 表达式的左侧必须是 'any' 类型,对象类型或类型参数.

```typescript
class Person {
  name: string
  constructor(name: string) {
    this.name = name
  }
}

const person = new Person("Alice")
console.log(person instanceof Person) // 输出: true

const obj = {}
console.log(obj instanceof Person) // 输出: false

const num = new String(12)
console.log(num instanceof String) // true

// 无法判断字面量
const num2: number = 12
console.log((num2 as any) instanceof String) // false
```

### Custom Guard 自定义守卫

实现一个函数支持任意类型:

1. 如果是对象,就检查里面的属性,
2. 如果里面的属性是 number 就取两位,如果是 string 就去除左右空格
3. 如果是函数就执行

编写代码时,需要用到 [类型谓语 (type predicate)](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates) 才能拥有良好的 type hint.

```typescript
// use type predicate
const isString = (str: any): str is string => typeof str === "string"
const isNumber = (num: any): num is number => typeof num === "number"
const isFn = (fn: any) => typeof fn === "function"
const isObj = (obj: any) => ({}).toString.call(obj) === "[object Object]"

const fn = (data: any) => {
  let value
  if (isObj(data)) {
    Object.keys(data).forEach((key) => {
      value = data[key]
      if (isString(value)) {
        data[key] = value.trim()
      }
      if (isNumber(value)) {
        data[key] = value.toFixed(2)
      }
      if (isFn(value)) {
        // 当函数被单独调用时(例如 value()),
        // 函数内部的 this 会指向全局对象(在浏览器环境下是 window)
        // value()
        data[key]()
      }
    })
  }
}
const obj = {
  a: 100.22222,
  b: " test  ",
  c: function () {
    console.log(this.a)
    return this.a
  }
}

fn(obj)
```

## Type compatible[/kəmˈpætəbl/] 类型兼容

类型兼容,就是用于确定一个类型 **是否能赋值给其他的类型**.TS 中的类型兼容性是基于 **结构类型** 的(也就是形状 shape),如果 A 要兼容 B,那么 A 至少具有 B 相同的属性.

### 型变 (协变/逆变) & Duck Type 鸭子类型

> 什么是鸭子类型?  
> 一只鸟 走路像鸭子 ,游泳也像,做什么都像,那么这只鸟就可以成为鸭子类型.  
> -- duck type

A B 两个类型完全不同但是可以赋值并无报错,B 类型充当 A 类型的子类型,当子类型里面的属性满足 A 类型就可以进行赋值,也就是说不能少可以多,这就是协变.

```typescript
interface A {
    name:string
    age:number
}

// 相当于 B 继承自 A
interface B extands A {
    name:string
    age:number
    sex:string
}

let a: A = {
    name: "hyy",
    age: 18,
}

let b: B = {
    name: "yyh",
    age: 30,
    sex: "女"
}

a = b
```

### 逆变

TypeScript 给 JavaScript 添加了一套静态类型系统,是为了保证类型安全的,也就是保证变量只能赋同类型的值,对象只能访问它有的属性、方法.这是类型检查做的事情,遇到类型安全问题会在编译时报错.

子类型是可以赋值给父类型的变量的,可以完全当成父类型来使用,也就是"型变(variant)"(类型改变).

这种"型变"分为两种,一种是子类型可以赋值给父类型,叫做协变(covariant),一种是父类型可以赋值给子类型,叫做逆变(contravariant).

对于 **字面量类型** 采用 **协变** 的变换规则,即 子类型 => 父类型, 看下面,因为 a 具备 string 的可能,没办法直接赋值给 b.但 b 可以赋值给 a, 子类型可以赋值给父类类型, 这就是协变. covariant (variant [/'veərɪənt/] n. 变体 adj. 不同的)

```typescript
// parent
const parent: string | number
// child
// Error: a may be string
const child: number = parent

// correct
const parent = child
```

而对于 **函数的参数类型**,采用完全相反的规则,即**逆变 (contravariant)**,允许父类型赋值给子类型,因为函数中是以父类型进行的约束,赋值一个限制更明确的子类型反而会造成意料之外的问题.

```typescript
let parent = (c: number | string) => {...}
let child = (c: number) => {...}

// correct
child = parent
// error
parent = child

```

协变和逆变在类型体操的时候会常常出现.当然不论是协变还是逆变,它们首先得是 **型变**.型变都是针对父子类型来说的,非父子类型自然就不会型变也就是不变(invariant).ts 中父子类型的判定是按照**结构** 来看的,更具体的那个是子类型.

开启双向协变

tsconfig strictFunctionTypes 设置为 false 支持双向协变 fna fnb 随便可以来回赋值

## TS 泛型工具

**泛型工具** 是一组预定义的泛型类型和操作符,用于**操作和转换类型**.它们可以帮助我们编写更灵活、更通用的代码,并提高代码的可读性和可维护性.

### Partial(adj. 局部的) 和 Required(adj. 必须的)

`Partial`  是一个泛型类型,用于将一个类型的所有属性变为可选.  
与之相反,`Required`  是一个泛型类型,用于将一个类型的所有属性变为必选

```typescript
// Partial: makes all attributes of a type optional
interface User {
  name: string
  age: number
}
type test = Partial<User>
// 转换完成之后的结果
type test = {
  name?: string | undefined
  age?: number | undefined
}

// 原理
type PratialUser<T, K extends keyof T> = {
  [P in K]?: T[P]
}

// Required: makes all attributes of a type required
interface User {
  name?: string
  age?: number
}
// 原理
type CustomRequired<T> = {
  [P in keyof T]-?: T[P]
}

type test = Required<User>
type test2 = CustomRequired<User>

// 结果
interface User {
  name: string
  age: number
}
```

### Pick(选取) 和 Exclude(排除)

`Pick` 用于从一个类型中选取指定的属性.  
`Exclude`  是一个类型操作符,用于从一个类型的属性集合中排除指定的属性.

```typescript
// pick: Used to select the specified property from a type
interface User {
  name?: string
  age?: number
}
// 原理
type CoustomPick<T, K extends keyof T> = {
  [P in K]: T[P]
}

type test = Pick<User, "age">

// 结果
type test = {
  age?: number | undefined
}

// Exclude: Used to exclude a specified property from a type's property collection

// 原理
type CustomExclude<T, K> = T extends K ? never : T
type test = Exclude<"a" | "b" | "c", "a" | "b">

// 结果
type test = "c"
```

### Omit

用于创建一个新类型,该新类型从原始类型中排除指定的属性.

```typescript
interface User {
  address?: string
  name?: string
  age?: number
}
// 原理
type coustomOmit<T, K> = Pick<T, Exclude<keyof T, K>>
type test = Omit<User, "age">

// 结果
type test = {
  address?: string | undefined
  name?: string | undefined
}
```

### Record

**约束一个 object 对象的 key,value.**

`Record` 工具类型接收两个泛型参数 K 和 T,其中:

- K 表示创建的新对象需要具有哪些属性,属性可以只有一个,也可以有多个,多个属性时采用 " 联合类型 " 的写法.
- T 表示对象属性的类型.

```typescript
// record is used to constrain(v. 约束) the key and value of an object.
type Key = "c" | "x" | "k"
type Value = "唱" | "跳" | "rap" | "篮球"

let obj: Record<Key, Value> = {
  c: "唱",
  x: "跳",
  k: "rap"
}

// 原理
// 对象的key 只能是symbol string number 那么keyof any正好获取这三个类型
type CustomRecord<K extends keyof any, T> = {
  [P in K]: T
}

// 支持嵌套约束
let obj: CustomRecord<Key, Record<Key, Value>> = {
  c: {
    c: "唱",
    x: "跳",
    k: "rap"
  },
  x: {
    c: "唱",
    x: "跳",
    k: "rap"
  },
  k: {
    c: "唱",
    x: "跳",
    k: "rap"
  }
}
```

### ReturnType

这个工具主要适用于函数,能够提取函数所返回的类型.

```typescript
const fn = () => [1, 2, 3, "sad"]

type num = ReturnType<typeof fn>
// type num = (string | number)[]

// 原理
type CustomFn<F extends Function> = F extends (...args: any[]) => infer Res
  ? Res
  : never
```

## 类型体操

**类型编程中类型才是一等公民,我们并不能直接传入一个变量或是函数**.需要通过 `typeof` `infer` 将值转换为类型才可.

### Extands (扩展)

extends 关键词它可以保证某个参数**至少具备某个类型**, 也就是子类型是父类型的延伸, 扩展.

e.g. `arr extends unknown[]` 意思就是 arr 至少需要满足 `unknown[]` 的类型, `arr` 是 `unknown[]` 类型的延伸, 扩展.

### Infer 类型推断

infer 关键词只能用于 extends 右侧,他们两个需要联动来使用,它的作用是可以完成**类型的推**导,我们知道在 ts 类型编程中,**类型是一等公民**,而不使用 infer 的时候,restArr 是无法被推导成一个类型的.

简单来说,在类型编程我们就可以使用 `yy extends xx<infer xxx> ? xxx : never` 的方式,提取出和 yy 相关的类型,比如这个例子中的去除第一个元素后,剩下的数组类型.

```typescript
type shiftArr<arr extands unknown[]> = arr extand [unknown, ...infer restArr] ? restArr : never;

type footArr = shiftArr<[1, 2, 3]>
// footArr = [2, 3]
```

### 类型条件判断

在上面提到 extends 可以用来限制参数的类型,extends 关键词它可以保证某个参数**至少具备某个类型**,同样地,我们也可以将 extends 与 **三元符** 来结合,达到类 if 的作用.

以上面的代码为例,`arr extends [unknown, …infer restArr] ? restArr : never` 可以理解成,arr 可以按照 `[unknown, …infer restArr]` 来解析吗?如果可以的得到有效值的话,就返回 restArr, 反之返回 never.

### never(从不) unknown(未知) any(任意) 对对碰

在 ts 中,`never` 表示 **永远不可能存在的值**, **永远不能取得任何值的地方**, 这个类型兜底了所有我们预料之外的情况,并且可以在用户触发这种场景的时候提供编译报错进行报警. 通常在类型编程中,遇到我们 **预料之外** 不知道该如何对应的值时(对应 null), 应该去使用 `never`.

`unknown` 则是 **可以取得任何值**,但是不知道类型的地方.

`any` 大家都很熟悉,使用它将 **丧失所有的类型检查**, 如果不考虑维护性, 做完就跑路, 大可以将 typescript 写成 anyscript.

### 映射类型 (Map Type)

在 TypeScript 中对象、class 对应的类型是 **索引类型**(Index Type),映射类型可以用于修改索引类型,也就是将一个集合映射到另一个集合中,比如下面的例子,我们通过定义 MapType 作为映射类型,就可以很轻松地将一系列对象转化成我们需要的类型.

```typescript
type MapType<T> = {
  [Key in keyof T]: [T[Key], T[Key]]
}

type res = MapType<{ a: 1; b: 2 }>
// res = {a: [1, 1], b: [2, 2]}
```

### 类型提取 (Type Extraction)

使用 `泛型 (generics) / infer (类型推断) / extands` 结合. 这个是类型编程中最为常用的技巧.

提取函数的返回值和参数类型

```typescript
type getFuncParams<func extends Function> = func extends (
  ...parmas: infer params
) => unknown
  ? params
  : never

type getFuncReturnType<func extends Function> = func extends (...args: any) => infer res
  ? res
  : never

const func = (a: string, b: number): string => {
  return "111"
}

type params = getFuncParams<typeof func>
// type params = [a: string, b: number]

type res = getFuncReturnType<typeof func>
// type res = string
```

### 递归处理 (recursion processing)

类型编程所提供的能力并不完全等同于一个普通的编程语言,它是不支持循环的,对于一些需要循环的场景我们不能通过单次类型提取或是直接处理得到我们需要的结果.虽然不支持循环,但是 ts 的类型编程可以**支持递归的实现**,通过这种方式我们也可以处理这一类复杂场景.

如何深度(即也要处理子对象)将一个对象的属性转换为 readonly?

涉及到两个知识点,一个是 **映射类型 (Map type)**,另一个就是 **递归 (Recursion)**,需要先遍历对象的所有 key,并将每个 key 转化为 readonly,对应 key 映射的 value 我们需要做一个判断,如果这个 value 对应 object 类型,那么我们就对这个 value 再执行一遍我们定义的这个类型函数,反之,直接返回.

需要注意的是,这里额外加上了 `T extends any`,是为了触发对 **完整类型** 的计算,ts 只会 **对用到的类型展开计算**,所以不加的话,后续的 `readonly` 会由 `deepReadonly` 代替,而不是深度计算.

```typescript
type deepTransformToReadonly<T extends object> = T extends any
  ? {
      readonly [key in keyof T]: T[key] extends object
        ? deepTransformToReadonly<T[key]>
        : T[key]
    }
  : never

const nestedObj = { a: "12", b: 21, c: { c1: "c1", c2: 1 } }

type nestedObjType = deepTransformToReadonly<typeof nestedObj>

// no T extands any
// type nestedObjType = {
//     readonly a: string;
//     readonly b: number;
//     readonly c: deepTransformToReadonly<{
//         c1: string;
//         c2: number;
//     }>;
// }

// add T extands any
// type nestedObjType = {
//     readonly a: string;
//     readonly b: number;
//     readonly c: {
//         readonly c1: string;
//         readonly c2: number;
//     };
// }
```

递归获取多层 Promise 的返回值.

```typescript
type getPromise<T extends Promise<unknown>> = T extends Promise<infer valueType>
  ? valueType extends Promise<unknown>
    ? getPromise<valueType>
    : valueType
  : never;

type test = getPromise<Promise<Promise<Promise<boolean>>
// type test = boolean
```

递归反转数组, 每次我们反转一个元素和剩下的数组序列,然后对剩下的数组序列执行同样的操作即可.

```typescript
type ReverseArray<T extends any[]> = T extends [infer first, ...infer rest]
  ? [...ReverseArray<rest>, first]
  : T

type reversedArray = ReverseArray<[1, 2, 3, 4]>
// type reversedArray = [4, 3, 2, 1]
```

## End

至此,我应该了解了 `TypeScript` 这个语言的大概,继续学习 Vue 去了.

对于 TypeScript,它是一个很有价值,甚至能对 Pure JavaScript 应用于大型项目中缺陷进行有效弥补的超类.如果遇到阻塞的类型就使用 any,项目的劣化程度只会越来越严重,甚至 typeScript 也会形同虚设.好好地学习一下 typeScript 和类型编程是有意义的.
