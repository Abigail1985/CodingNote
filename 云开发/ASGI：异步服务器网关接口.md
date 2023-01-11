

### WSGI

wiki 上的解释

-   Web Server Gateway Interface
-   Web 服务器网关接口
-   是为 Python 定义的 **Web 服务器和 Web 应用程序或框架之间的一种简单而通用的接口**

#### WSGI 的问题

-   随着移动网络的发展，Web 技术也在升级，比如 WebSocket、HTTP/2，HTTP/3
-   WSGI 应用是一个单调用、同步接口，即输入一个请求，返回一个响应；这个模式无法支持长连接或者 WebSocket 这样的连接
-   即使想办法将 WSGI 应用改成异步，还有另一个限制：一个 URL 对应一个请求，而 HTTP/2、Websocket 等在一个 URL 里会出现多个请求

### ASGI 的背景

-   在 Python 3.5+ 增加 async/await 特性之后，异步编程变得异常火爆
-   但 Python 仍缺乏用于 asyncio 框架最低限度的低级服务器/应用程序接口
-   而 ASGI 协议规范的出现填补了这一空白，这意味着现在能够开始构建可在所有异步框架中使用的通用工具集

### ASGI

-   Asynchronous Server Gateway Interface
-   异步服务器网关接口
-   ASGI 是 WSGI 的继承者，旨在提供支持异步的 Python web 服务器、框架和应用程序之间的标准接口
-   ASGI 为异步和同步应用程序提供了一个标准，继续成为 web 服务器、框架和应用程序（如异步 python 中的 WSGI）之间的标准兼容性

### 简单总结

-   WSGI 是为同步应用程序提供标准，不支持 WebSocket
-   ASGI 是为异步、同步应用程序提供标准，支持 WSGI 不支持当前 web 开发中的一些新的协议标准
-   ASGI 是对 WSGI 的扩展