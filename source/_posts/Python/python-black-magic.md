---
title: Python 黑魔法
date: 2023-08-11 10:51:51
updated: 2023-08-11 10:51:51
categories: Python
tags: [coding, Python, 高级编程]
description:
thumbnail:
banner_img:
---

# Python 黑魔法

记录一些 Python 语言独有的，不太常见的写法，方便进行库的开发。一般我在读源码的时候可以碰见很多没有见过的魔术方法和神奇实现，所以把它记录下来。

## Getattr (get attribute 获取属性)

source code: [Pyrogram Conversation-Pyrogram](https://github.com/Ripeey/Conversation-Pyrogram/blob/main/src/convopyro/__init__.py)

### getattr

`getattr()` 是 Python 的一个内置函数，用于获取对象的属性值。它接受两个参数：对象和属性名，并返回属性值。

语法：

```python
getattr(object, name[, default])
```

- object: 要获取属性值的对象。
- name: 属性名。
- default (可选): 如果属性不存在，则返回 default 值（默认为 None）。

示例：

```python
class MyClass:
    def __init__(self):
        self.foo = 42

obj = MyClass()
print(getattr(obj, 'foo'))  # 输出: 42
print(getattr(obj, 'bar', 'default value'))  # 输出: default value
```

在上述示例中，通过 `getattr(obj, 'foo')` 获取了对象 `obj` 的属性 `foo` 的值，返回结果为 42。而通过 `getattr(obj, 'bar', 'default value')` 获取了对象 `obj` 的属性 `bar` 的值，由于该属性不存在，所以返回了指定的默认值 'default value'。

`getattr()` 的特点是可以在 **运行时动态地获取属性值**，而不需要提前知道属性的名称。这在处理需要根据不同条件获取不同属性值的情况下非常有用。

### `__getattr__` 魔术方法

在类中定义 `__getattr__`  方法可以实现自定义的属性访问逻辑。当访问一个不存在的属性时，Python 解释器会自动调用  `__getattr__` 方法，传入属性名作为参数，并期望返回一个值。

以下是一个示例：

```python
class MyClass:
    def __getattr__(self, name):
        print(f'Accessing attribute {name}')
        return 42

obj = MyClass()
print(obj.foo)  # 输出: Accessing attribute foo
                #      42
```

在上述示例中，当访问 obj.foo 属性时，由于该属性不存在，Python 解释器会调用  `__getattr__` 方法，并将属性名 "foo" 作为参数传入。 `__getattr__` 方法打印了一条消息并返回了 42。

需要注意的是，如果一个类定义了 `__getattr__` 方法，那么该方法只会在访问不存在的属性时被调用。对于已经存在的属性，仍然会直接访问。如果需要在访问任何属性时都进行拦截，可以使用 `__getattribute__` 方法。

## loop.run_until_complete() Vs asyncio.run

[python - asyncio run or run_until_complete - Stack Overflow](https://stackoverflow.com/questions/55590343/asyncio-run-or-run-until-complete)

`loop.run_until_complete()` 是用于在一个已存在的事件循环中运行一个协程，它会一直运行直到协程完成或抛出异常。这个函数在 Python 3.7 之前是主要的运行协程的方法。

而 `asyncio.run()` 是在 Python 3.7 中引入的一个方便的函数，它会创建一个新的事件循环并在其中运行一个协程。它会一直运行直到协程完成或抛出异常，并且在协程完成后会自动关闭事件循环。

使用 `loop.run_until_complete()` 时，你需要先创建一个事件循环对象，并在使用完后手动关闭它。而使用 `asyncio.run()` 时，你不需要手动创建和关闭事件循环，它会自动处理这些细节。

以下是使用 `loop.run_until_complete()` 和 `asyncio.run()` 的示例：

Copy

```python
import asyncio

async def my_coroutine():
    await asyncio.sleep(1)
    print("Coroutine completed")

# 使用 loop.run_until_complete()
loop = asyncio.get_event_loop() # get new event loop
loop.run_until_complete(my_coroutine()) # run coroutine
loop.close() # close event loop

# 使用 asyncio.run()
asyncio.run(my_coroutine())
```

总结来说，`loop.run_until_complete()` 适用于在已存在的事件循环中运行协程，而 `asyncio.run()` 适用于创建一个新的事件循环并运行协程，并且它能够自动处理事件循环的创建和关闭。在 Python 3.7 及更高版本中，推荐使用 `asyncio.run()` 来运行协程。

## @contentmanager

在 Python 中，`@contextmanager` 是一个装饰器，用于创建上下文管理器。上下文管理器是一个对象，它定义了在进入和退出上下文时要执行的代码块。通常，上下文管理器用于管理资源的分配和释放，例如打开和关闭文件，获取和释放锁等。

`@contextmanager` 装饰器可以应用于一个生成器函数，该函数使用 `yield` 语句来定义进入和退出上下文时的操作。被装饰的生成器函数必须返回一个上下文管理器对象。

当使用装饰器 `@contextmanager` 装饰一个生成器函数时，该函数将被转换为一个上下文管理器对象。装饰器会自动处理进入和退出上下文的逻辑，并在进入上下文时返回生成器的迭代器，以便在进入和退出上下文时执行相应的代码。

下面是一个使用 `@contextmanager` 装饰器的示例：

```python
from contextlib import contextmanager

@contextmanager
def file_manager(filename):
    file = open(filename, 'w')
    try:
        yield file
    finally:
        file.close()

with file_manager('example.txt') as f:
    f.write('Hello, World!')
```

在上面的示例中，`file_manager` 函数被装饰为上下文管理器。在 `with` 语句块中，文件 `example.txt` 被打开，并在退出上下文时自动关闭。在 `yield` 语句之前的代码块表示进入上下文时要执行的操作，`yield` 语句之后的代码块表示退出上下文时要执行的操作。在 `with` 语句块中，可以使用 `f` 变量来访问文件对象，并在其中执行相应的操作。

使用 `@contextmanager` 装饰器可以简化上下文管理器的创建过程，使代码更加简洁和可读。

## 异步 sqlite3

```shell
pip install aiosqlite
```

[GitHub - omnilib/aiosqlite: asyncio bridge to the standard sqlite3 module](https://github.com/omnilib/aiosqlite)

## Python enum 枚举类型

[enum --- 枚举类型支持 — Python 3.7.13 文档](https://docs.python.org/zh-cn/3.7/library/enum.html)

## AsyncGenerator func hint

异步生成器、异步迭代器函数注解：

`AsyncGenerator` 类型应该使用 `AsyncGenerator[YieldType, SendType]` 的形式来表示，其中 `YieldType` 是生成器产出的值的类型，而 `SendType` 是使用 `yield` 语句发送给生成器的值的类型。

```python
from typing import AsyncIterator, AsyncGenerator  # 异步迭代器 异步生成器

# FastAPI Dependent
async def get_session() -> AsyncGenerator[AsyncSession, None]:
    async with AsyncSessionMaker() as session:
        yield session
```

## @dataclass 装饰器

`@dataclass` 是 Python 3.7 中引入的一个装饰器，用于简化数据类的定义。数据类是一种用于存储数据的类，通常包含一些属性和方法，用于表示和操作数据。

```python
# Python 3.7+ frozen: not allow to change value
@dataclass(frozen=True)
class Person(object):
    first_name: str
    last_name: str
    age: int
```

## Python 3.10 新特性

海象运算符 `:=` (walrus operator) 是 Python 3.8 中引入的一个新特性，它允许在 **表达式中同时进行赋值和比较操作**。在 Python 3.10 中，海象运算符的使用范围得到了扩展，可以在 `if` 语句中使用海象运算符。其实借鉴了 Go 语言的写法。这使得代码更加简洁，并且避免了引入不必要的临时变量。

before:

```python
data = get_data()
if data is not None:
    print(len(data))
```

now:

```python
if (data := get_data()) is not None:
    print(len(data))
```

对某些需要跳出 while 循环的情况, 尤其是分片读取文件时, 这个特性非常有用。

```python
import aiofiles
from fastapi import UploadFile

# 默认分片大小 50MB
DEFAULT_CHUNK_SIZE = 1024 * 1024 * 50  # 50 megabytes

# 使用海象运算符
async def save_video(video_file: UploadFile):
  async with aiofiles.open("/file/path/name.mp4", "wb") as f:
    # while 直到 chunk 为空
    while chunk := await video_file.read(DEFAULT_CHUNK_SIZE):
      await f.write(chunk)

# 不使用海象运算符
async def save_video(video_file: UploadFile):
  async with aiofiles.open("/file/path/name.mp4", "wb") as f:
    while True:
      chunk = await video_file.read(DEFAULT_CHUNK_SIZE)
      # skip loop if chunk is empty
      if not chunk:
        break
      await f.write(chunk)

```
