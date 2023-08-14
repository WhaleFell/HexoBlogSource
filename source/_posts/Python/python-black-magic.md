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

### Getattr

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

需要注意的是，如果一个类定义了 `__getattr__` 方法，那么该方法只会在访问不存在的属性时被调用。对于已经存在的属性，仍然会直接访问。如果需要在访问任何属性时都进行一些处理，可以使用 `__getattribute__` 方法。

## asyncio.run Vs asyncio.run

[python - asyncio run or run\_until\_complete - Stack Overflow](https://stackoverflow.com/questions/55590343/asyncio-run-or-run-until-complete)

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
loop = asyncio.get_event_loop()
loop.run_until_complete(my_coroutine())
loop.close()

# 使用 asyncio.run()
asyncio.run(my_coroutine())
```

在上面的示例中，`my_coroutine` 是一个简单的协程，它会等待 1 秒钟然后打印一条消息。

在第一个示例中，我们使用 `loop.run_until_complete()` 来运行协程。我们首先通过 `asyncio.get_event_loop()` 获取一个事件循环对象，然后通过 `run_until_complete()` 运行协程。最后，我们手动关闭事件循环。

在第二个示例中，我们使用 `asyncio.run()` 来运行协程。我们只需要调用 `asyncio.run()` 并传入协程，它会自动创建和关闭事件循环。

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
