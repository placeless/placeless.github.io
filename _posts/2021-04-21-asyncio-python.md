---
layout: post
title:  "浅析 Python 中的 Async IO"
date:   2021-04-21 02:12 +0800
---

## Async IO 是什么

Miguel Grinberg 曾在 2017 PyCon 上讲过一个[例子](https://www.youtube.com/watch?v=iG6fr81xHKA)：你可以想象一个象棋大师跟一堆人车轮战，大师不会一人一场的来，通常是挑战者排成一排大师，大师依次走过去，路过一人走一步棋，且不在原地等待对手反击，而是直接走向下一个对手，如此往复，直到下完。

这么做，效率显然高了一大截，我们可以做个计算：假如有 24 个对手，大师走一步棋需要 5 秒，对手走一步平均要 55 秒，一局算 30 个回合，如果是一个一个来，下完一局再进行下一局，那么合到一起就是 (55 + 5) * 30 = 1800 秒一局，总共需要 12 个小时才能下完；如果改成上面的车轮战方式，则只需要 24 * 5 * 30 = 1 小时。

这大概就是 Async IO 了。需注意的是，Async IO 跟往常说的多进程、多线程并无直接关系，它是单线程单进程的设计，只是给人一种并发的感觉。范围涵盖上，Concurrency 包含 Parallelism，然后 Threading 和 Async IO 属于前者，Multiprocessing 属于后者；另外，Async IO 是跟语言无关的编程范式，每种语言都有各自的实现，Python 是通过 `asyncio` 这个包来提供相关接口，发展过程中，设计改变很大，这里以 Python 3.7+ 为例，仅为个人在工具应用层面的粗浅理解，并不涉及工具底层的设计和实现。

## Python Async IO 的语法规则

```python
async def g():
    # Pause here and come back to g() when f() is ready
    r = await f()
    return r
```

使用 `async def xxx` 的形式来定义异步功能，它会引入一个叫作 Native Coroutine 的东西，接管底层工作。`async with` 和 `async for` 等也是有效写法。

使用 `await` 获取异步函数的结果，在函数内调用 `await` 会挂起当前函数（比如 `g()`），并将当前函数加入到 event loop 中，等待 await 后边（比如 `f()`）获得结果之后，再通知当前函数继续执行。

使用 `asyncio.create_task()` 来计划一个任务，使用 `asyncio.gather(*tasks)` 来计划多个任务（或者说是将一系列 corutines 加入到单个 future 中），也就是说输入一堆任务（通常是以星号开头的展开形式），等它们全部完成，输出这些任务的结果清单。使用 `asyncio.as_completed` 来监控任务的完成情况。最后，使用 `asyncio.run()` 来执行任务队列。

搭配 `asyncio` 的常用包有不少，比如网络相关的 aiohttp，比如文件相关的 aiofiles，以自己的应用场景举个例子：

- 读取一个本地文件，做些准备处理（略）
- 调用远程地址，更新数据（异步）
- 记录回调信息到本地（异步）

```python
import json
from sys import argv
import pandas as pd
import asyncio
import aiohttp
import aiofiles
from tqdm.asyncio import tqdm

def read(file):
	pass

def getToken():
	pass
	
async def update(token, session, id, msg):
	async with session.put('url', json={"id": id, "msg": msg}) as response:
		text = await response.text()
		async with aiofiles.open(log_file, 'a') as f:
			await f.write(f'{text}\n')
		
async def main():
	data = read(argv[1])
	print(data.tail(5))
	print('Press Enter to continue...')
	token = getToken()
	
	async with aiohttp.ClientSession() as session:
		tasks = [update(token, session, row['id'], row['msg']) for _, row in data.iterrows()]
		results = [await t for t in tqdm.as_completed(tasks, leave=False)]
		return results
		
asyncio.run(main())
```

应用到实际案例当中，总会引入了一些语境本身的复杂性，比如这里的 aiohttp 带来的 session 管理（可以理解为浏览器窗口，在一个窗口中管理多个标签页），比如 tqdm 相关的异步任务进度监控（注意 gather 和 as_completed 的区别，以及 as_completed 之前，我们在创建 tasks 之时，还不需要 await 获得结果），剥离开来单独看，也就相对清楚了。

## 参考链接：

1. [asyncio High-level API Index](https://docs.python.org/3/library/asyncio-api-index.html)
2. [Async IO in Python: A Complete Walkthrough](https://realpython.com/async-io-python/)