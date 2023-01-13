
## FastAPI 中经常用 ... 来表达请求参数

使用 FastAPI，以下代码片段将使用查询参数定义一个路由和服务，该参数`q`应为字符串。

```text
@app.get("/something/)
async def something(q: str):
    return f"Your query was {q}"
```

如果想让这个请求参数是可选的就可以写成这样。

```text
async def something(q: str = None):
```

但是如果想做一些验证，比如最小长度是 10，并且是可选的，可以写成这样。

```text
async def something(q: str = Query(None, min_length=10)):
```

Query 对象的第一个参数是默认值 None。请求的时候可以将其省略，但是如果存在该参数，则其长度应至少为10个字符。但是，如果我们想为参数设置最小长度同时又需要它，该怎么办？

这是我们应该使用 ... 的地方。现在看起来像这样：

```text
async def something(q: str = Query(..., min_length=10)):
```

**就是这个参数是必须的，并且长度不能小于 10。**

[Python 3 中 ... 三个点的省略号的作用 - 知乎](https://zhuanlan.zhihu.com/p/264896206)