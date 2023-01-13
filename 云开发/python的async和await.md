
### 协程的基础使用

这是 python 3.7 里面的基础协程用法，现在这种用法已经基本稳定，不太建议使用之前的语法了。

```python
import asyncio
import time

async def visit_url(url, response_time):
    """访问 url"""
    await asyncio.sleep(response_time)
    return f"访问{url}, 已得到返回结果"

start_time = time.perf_counter()
task = visit_url('http://wangzhen.com', 2)
asyncio.run(task)
print(f"消耗时间：{time.perf_counter() - start_time}")
复制代码
```

-   1, 在普通的函数前面加 async 关键字；
-   2，await 表示在这个地方等待子函数执行完成，再往下执行。（在并发操作中，把程序控制权教给主程序，让他分配其他协程执行。） await 只能在带有 async 关键字的函数中运行。
-   3， asynico.run() 运行程序
-   4， 这个程序消耗时间 2s 左右。
- 
[[MongoDB的访问-Motor用法#使用 async for 查询所有文档]]
  

#python 
[[js 的 async和 await]]