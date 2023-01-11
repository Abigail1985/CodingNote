
### Pydantic

Pydantic 是一个库，基于Python类型提示来定义数据验证，序列化和文档（使用JSON模式）。这使其非常直观。它可与 Marshmallow 媲美。尽管在基准测试中它比Marshmallow 更快。并且由于它基于相同的Python类型提示，因此对编辑器的支持非常棒。

FastAPI使用它来**处理所有数据验证，数据序列化和自动模型文档（基于JSON Schema）。

然后，FastAPI**会获取该 JSON Schema 数据并将其放入OpenAPI 中，除此之外它还会执行其他所有操作。

[[pydantic的field]]

### Starlette

Starlette 是一种轻量级的 ASGI 框架/工具包，是构建高性能 asyncio 服务的理想选择。
[[ASGI：异步服务器网关接口]]

它非常简单直观。它的设计易于扩展，并具有模块化组件。

它具有：

-   令人印象深刻的性能。
-   WebSocket支持。
-   GraphQL支持。
-   处理中的后台任务。
-   启动和关闭事件。
-   测试基于 requests 的客户端。
-   CORS，GZip，静态文件，流式响应。
-   会话和 Cookie 支持。
-   100％ 的测试覆盖率。
-   100％ 类型注释的代码库。
-   零硬依赖性。

Starlette 是目前测试最快的 Python 框架。只有 Uvicorn 超越了它，

**Uvicorn 不是框架，而是服务器。**

Starlette 提供了所有基本的 Web 微框架功能。但是它不提供自动数据验证，序列化或API 文档。

这是 FastAPI 在顶部添加的主要内容之一，全部基于Python类型提示（使用Pydantic）。以及依赖注入系统，安全实用程序，OpenAPI 模式生成等。

技术细节： ASGI 是 Django 核心团队成员开发的新“标准”。尽管他们正在这样做，但它仍然不是“ Python标准”（PEP）。但是，它已经被多种工具用作“标准”。这可以大大提高互操作性，因为您可以将 Uvicorn 切换到任何其他 ASGI 服务器（例如 Daphne 或 Hypercorn），也可以添加与ASGI兼容的工具，例如 python-socketio。

FastAPI 使用它来处理所有核心 Web 部件。在顶部添加功能。类 FastAPI 本身直接继承Starlette。因此，使用 Starlette 可以执行的任何操作，都可以直接使用 FastAPI 进行。


### Uvicorn

Uvicorn 是基于 uvloop 和 httptools 构建的如闪电般快速的 ASGI 服务器。它不是Web框架，而是服务器。例如，它不提供用于按路径进行路由的工具。那是像 Starlette（或FastAPI）这样的框架可以提供的。它是 Starlette 和 FastAPI 的推荐服务器。

FastAPI 推荐它为主 Web服务器运行 FastAPI 应用程序。您可以将其与 Gunicorn 结合使用，以拥有异步多进程服务器。在“ [部署”](https://link.zhihu.com/?target=https%3A//fastapi.tiangolo.com/deployment/) 部分中查看更多详细信息。





### 总结
-   unicorn：一个实现 WSGI 规范的高性能服务器，使用 asyncio 底层框架来实现服务器/应用接口。

-   starlette: 在 uvicorn 基础上建立的 web 开发框架，可以看作是 tornado 的替代品。在此框架上可以涵盖 http， web socket, 路由，中间件，后台任务等几乎所有的后端开发需求。

-   回到 FastAPI，他是一个专注于 web api 的开发框架（web 部分基于 starlette, 数据部分基于 pydantic）

应用上：

-     如果只是要开发一个足够小的 http 服务，使用 uvicorn 就可以了，把他当成 [web.py](http://web.py/) 或者 SimpleHTTPServer。

-   如果要开发一个完整的 web 服务，那要用 starlette，实现更多 web 服务功能 （当成 tornado）。

-   如果专注于要开发一个 web api 的话，那可以使用 FastAPI，实现标准化的接口开发。

在开发中可以用 uvicorn 运行服务，在生产环境用 gunicorn 进行多进程管理。但是我使用 kubernetes 部署，所以其实在 docker 中使用 uvicorn 运行进程就可以了。