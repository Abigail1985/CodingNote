
[模式的额外信息 - 例子 - FastAPI](https://fastapi.tiangolo.com/zh/tutorial/schema-extra-example/)

## `Field` 的附加参数[¶](https://fastapi.tiangolo.com/zh/tutorial/schema-extra-example/#field)

在 `Field`, `Path`, `Query`, `Body` 和其他你之后将会看到的工厂函数，你可以为JSON 模式声明额外信息，你也可以通过给工厂函数传递其他的任意参数来给JSON 模式声明额外信息，比如增加 `example`:

```python
from typing import Union

from fastapi import FastAPI
from pydantic import BaseModel, Field

app = FastAPI()


class Item(BaseModel):
    name: str = Field(example="Foo")
    description: Union[str, None] = Field(default=None, example="A very nice Item")
    price: float = Field(example=35.4)
    tax: Union[float, None] = Field(default=None, example=3.2)


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results
```

**==请记住，传递的那些额外参数不会添加任何验证，只会添加注释，用于文档的目的。==**

## `Body` 额外参数[¶](https://fastapi.tiangolo.com/zh/tutorial/schema-extra-example/#body)

你可以通过传递额外信息给 `Field` 同样的方式操作`Path`, `Query`, `Body`等。

比如，你可以将请求体的一个 `example` 传递给 `Body`:

```python
from typing import Union

from fastapi import Body, FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None


@app.put("/items/{item_id}")
async def update_item(
    item_id: int,
    item: Item = Body(
        example={
            "name": "Foo",
            "description": "A very nice Item",
            "price": 35.4,
            "tax": 3.2,
        },
    ),
):
    results = {"item_id": item_id, "item": item}
    return results
```

使用上面的任何方法，它在 `/docs` 中看起来都是这样的:

![](https://fastapi.tiangolo.com/img/tutorial/body-fields/image01.png)