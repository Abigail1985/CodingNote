
## `startup`事件[¶](https://fastapi.tiangolo.com/zh/advanced/events/#startup-event)

要添加应在应用程序启动前运行的函数，请使用事件`"startup"`声明它：

```python
from fastapi import FastAPI

app = FastAPI()

items = {}


@app.on_event("startup")
async def startup_event():
    items["foo"] = {"name": "Fighters"}
    items["bar"] = {"name": "Tenders"}


@app.get("/items/{item_id}")
async def read_items(item_id: str):
    return items[item_id]
```

在这种情况下，`startup`事件处理程序函数将使用一些值初始化项目“数据库”（只是一个听写）。

您可以添加多个事件处理程序函数。

在所有`startup`事件处理程序完成之前，您的应用程序不会开始接收请求。

[Events: startup - shutdown - FastAPI](https://fastapi.tiangolo.com/zh/advanced/events/)



本项目里，用startup事件连接数据库：

```python
@app.on_event("startup") #定义在应用程序启动之前或应用程序关闭时需要执行的事件处理程序（函数）

async def startup_event():

	conn = f"mongodb://"
	
	if settings.mongo_user:
	
	conn += f"{settings.mongo_user}:{settings.mongo_password}@"
	
	conn += f"{settings.mongo_host}:{settings.mongo_port}"
	
	conn += f"/{settings.database_name}?authSource={settings.auth_database_name}"
	
	client = motor.motor_asyncio.AsyncIOMotorClient(conn)
	
	await init_beanie(database=client[settings.database_name], document_models=[Photographer])
```

[[python的格式化字符串f-string]]
[[MongoDB的访问-Motor用法]]