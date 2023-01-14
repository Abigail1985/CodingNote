# httpx 异步支持

默认情况下，HTTPX 提供标准的同步 API，但如果需要，还可以选择异步​`client`​。

Async 是一种并发模型，它比多线程更有效，可以提供显著的性能优势，并允许使用长期存在的网络连接（如 WebSockets）。

如果您使用的是异步 Web 框架，那么您还需要使用异步​`client`​来发送传出 HTTP 请求。

## 发出异步请求

要发出异步请求，您需要一个 ​`AsyncClient`​。

```
>>> async with httpx.AsyncClient() as client:
...     r = await client.get('https://www.example.com/')
...
>>> r
<Response [200 OK]>
```

> 提示  
> 使用 [IPython](https://ipython.readthedocs.io/en/stable/) 或 Python 3.8+使用​`python -m` ​以交互方式尝试此代码，因为它们支持 ​`asyncio`​在控制台中执行​`async` ​/​`await`​表达式。

## 接口差异

如果您使用的是异步​`client`​，那么有一些 API 是使用异步方法的。

### 请求构造

请求方法都是异步的，因此您应该对以下所有内容使用​`response = await client.get(...)`​样式：

-   ​`AsyncClient.get(url, ...)`​
-   ​`AsyncClient.get(url, ...)`​
-   ​`AsyncClient.get(url, ...)`​
-   ​`AsyncClient.get(url, ...)`​
-   ​`AsyncClient.get(url, ...)`​
-   ​`AsyncClient.get(url, ...)`​
-   ​`AsyncClient.get(url, ...)`​
-   ​`AsyncClient.get(url, ...)`​
-   ​`AsyncClient.get(url, ...)`​

### 打开和关闭​`client`​

如果需要上下文管理的client，请使用​`async with httpx.AsyncClient()`​...

```
async with httpx.AsyncClient() as client:
    ...
```

或者，如果要显式关闭​`client`​，请使用​`await client.aclose()`​:

```
client = httpx.AsyncClient()
...
await client.aclose()
```