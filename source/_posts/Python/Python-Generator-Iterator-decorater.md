---
title: Python 迭代器生成器修饰器
date: 2021-08-18 19:37:48
updated: 2021-08-18 19:37:48
categories: Python
tags: [Python, Coding]
description: Python 迭代器生成器修饰器,Python 中三大名器的完全总结！个人总结，算是比较详细的了。
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210630201524.png
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210630201524.png
---

# Python-Generator-Iterator-Decorater

## 迭代器

> **迭代器**指的是迭代取值的工具，迭代是一个重复的过程，并且每次重复都是基于上一次的结果而继续的，
>
> 单纯的重复并不是迭代!

1. 可迭代对象

   元组; 列表; 字典; 集合; 字符串等

   生成器.

2. 用 for 循环迭代
3. 验证一个东西是否可迭代

   > **可迭代不一定是迭代对象**
   >
   > (列表不是可迭代对象,但是可迭代.)

   ```python
   from collections import Iterable
   # 判断是否是可迭代对象(迭代器)
   isinstance("adb",Iterable) # instance:实例
   ```

4. 生成器可迭代,但可以迭代的不一定是生成器
5. `iter()` 可以将列表变成可迭代对象

### 迭代器的作用

迭代器是用来**迭代取值**的工具，而涉及到把多个值循环取出来的类型

> 有：列表、字符串、元组、字典、集合、打开文件

```python
l = ['egon', 'liu', 'alex']
i = 0
while i < len(l):
    print(l[i])
    i += 1
```

上述**迭代取值的方式**只适用于**有索引**的数据类型：**列表、字符串、元组.**  
为了解决基于索引迭代器取值的局限性  
python 必须提供一种能够不**依赖于索引的取值方式**，这就是**迭代器**.

### 迭代器详解

但凡内置有 `__iter__()` 方法的对象都称之为**可迭代的对象**:

```python
s1=''
s1.__iter__()

l=[]
l.__iter__()

t=(1,)
t.__iter__()

d={'a':1}
d.__iter__()

set1={1,2,3}
set1.__iter__()

with open('a.txt',mode='w') as f:
    f.__iter__()
    pass
```

**迭代器对象**:

内置有 `__next__` 方法并且内置有 `__iter__` 方法的对象  
迭代器对象 `__next()__`：得到迭代器的下一个值.  
迭代器对象 `__iter__()`：得到迭代器的本身，调用和没调用相同.

```python
d={'a':1,'b':2,'c':3}
d_iterator=d.__iter__()
print(d_iterator)    # <dict_keyiterator object at 0x0000022FF0E13A90>

print(d_iterator.__next__()) # a
print(d_iterator.__next__()) # b
print(d_iterator.__next__()) # c
print(d_iterator.__next__()) # 抛出异常StopIteration,值取完了
d={'a':1,'b':2,'c':3}
d_iterator=d.__iter__()

while True:
    try:
        print(d_iterator.__next__())
    except StopIteration:
        break
# 得到"a","b","c"
print('====>>>>>>') # 在一个迭代器取值取完后，再对其取值就会报错
d_iterator=d.__iter__()
while True:
    try:
        print(d_iterator.__next__())
    except StopIteration:
        break
# 什么都得不到,因为迭代器值已取完


l=[1,2,3,4,5]
l_iterator=l.__iter__()

while True:
    try:
        print(l_iterator.__next__())
    except StopIteration:
        break
# 得到1,2,3,4,5
```

常见**数据类型**的分类:

> 可迭代对象：**字符串、列表、元组、字典、集合、文件对象**  
> 迭代器对象：**文件对象**

```python
s1=''
s1.__iter__()

l=[]
l.__iter__()

t=(1,)
t.__iter__()


d={'a':1}
d.__iter__()

set1={1,2,3}
set1.__iter__()


with open('a.txt',mode='w') as f:
    f.__iter__()
    f.__next__()
```

### 创建一个迭代器

把一个类作为一个迭代器使用需要在类中实现两个方法 `iter()` 与 `next()` 。

1. `iter()` 方法返回一个特殊的迭代器对象,这个迭代器对象实现了 `next()` 方法并通过 `StopIteration` 异常标识迭代的完成。
2. `next()` 方法会返回**下一个迭代器对象**。
3. **StopIteration**

   `StopIteration` 异常用于标识**迭代的完成**，防止出现无限循环的情况，在 **next**() 方法中我们可以设置在完成指定循环次数后触发 `StopIteration` 异常来结束迭代。

> 创建一个返回数字的迭代器，初始值为 1，逐步递增 1

```python
class MyNumbers:
  def __iter__(self):
    # 初始化迭代器
    self.a = 1
    return self

  def __next__(self):
    # 迭代器返回值
    if self.a <= 20:
      x = self.a
      self.a += 1
      return x
    else:
      raise StopIteration

myclass = MyNumbers()
myiter = iter(myclass)

for x in myiter:
  print(x)

# fun2:
myiter = iter(myclass)
print(next(myiter))
print(next(myiter))
print(next(myiter))
print(next(myiter))
print(next(myiter))
```

### For 循环的工作原理

for 循环可以称之为叫**迭代器循环**

```json
d = {'a':1,'b':2,'c':3}
```

1. `d.iter()` 得到一个迭代器对象
2. `迭代器对象.next()` 拿到一个返回值，然后将该返回值赋值给 k
3. 循环往复,直到抛出 `StopIteration` 异常**for 循环会捕捉异常然后结束循环.**

```python
d = {'a':1,'b':2,'c':3}
for k in d:   # d_it = d.__iter__()
    print(k)  # 循环k = d_it.__next__()直到抛出异常结束循环

with open('a.txt',mode='rt',encoding='utf-8') as f:
    for line in f: # f.__iter__()
        print(line)

list('hello') # 原理同for循环
```

## 生成器

在 Python 中，使用了 `yield` 的函数被称为**生成器**（generator）。

跟普通函数不同的是，生成器是**一个返回迭代器的函数**，只能用于**迭代操作**，更简单点理解**生成器就是一个迭代器**。

在**调用生成器**运行的过程中，每次遇到 `yield` 时函数会**暂停并保存当前所有的运行信息**，**返回 ​**​**`yield`**​**​ 的值**, 并在下一次执行 `next()` 方法时**从当前位置继续运行**。

**调用一个生成器函数，返回的是一个迭代器对象。**

### 生成器的使用

```python
def func():
    print('第一次')
    yield 1
    print('第二次')
    yield 2
    print('第三次')
    yield 3
    print('第四次')

g = func()
print(g)
# 生成器就是迭代器
# 生成器函数返回的是可迭代对象.
g.__iter__()

# g.__next__()会触发函数体代码的运行，然后遇到yield停下来，将yield后的值
# 当做本次调用的结果返回
g = func()
# 可以用iter将可迭代对象转化为迭代器.用next()方法
print(g)

g.__iter__()

res1 = g.__next__()
# 第一次
print(res1)
# 1

res2 = g.__next__()
# 第二次
print(res2)
# 2

res3 = g.__next__()
# 第三次
print(res3)
# 3

res4 = g.__next__()
# 第四次
# StopIteration
```

实例: 使用 `yield` 实现斐波那契数列：

```python
def fibonacci(n):  # 生成器函数 - 斐波那契
    a, b, counter = 0, 1, 0
    while True:
        if (counter > n):
            return
        yield a
        a, b = b, a + b
        counter += 1


f = fibonacci(10)  # f 是一个迭代器，由生成器返回生成

while True:
    try:
        print(next(f), end=" ")
    except StopIteration:
        break
```

### `yield` 关键字

yield 关键字的作用是**函数与调用者**的通信，它不光能够将**值返回给调用者**，它还可以**接收调用者传过来的值**，那么怎么实现呢？ 调用者通过 `send()` 函数将值传递给**generator**，**generator**通过 `yield` 前面的变量 (形如:`n = yield)` 来接收

```python
>>> def test():
 number = 0
 while True:
  n = yield number
  print('我是yield关键字下面一行代码')
  print('调用者传递过来的值为:', n)


>>> t = test()
>>> t.send(None)
# 当第一次执行生成器的时候，需要传入None

>>> t.send(1)
我是yield关键字下面一行代码
调用者传递过来的值为: 1

>>> t.send(2)
我是yield关键字下面一行代码
调用者传递过来的值为: 2
t.close() 关闭generator后再调用，会出现StopIteration异常
>>> next(t)
我是yield关键字下面一行代码
调用者传递过来的值为: None
```

> 可以发现，`send` 函数和 `next` 函数的区别就是 `send` 函数可以在执行 generator 的**过程中**，给**generator**发送消息。**而 next 仅仅是接收 yield 右边的变量值**。

### `send()`​**方法用法**

1. **send 过来的数值 = yield next 出来的数值**
2. 它的主要作用是，当我需要**手动更改生成器里面的某一个值并且使用它**，则**send 发送进去一个数据**，然后**保存到 yield 语句的返回值**，以提供使用.
3. `send()` 的返回值就是那个**本来应该被迭代出来的那个值**。这样既可以**保证我能够传入新的值，原来的值也不会弄丢.**
4. 实例:

   ```python
   def my_generator(n):
       '''定义一个生成器'''
       for i in range(n):
           # send来的数值 = yield 数值
           num = yield i
           print(f'传入的{num}')

   g = my_generator(5)
   print(next(g))
   print(next(g))
   a = g.send(100)
   print('我是{0}'.format(a))
   print(next(g))
   print(next(g))
   ''' 结果
   0
   传入的None
   1
   传入的100
   我是2
   传入的None
   3
   传入的None
   4
   '''
   ```

### `throw()` 方法用法

> 简单来说就是:**向生成器扔去一个异常,使生成器停止.**

`raise exception in generator，return next yielded value or StopIteration`，即在**生成器中抛出异常**，并且这个 throw 函数会**返回下一个要迭代的值**或者是**StopIteration**。还是通过几个例子来看吧！

```python
def my_generator():
    yield 'a'
    yield 'b'
    yield 'c'
g = my_generator()
print(next(g))
print(next(g))
print('-------------------------')
print(g.throw(StopIteration))
print(next(g))
'''运行结果为：
a
b
-------------------------
StopIteration
'''
```

### **生成器的启动与关闭 close**

1. **生成器的启动**

   **方法 1：**​\***\*直接使用\*\***​\***\*`next(g)`\*\***​\***\*，这会\*\***​**直接开始迭代第一个元素**（**推荐使用这个启动**）

   **方法 2：**​\***\*使用\*\***​\***\*`g.send(None)`\*\***​\***\*进行启动，注意\*\***​**第一次启动的时候只能传入**​**`None`**，如果传入其他具体的指则会**报错**哦！

   ```python
   def my_generator():
       yield 1
       yield 2
       yield 3
       yield 4

   g = my_generator()
   g.send(None)   #第一次启动，本来第一次应该迭代的1，这里被取代了，但是send(None)会返回1
   print(next(g))
   print(next(g))
   print(next(g))

   '''运行结果为：
   2
   3
   4
   '''
   ```

2. **生成器的关闭**

   如果一个生成器被**中途关闭**之后，在此调用 `next()` 方法，则会显示错误，如下：

   ```python
   def my_generator():
       yield 1
       yield 2
       yield 3
       yield 4

   g = my_generator()
   print(next(g))
   print(next(g))
   g.close()
   print(next(g))   #在此处会显示错误
   print(next(g))
   '''运行结果为：
   1
   2
   显示StopIteration
   '''
   ```

### **生成器的终止迭代——**​**`StopIteration`**

> 前面讲的手动关闭生成器，使用 `close()` 方法，后面的迭代会抛出 `StopIteration` 异常。

在一个生成器中，如果没有 `return`，则默认执行到函数完毕时返回 `StopIteration`:

```python
def g1():
    yield 1
g = g1()
next(g)    #第一次调用next(g)时，会在执行完yield语句后挂起，所以此时程序并没有执行结束。
next(g)    #程序试图从yield语句的下一条语句开始执行，发现已经到了结尾，所以抛出StopIteration异常。
'''运行结果为：
1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
'''
```

如果遇到 `return`,如果在执行过程中 return，则直接抛出 `StopIteration` 终止迭代:

```python
def g2():
    yield 'a'
    return
    yield 'b'
g=g2()
next(g)    #程序停留在执行完yield 'a'语句后的位置。
next(g)    #程序发现下一条语句是return，所以抛出StopIteration异常，这样yield 'b'语句永远也不会执行。
'''运行结果为：
a
b
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
'''
```

如果在 `return` 后返回一个值，那么这个值为 `StopIteration` 异常的说明，不是程序的返回值。

```python
def g3():
 yield 'a'
 return '这是错误说明'
 yield 'b'   #有一些编辑器会提示错误，此处为unreachable code，即不可到达的代码
g=g3()
next(g)
next(g)
'''运行结果为：
a
Traceback (most recent call last):
 File "<stdin>", line 1, in <module>
StopIteration: 这是错误说明
'''
```

> **注意：**​\***\*生成器没有办法使用\*\***​\***\*`return`\*\***​\***\*来返回值。因为\*\***​\***\*`return`\*\***​\***\*返回的那个值是通过\*\***​**StopIteration 的异常信息**返回的，所以没办法**直接获取这个 return 返回的值**。

当然上面所说的无法获取**return 返回值**，我们指的是没有办法通过 `result=g3()` 这种形式获取**return 的返回值**。实际上还是有手段获取这个**return 的值的**，有两种方法：

1. 使用 `yield from` 语句
2. 因为 `return` 返回的值是作为**StopIteration 的一个 value 属性存在的**，StopIteration 本质上是**一个类**，所以可以通过访问它的**value 属性获取这个 return 返回的值**。使用下面的代码：

   ```python
   def g3():
       yield 'a'
       return '这是错误说明'
       yield 'b'
   g=g3()

   try:
       print(next(g))  #a
       print(next(g))  #触发异常
   except StopIteration as exc:
       result=exc.value
       print(result)
   '''运行结果为：
   a
   这是错误说明
   '''
   ```

### **`yield`**​**实现协程**

在 Python 中，协程通过 `yield` 实现。因为当**一个函数中有 yield 存在**的时候，这个函数是**生成器**，那么当你调用这个函数的时候，你在函数体中写的代码**并没有被执行**，而是**只返回了一个生成器对象**，这个需要特别注意。然后，你的代码将会在每次使用这个生成器的时候被执行。

前面讲过**yield 表达式**的两个关键作用：**①返回一个值、②接收调用者的参数**

**“调用者”与“被调用者”**之间的通信是通过 `send()` 进行联系的

正是因为 `yield` 实现的生成器具备**“中断等待的功能”**，才使得 yield 可以实现**协程**。

1. 例子 1:**生产者 - 消费者模型**

   ```python
   def consumer():
       r = ''
       while True:
           n = yield r  #执行的中断点
           if not n:
               return
           print('[消费者] 正在消费:{0}'.format(n))
           r = '200 人民币'

   def produce(c):
       c.send(None)  #启动消费者（生成器）——实际上是函数调用，只不过生成器不是直接象函数那般调用的
       n = 0
       while n < 5:
           n = n + 1
           print('[生产者] 正在生产:{0}'.format(n))
           r = c.send(n) #给消费者传入值——实际上也是函数调用
           print('[生产者] 消费者返回:{0}'.format(r))
           print('-------------------------------------------------')
       c.close()

   c = consumer()#构造一个生成器
   produce(c)

   '''运行结果为：
   [生产者] 正在生产:1
   [消费者] 正在消费:1
   [生产者] 消费者返回:200 人民币
   -------------------------------------------------
   [生产者] 正在生产:2
   [消费者] 正在消费:2
   [生产者] 消费者返回:200 人民币
   -------------------------------------------------
   [生产者] 正在生产:3
   [消费者] 正在消费:3
   [生产者] 消费者返回:200 人民币
   -------------------------------------------------
   [生产者] 正在生产:4
   [消费者] 正在消费:4
   [生产者] 消费者返回:200 人民币
   -------------------------------------------------
   [生产者] 正在生产:5
   [消费者] 正在消费:5
   [生产者] 消费者返回:200 人民币
   -------------------------------------------------
   '''
   ```

协程其实就是一个**可以暂停执行**的函数，并且**可以恢复继续执行**。那么 `yield` 已经可以**暂停执行**了，如果在暂停后有办法把一些 `value` 发回到**暂停执行的函数**中，那么 Python 就有了**『协程』**。于是有了, **“把东西发送到已经暂停的生成器中”** 的方法，这个方法就是 `send()`。

### `yield from` 的简单实现

yield 是每次**“惰性返回”一个值**，其实从名字中就能看出，`yield from` 是 `yield` 的升级改进版本，如果将**yield 理解成“返回”**，那么 yield from 就是**“从什么（生成器）里面返回”**，这就构成了 `yield from` 的一般语法，即:

```python
yield from 生成器()
```

**其实她等同于:**

```python
for item in 生成器():
    yield item
```

这样的形式。我们通过一个简单例子来看：

```python
def generator2():
    yield 'a'
    yield 'b'
    yield 'c'
    yield from generator1() #yield from iterable本质上等于 for item in iterable: yield item的缩写版
    yield from [11,22,33,44]
    yield from (12,23,34)
    yield from range(3)

for i in generator2():
    print(i,end=' , ')
'''运行的结果为：
a , b , c , 0 , 1 , 2 , 3 , 4 , 5 , 6 , 7 , 8 , 9 , 11 , 22 , 33 , 44 , 12 , 23 , 34 , 0 , 1 , 2 ,
'''
```

`yield from` 后面可以跟的可以是 **“ 生成器 、元组、 列表、range() 函数产生的序列等可迭代对象”**

简单地说,`yield from  generator` 。实际上就是返回**另外一个生成器**。而 `yield` 只是返回**一个元素**。从这个层面来说，有下面的等价关系：**yield from iterable 本质上等于 for item in iterable: yield item 。**

### `yield from` 的高级应用

#### **1. 针对 Yield 无法获取生成器 return 的返回值**

> 在使用**yield 生成器**的时候，如果使用**for 语句去迭代生成器**，则不会显式的发出**StopIteration 异常**，而是**自动捕获 StopIteration 异常**，所以如果**遇到 return，只是会终止迭代，而不会触发异常**，故而也就**没办法获取 return 的值**。如下：

```python
def my_generator():
    for i in range(5):
        if i==2:
            return '我被迫中断了'
        else:
            yield i

def main(generator):
    try:
        for i in generator:  #不会显式触发异常，故而无法获取到return的值
            print(i)
    except StopIteration as exc:
        print(exc.value)

g = my_generator()  #调用
main(g)
'''运行结果为：
0
1
'''
```

**for 迭代语句不会显式触发异常**，故而无法获取到**return 的值**，迭代到 2 的时候**遇到 return 语句**，**隐式的触发了 StopIteration 异常**，就**终止迭代**了，但是在程序中**不会显示出来**。

如果是使用 `next()`​**一次一次迭代**，则会**显式触发异常**，但要获取 return 的返回值，我需要如下操作：

```python
def my_generator():
    for i in range(5):
        if i==2:
            return '我被迫中断了'
        else:
            yield i

def main(generator):
    try:
        print(next(generator))   #每次迭代一个值，则会显式出发StopIteration
        print(next(generator))
        print(next(generator))
        print(next(generator))
        print(next(generator))
    except StopIteration as exc:
        print(exc.value)     #获取返回的值

g=my_generator()
main(g)
'''运行结果为：
0
1
我被迫中断了
'''
```

现在我们使用 `yield from` 来完成上面的同样的功能：

```python
生成器中return的值 = yield from 生成器对象
```

```python
def my_generator():
    for i in range(5):
        if i == 2:
            # 遇到return自动触发StopIteration异常
            return '我被迫中断了'
        else:
            yield i

def wrap_my_generator(generator):  # 定义一个包装“生成器”的生成器，它的本质还是生成器
    result = yield from generator    # 自动触发StopIteration异常，并且将return的返回值赋值给yield from表达式的结果，即result
    print(result)

def main(generator):
    for j in generator:
        print(j)

g = my_generator()
wrap_g = wrap_my_generator(g)
main(wrap_g)  #调用
'''运行结果为：
0
1
我被迫中断了
'''
```

从上面的比较可以看出，`yield from` 具有以下几个**特点**：

1. **单独**​**`yield`**​**时**：的 my_generator 是原始的生成器，main 是调用方，使用 yield 的时候，只涉及到这两个函数，即“调用方”与“生成器（协程函数）”是**直接进行交互的，不涉及其他方法**，即**“调用方——>生成器函数 (协程函数)”；**
2. **在使用**​**`yield from`**​**的时**：多了一个对原始 my_generator 的**包装函数**，然后**调用方是通过这个包装函数**（后面会讲到它专有的名词）来与**生成器进行交互**的，即**“调用方——>生成器包装函数——>生成器函数 (协程函数)”**；
3. `yield from iteration` 结构会在内部**自动捕获 iteration 生成器的 StopIteration 异常**。这种处理方式与 for 循环处理 StopIteration 异常的方式一样。而且对 `yield from` 结构来说，**解释器不仅会捕获 StopIteration 异常，还会把 return 返回的值或者是 StopIteration 的 value 属性的值变成 yield from 表达式的值，即上面的 result。**

> 委派生成器：包含 `yield from` <iterable> **表达式的生成器函数**；即上面的 wrap_my_generator**生成器包装函数**

> 子生成器：从 `yield from` 表达式中 <iterable> 部分获取的生成器；即上面的 my_generator**生成器函数**

> 调用方：**调用委派生成器的客户端代码**；即上面的**main 调用方函数**

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210814184753.png&webp=true)

#### 2. Yield from 的管道传输用法示例

其实 `yield from` 最重要的作用就是提供了一个**“数据传输的管道”**，下面通过一个简单的例子加以说明为什么是管道：

```python
def average():
    total = 0.0  #数字的总和
    count = 0    #数字的个数
    avg = None   #平均值
    while True:
        num = yield avg
        total += num
        count += 1
        avg = total/count

def wrap_average(generator):
    yield from generator

#定义一个函数，通过这个函数向average函数发送数值
def main(wrap):
    print(next(wrap))  #启动生成器
    print(wrap.send(10))  # 10
    print(wrap.send(20))  # 15
    print(wrap.send(30))  # 20
    print(wrap.send(40))  # 25

g = average()
wrap = wrap_average(g)
main(wrap)

'''运行结果为：
None
10.0
15.0
20.0
25.0
'''
```

从上面我们可以发现，调用方发送的数据是发给 `wrap_average` 的，怎么依然到了生成器函数 `average` 里面呢？这就是**“数据传输管道的作用”**。即**主函数调用方 main 把各个 value 传给 grouper** ，而这个传入的值**最终到达 averager 函数中**； **grouper 并不知道传入的是什么值**，因为从上面的代码看出，**`wrap_average`**​**里面完全没有处理这个值的任何代码！**

## 装饰器

### 函数引用

```python
def test():
    print("1")
print(test)
# <function __main__.test>
a = test # 变量A指向函数对象
a() # 变量A可调用函数
```

### 什么是闭包

> **在函数内部再定义一个函数，并且这个函数用到了外边函数的变量，那么将这些函数以及用到的一些变量称之为闭包。**

### 闭包的应用

```python
def parse(number: int):
    print("第一层函数", number)

    def sum(number2):
        print("第二层函数加和后", number+number2)
    return sum
# 接受函数对象的引用
func = parse(100)
func(100)
```

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210719211704.png&webp=true)

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210719215011.png&webp=true)

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210719215459.png&webp=true)

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210719215842.png&webp=true)

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210719220126.png&webp=true)

```python
# 编写闭包函数
def checkLogin(func):
    print("准备检查权限")

    def inner():
        print("权限正确!")
        func()  # 执行函数的变量

    return inner  # 返回一个函数对象

# main变量指向的是mian函数
def main():
    print("执行业务1")

# 不用修改主函数的内容,就可以扩展主函数.
main = checkLogin(main)  # 此时main变量指向的是inner函数
main()
# 等效于
# 到这里python会运行 checkLogin函数,并把下面的函数名变量指向 checkLogin 函数的返回值,即 inner 函数
@checkLogin
def mian():
    print("执行业务2")

# 在调用main之前已经被装饰了
mian()
```

### 多个修饰器，从下到上修饰

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210720161511.png&webp=true)

> **装饰是倒着装的，执行是顺着执行的。**

### 装饰有参数的函数

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210720162540.png&webp=true)

> 对不定长参数的传递

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210720162754.png&webp=true)

### 装饰有返回值的参数

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210720163328.png&webp=true)

### 通用修饰器

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210720164146.png&webp=true)

### 带参数的修饰器

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210818144548.png&webp=true)

> 在函数中嵌套闭包函数
