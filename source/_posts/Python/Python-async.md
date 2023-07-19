---

title: Python asyncio 异步协程百万并发
date: 2021-08-18 19:37:48
updated: 2021-08-18 19:37:48
categories: Python
tags: [Python, Coding]
description: Python 异步协程百万并发
thumbnail: http://oss.whaleluo.top/blog/img/Python-Asyncio-banner.png-picsmall
banner_img: http://oss.whaleluo.top/blog/img/Python-Asyncio-banner.png-picsmall
excerpt: Python 异步百万并发全文最详细笔记！！

---

![banner](http://oss.whaleluo.top/blog/img/Python-Asyncio-banner.png-picsmall)

# Python Asyncio 异步协程百万并发

## 协程（coroutine）

本质就是一个 **函数**

## **事件循环**——(event_loop)

**协程函数**，不是像普通函数那样直接调用运行的，必须**添加到事件循环**中，然后由**事件循环**去运行，单独运行协程函数是不会有结果的，看一个简单的例子：

```python
import time
import asyncio
async def say_after_time(delay,what):
        await asyncio.sleep(delay)
        print(what)
 
async def main():
        print(f"开始时间为： {time.time()}")
        await say_after_time(1,"hello")
        await say_after_time(2,"world")
        print(f"结束时间为： {time.time()}")
 
loop=asyncio.get_event_loop()    #创建事件循环对象
# loop=asyncio.new_event_loop()   #与上面等价，创建新的事件循环
loop.run_until_complete(main())  #通过事件循环对象运行协程函数
loop.close()
```

在 python3.6 版本中，如果我们单独像执行普通函数那样执行一个协程函数，只会返回一个 coroutine 对象（python3.7）如下所示：

```python
>>> main()
<coroutine object main at 0x1053bb7c8>
```

### 获取事件循环对象的几种方式

```
loop = asyncio.get_event_loop()
```

它是 python3.7 中新添加的，**获得一个事件循环**，如果当前线程**还没有事件循环**，则**创建一个新的事件循环 loop；**

### 通过事件循环运行协程函数的两种方式

1. 创建事件循环对象 loop，即 `asyncio.get_event_loop()`，**通过事件循环运行协程函数**.
2. 直接通过 `asyncio.run(function_name)` 运行**协程函数。**

   > **但是需要注意的是，首先 run 函数是 python3.7 版本新添加的，前面的版本是没有的；**
   >

其次，这个 run 函数总是**会创建一个新的事件循环并在 run 结束之后关闭事件循环**，所以，如果在**同一个线程**中已经有了一个事件循环，则**不能再使用这个函数**了，因为**同一个线程不能有两个事件循环**，而且这个 run 函数**不能同时运行两次**，因为他已经创建一个了。即**同一个线程中是不允许有多个事件循环 loop 的**。

## Task 任务

### 创建任务（两种方法）

1. `task = asyncio.create_task(coro())`

   > **这是 3.7 版本新添加的**,**可以传协程函数**
   >

2. `task = asyncio.ensure_future(coro())`
3. 也可以：

   ```python
   loop.create_future()
   loop.create_task(coro)
   ```

### 获取某一个任务的方法

1. `task=asyncio.current_task(loop=None)`

   > 返回在某一个指定的 loop 中，**当前正在运行的任务**，**如果没有任务正在运行，则返回 None**；  
   > 如果 loop 为 None，**则默认为在当前的事件循环中获取**.
   >

2. `asyncio.all_tasks(loop=None)`

   > 返回某一个**loop 中还没有结束的任务**
   >

## 异步函数的结果获取

对于异步编程、异步函数而言，最重要的就是**异步函数调用结束之后，获取异步函数的返回值**，我们可以有以下几种方式**来获取函数的返回值**，第一是直接通过 `Task.result()` 来获取；第二种是**绑定一个回调函数**来获取，**即函数执行完毕后调用一个函数来获取异步函数的返回值。**

1. 直接通过 `result` 来获取.

   ```python
   import asyncio
   import time


   async def hello1(a,b):
       print("Hello world 01 begin")
       await asyncio.sleep(3)  #模拟耗时任务3秒
       print("Hello again 01 end")
       return a+b

   coroutine=hello1(10,5)
   loop = asyncio.get_event_loop()                #第一步：创建事件循环
   task = asyncio.ensure_future(coroutine)         #第二步:将多个协程函数包装成任务列表
   loop.run_until_complete(task)                  #第三步：通过事件循环运行
   print('-------------------------------------')
   print(task.result())
   loop.close() 

   '''运行结果为
   Hello world 01 begin
   Hello again 01 end
   -------------------------------------
   15
   '''
   ```

2. 通过定义**回调函数**来获取

```python
import asyncio
import time
   
  async def hello1(a,b):
      print("Hello world 01 begin")
      await asyncio.sleep(3)  #模拟耗时任务3秒
      print("Hello again 01 end")
    return a+b
   
def callback(future):   #定义的回调函数,需要传future参数
    print(future.result())
 
loop = asyncio.get_event_loop()                #第一步：创建事件循环
task=asyncio.ensure_future(hello1(10,5))       #第二步:将多个协程函数包装成任务
task.add_done_callback(callback)                      #并被任务绑定一个回调函数，默认传入结果参数
 
loop.run_until_complete(task)                  #第三步：通过事件循环运行
loop.close()                                   #第四步：关闭事件循环
 
 
'''运行结果为：
Hello world 01 begin
Hello again 01 end
15
'''
```

> **注意：**​****所谓的****​**回调函数**，就是指协程函数 coroutine**执行结束时候会调用回调函数**。并通过**参数 future 获取协程执行的结果**。我们创建的**task 和回调里的 future 对象**，实际上是**同一个对象**，因为 task 是 future 的子类。

## Asyncio 异步编程的基本模板

### **第一步：构造事件循环**

1. `loop = asyncio.get_running_loop()`

   > 返回（获取）在当前线程中**正在运行的事件循环**，如果没有正在运行的事件循环，则会显示错误；它是**python3.7 中新添加的**
   >

2. `loop = asyncio.get_event_loop()`

   > **获得一个事件循环**，如果当前线程还没有事件循环，则**创建一个新的事件循环 loop**；
   >

3. `loop=asyncio.set_event_loop(thread)`

   > 设置一个事件循环**为当前线程的事件循环**；
   >

4. `loop=asyncio.new_event_loop()`

   > **创建一个新的事件循环**
   >

### **第二步：将一个或者是多个协程函数包装成任务 Task**

1. `task = asyncio.create_task(coro(参数列表))`

   > **这是 3.7 版本新添加的**
   >

2. `task = asyncio.ensure_future(coro(参数列表))`

> 需要注意的是，在使用 `Task.result()` 获取**协程函数结果**的时候，使用 `asyncio.create_task()` 却会显示错，但是使用 `asyncio.ensure_future` 却正确

### **第三步：通过事件循环运行**

1. `loop.run_until_complete(asyncio.wait(tasks))`

   > 通过 `asyncio.wait()`​**整合多个 task**
   >

2. `loop.run_until_complete(asyncio.gather(tasks))`

   > 通过 `asyncio.gather()`​**整合多个 task**
   >

3. `loop.run_until_complete(task_1)`

   > **单个任务则不需要整合**
   >

4. ~~loop.run_forever()~~

   > ~~但是这个方法在新版本已经取消，不再推荐使用，因为使用起来不简洁~~
   >

#### 使用 `gather` 和 `wait` 整合 Task 注册多个服务

1. #### **参数形式不一样**

   **gather**的参数为 coroutines_or_futures,即如这种形式：

   ```python
   tasks = asyncio.gather(*[task1,task2,task3])
   tasks = asyncio.gather(task1,task2,task3)
   loop.run_until_complete(tasks)
   ```

   **wait**的参数为**列表或者集合**的形式，如下:

   ```python
   tasks = asyncio.wait([task1,task2,task3])
   loop.run_until_complete(tasks)
   ```

2. #### **返回的值不一样**

   **gather 返回的是每一个任务运行的结果**：

   > ###### 要以传入一个列表可变参数
   >

   **可变参数允许在调用参数的时候传入多个参数,这些参数在调用时被自动组装为一个 tuple**

   `results = await asyncio.gather(*[tasks])`

   `results = await asyncio.gather(task1,task2,task3)`

   **wait 返回 dones 是已经完成的任务，pending 是未完成的任务，都是集合类型**：  
   `done, pending = yield from asyncio.wait(fs)`

> 简单来说：**async.wait 会返回两个值:done 和 pending**，done 为已完成的协程 Task，pending 为超时未完成的协程 Task，**需通过 future.result 调用 Task 的 result。**
>
> 而 `async.gather` 返回的是**已完成 Task 的 result**。

### **第四步：关闭事件循环**

```python
loop.close()
# 以上示例都没有调用 loop.close，好像也没有什么问题。所以到底要不要调 loop.close 呢？
```

## 注意

### 协程阻塞问题

**异步方式依然会有阻塞的**，当我们定义的很多个异步方法**彼此之间有一来**的时候，比如，我必须要等到函数 1 执行完毕，**函数 2 需要用到函数 1 的返回值**，就会造成**阻塞**，这也是异步编程的难点之一，如何合理配置这些资源，尽量**减少函数之间的明确依赖**，这是很重要的。

**结论**：在有**很多个异步方式**的时候，一定要尽量避免这种**异步函数的直接调用**，这和同步是没什么区别的，一定要**通过事件循环 loop**，**“让事件循环在各个异步函数之间不停游走”**，这样才不会造成阻塞。

## 代码片段

### 使用 Gather 同时注册多个任务，实现并发

```python
import asyncio
import time
async def hello1(a,b):
    print("Hello world 01 begin")
    await asyncio.sleep(3)  #模拟耗时任务3秒
    print("Hello again 01 end")
    return a+b
 
async def hello2(a,b):
    print("Hello world 02 begin")
    await asyncio.sleep(2)   #模拟耗时任务2秒
    print("Hello again 02 end")
    return a-b
 
async def hello3(a,b):
    print("Hello world 03 begin")
    await asyncio.sleep(4)   #模拟耗时任务4秒
    print("Hello again 03 end")
    return a*b
 
async def main():  #封装多任务的入口函数
    # 用列表表达式创建任务
    tasks = [
        asyncio.ensure_future(hello1(10,5))
        for i in range(10)
    ]
    results = await asyncio.gather(tasks)   
    for result in results:    #通过迭代获取函数的结果，每一个元素就是相对应的任务的返回值，顺序都没变
        print(result)
 
 
loop = asyncio.get_event_loop()               
loop.run_until_complete(main())
loop.close()
```

### 异步 + 多线程

```python
import asyncio 
import asyncio,time,threading
 
#需要执行的耗时异步任务
async def func(num):
    print(f'准备调用func,大约耗时{num}')
    await asyncio.sleep(num)
    print(f'耗时{num}之后,func函数运行结束')
 
#定义一个专门创建事件循环loop的函数，在另一个线程中启动它
def start_loop(loop):
    asyncio.set_event_loop(loop)
    loop.run_forever()
 
#定义一个main函数
def main():
    coroutine1 = func(3)
    coroutine2 = func(2)
    coroutine3 = func(1)
 
    new_loop = asyncio.new_event_loop()                        #在当前线程下创建时间循环，（未启用），在start_loop里面启动它
    t = threading.Thread(target=start_loop,args=(new_loop,))   #通过当前线程开启新的线程去启动事件循环
    t.start()
 
    asyncio.run_coroutine_threadsafe(coroutine1,new_loop)  #这几个是关键，代表在新线程中事件循环不断“游走”执行
    asyncio.run_coroutine_threadsafe(coroutine2,new_loop)
    asyncio.run_coroutine_threadsafe(coroutine3,new_loop)
 
    for i in "iloveu":
        print(str(i)+"    ")
 
if __name__ == "__main__":
    main()
 
'''运行结果为：
i    准备调用func,大约耗时3
l    准备调用func,大约耗时2
o    准备调用func,大约耗时1
v
e
u
耗时1之后,func函数运行结束
耗时2之后,func函数运行结束
耗时3之后,func函数运行结束
'''
```

### Httpx Aiohttp 之异步请求

- **aiohttp 实现**

```python
import aiohttp
import asyncio
 
async def main():
    async with aiohttp.ClientSession() as client:
         async with client.get('http://httpbin.org/get') as resp:
              assert resp.status == 200
              html= await resp.text()
              print(html)
```

- **httpx 实现**

```python
async with httpx.AsyncClient() as client:
    resp = await client.get('http://httpbin.org/get')
    assert resp.status_code == 200
    html = resp.text
```

感觉总体上比较 `aiohttp` 写起来舒服多了**，少写很多异步代码。**

> 之前使用 aiohttp 中的 resp.status 来获取状态码的时候写了 status_code，应该是使用 requests 习惯了吧，这下好了使用 httpx 不用担心这个写错的问题了。

# 深入理解 Python 协程

> 参考: [Python Asyncio 文档](https://docs.python.org/zh-cn/3/library/asyncio.html)

## 协程

```python
import asyncio
import time

# 协程函数返回的是一个协程对象
async def say_after(delay, what):
    await asyncio.sleep(delay)
    print(what)

async def main():
    print(f"started at {time.strftime('%X')}")

    # 等待1s后再等待2s,不能做到并行运行
    await say_after(1, 'hello') # await 等待协程运行结束
    await say_after(2, 'world')

    print(f"finished at {time.strftime('%X')}")

# asyncio.run() 函数用来运行最高层级的入口点 "main()" 函数
asyncio.run(main())
```

`asyncio.create_task()` 函数用来运行作为 `asyncio` 任务的多个协程。

```python
async def main():
    task1 = asyncio.create_task(
        say_after(1, 'hello'))

    task2 = asyncio.create_task(
        say_after(2, 'world'))

    print(f"started at {time.strftime('%X')}")

    # 等待两个 task 完成,并行运行,只需要 2s.
    await task1
    await task2

    print(f"finished at {time.strftime('%X')}")
```

## 可等待对象

如果一个对象可以在 `await` 语句中使用，那么它就是 **可等待** 对象。

> 可等待对象有三种主要类型: **协程**, **任务** 和 **Future**.

### 协程

**协程函数**: 定义形式为 `async def` 的函数;  
**协程对象**: 调用 **协程函数** 所返回的对象。

```python
import asyncio

# 协程函数
async def nested():
    return 42

async def main():
    # 调用协程函数返回的是协程对象(coroutine object),不能运行
    nested()

    # 协程通过 await 可等待的方式运行
    print(await nested())

asyncio.run(main())
```

### 任务

**任务**: 被用来 “并行的” 调度协程  
当一个协程通过 `asyncio.create_task(coro,*,name=None)` 等函数被封装为一个任务，该协程会被 **自动调度** 执行:

该任务会在 `get_running_loop()` 返回的循环中执行，如果当前线程没有在运行的循环则会引发 **RuntimeError**。

`task.add_done_callback(func)` 设置任务完成的回调函数

```python
import asyncio

async def nested():
    return 42

async def main():
    # 将协程封装成一个任务
    task = asyncio.create_task(nested())

    # 等待直到它完成
    await task

asyncio.run(main())
```

`create_task` 会把可等待对象被封装为一个任务,该协程会被 **自动调度** 执行,所以不用 `await`

```python
import asyncio

async def wait(times: int):
    print(f"等待{times}s")
    await asyncio.sleep(times)
    print("等待结束!")

async def main():
    # asyncio.wait_for(wait(3), 2) 不 await 就出错: RuntimeWarning: coroutine 'wait_for' was never awaited

    # create_task 会把可等待对象被封装为一个任务，该协程会被 **自动调度** 执行,
    # 所以不用 await 也能执行, await 了就表示等待这个任务执行完成!

    asyncio.create_task(asyncio.sleep(5))
    await asyncio.create_task(wait(100))  # 可用于堵塞事件循环,不退出

if __name__ == "__main__":
    asyncio.run(main())
```

### Futures

`Future` 是一种特殊的 低层级 可等待对象，表示一个异步操作的 **最终结果**。

**通常情况下没有必要在应用层级的代码中创建 ​**​**`Future`**​**​ 对象。**

## 运行 Asyncio 协程

`asyncio.run(coro, *, debug=False)`  
执行 `coroutine coro` 并返回结果。  
此函数会运行传入的协程，负责管理 asyncio 事件循环，终结异步生成器，并关闭线程池。

当有其他 `asyncio` 事件循环在同一线程中运行时，此函数不能被调用。

如果 debug 为 True，事件循环将以调试模式运行。

**此函数总是会创建一个新的事件循环并在结束时关闭之。它应当被用作 asyncio 程序的主入口点，理想情况下应当只被调用一次。**

## 并发运行任务

`asyncio.gather(*aws, return_exceptions=False)`

并发运行 aws 序列中的可等待对象。  
如果 aws 中的某个可等待对象为协程，它将自动被作为一个任务 (asyncio.create_task) 调度。

如果 `return_exceptions` 为 False (默认)，所引发的首个异常会立即传播给**等待 gather() 的任务**。aws 序列中的其他可等待对象 不会被取消 并将继续运行。

如果 return_exceptions 为 True，异常会和成功的结果一样处理，并聚合至结果列表。

```python
import asyncio


async def factorial(name, number):
    """计算 number! 阶乘并返回
    example: 3!=1*2*3
    """
    # 测试如果出现异常
    if name == "error":
        print(f"任务名:{name} 出现异常!")
        raise EOFError
    f = 1
    for i in range(2, number + 1):
        print(f"任务名:{name}:计算阶乘({number}), i={i}...")
        await asyncio.sleep(1)  # await 使协程等待,让出给其他协程使用
        f *= i
    print(f"任务名{name}: {number}! = {f}")
    return f

async def main():
    # gather 一起执行可等待对象,并按调用顺序返回
    # gather 会阻塞直到 gather 中的所有可等待对象完成
    print("async start!")
    L = await asyncio.gather(
        factorial("error", None),
        factorial("A", 2),
        factorial("B", 3),
        factorial("C", 4), return_exceptions=False
    )
    # 当 return_exceptions 为 False 时引发的首个异常会传播给 gather 可等待对象列表的任务
    # 整个程序停摆,未执行完的可等待对象也会取消

    # 当 return_exceptions 为 True，异常会和成功的结果一样处理，并聚合至结果列表。
    # 并不会引发整个程序的异常
    print(f"async result:{L}")
    print("async end....")
asyncio.run(main())
```

如果 gather 本身被取消，则无论 `return_exceptions` 取值为何，消息都会被传播。

```python
if name == "error":
    print(f"任务名:{name} 出现异常!")
    gather_waiting_object.cancel()  # 取消 gather
# 错误: asyncio.exceptions.CancelledError
```

## 超时

协程 (可等待对象) `asyncio.wait_for(aw, timeout)`

等待 aw 可等待对象 完成，指定 `timeout` 秒数后超时。  
如果 aw 是一个协程，它将自动被作为任务 ((asyncio.create_task)) 调度。  
`timeout` 可以为 None，也可以为 float 或 int 型数值表示的等待秒数。如果 timeout 为 None，则等待直到完成。

如果发生超时，任务将取消并引发 asyncio.TimeoutError.

要避免任务 取消，可以加上 shield()。

此函数将等待直到 Future 确实被取消，所以总等待时间可能超过 timeout。 如果在取消期间发生了异常，异常将会被传播。

如果等待被取消，则 aw 指定的对象也会被取消。

## 简单等待

coroutine `asyncio.wait(aws, *, timeout=None, return_when=ALL_COMPLETED)`

并发地运行 aws 可迭代对象中的 `可等待对象`(不能直接传入协程对象需要转换为 Task) 并进入阻塞状态直到满足 return_when 所指定的条件。

return_when 指定此函数应在何时返回:

- FIRST_COMPLETED: 在第一个可等待对象运行完毕后返回
- FIRST_EXCEPTION: 在任意可等待对象抛出异常后返回,不会结束其他可等待对象,并在程序结束最后抛出异常
- ALL_COMPLETED(默认): 在所有可等待对象执行完毕后返回

aws 可迭代对象必须不为空。 返回两个 Task/Future 集合: (done, pending)

`(done, panding)`: done: 已完成的协程;panding: 正在等待的协程; 支持通过 `if` 判断

```python
import asyncio


async def wait(name, times: int):
    if name == "A":
        raise EOFError
    print(f"{name}等待{times}s")
    await asyncio.sleep(times)
    print(f"{name}等待结束!")
    return times


async def main():

    task, pending = await asyncio.wait(
        (
            wait("A", 3),
            wait("B", 2),
            wait("C", 1)
        ), return_when=asyncio.FIRST_COMPLETED
    )
    print("已完成:", task, "\n等待:", pending)
    await asyncio.sleep(5)
    print("抛出异常")

if __name__ == "__main__":
    asyncio.run(main())
```

(和男朋友出去喝奶茶了,未完待续,可能会继续写一些应用场景,或者和 Golang 的 Goruntine 进行对比)……..  
2022/8/24 18:34
