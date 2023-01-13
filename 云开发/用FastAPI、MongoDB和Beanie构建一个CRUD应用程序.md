
[[beanie介绍]]
[[MongoDB的访问-Motor用法]]


在本教程中，你将学习如何用[FastAPI](https://link.juejin.cn/?target=https%3A%2F%2Ffastapi.tiangolo.com%2F "https://fastapi.tiangolo.com/")和[MongoDB](https://link.juejin.cn/?target=https%3A%2F%2Fwww.mongodb.com%2F "https://www.mongodb.com/")开发一个异步API。我们将使用[Beanie ODM](https://link.juejin.cn/?target=https%3A%2F%2Froman-right.github.io%2Fbeanie%2F "https://roman-right.github.io/beanie/")库来与 MongoDB 进行异步交互。

## 目标

在本教程结束时，你将能够。

1.  解释什么是 Beanie ODM，以及为什么你可能想使用它
2.  使用 Beanie ODM 与 MongoDB 进行异步交互
3.  用Python和FastAPI开发一个RESTful API

## 为什么是 Beanie ODM？

[Beanie](https://link.juejin.cn/?target=https%3A%2F%2Froman-right.github.io%2Fbeanie%2F "https://roman-right.github.io/beanie/")是 MongoDB 的一个异步对象-文档映射器（ODM），它支持开箱即用的数据和模式迁移。它使用[Motor](https://link.juejin.cn/?target=https%3A%2F%2Fmotor.readthedocs.io%2F "https://motor.readthedocs.io/")，作为一个异步数据库引擎，以及[Pydantic](https://link.juejin.cn/?target=https%3A%2F%2Fpydantic-docs.helpmanual.io%2F "https://pydantic-docs.helpmanual.io/")。

虽然你可以简单地使用Motor，但Beanie提供了一个额外的抽象层，使得与Mongo数据库内的集合进行交互变得更加容易。

> 想只使用Motor吗？请查看《[使用FastAPI和MongoDB构建CRUD应用程序](https://link.juejin.cn/?target=https%3A%2F%2Ftestdriven.io%2Fblog%2Ffastapi-mongo "https://testdriven.io/blog/fastapi-mongo")》。

## 初始设置

首先创建一个新的文件夹来存放你的项目，名为 "fastapi-beanie"。

```shell
$ mkdir fastapi-beanie
$ cd fastapi-beanie
复制代码
```

接下来，创建并激活一个虚拟环境。

```shell
$ python3.10 -m venv venv
$ source venv/bin/activate
(venv)$ export PYTHONPATH=$PWD
复制代码
```

> 请随意将venv和Pip换成[Poetry](https://link.juejin.cn/?target=https%3A%2F%2Fpython-poetry.org%2F "https://python-poetry.org/")或[Pipenv](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fpypa%2Fpipenv "https://github.com/pypa/pipenv")。更多信息，请查看[现代 Python 环境](https://link.juejin.cn/?target=https%3A%2F%2Ftestdriven.io%2Fblog%2Fpython-environments%2F "https://testdriven.io/blog/python-environments/")。

接下来，创建以下文件和文件夹。

```css
├── app
│   ├── __init__.py
│   ├── main.py
│   └── server
│       ├── app.py
│       ├── database.py
│       ├── models
│       └── routes
└── requirements.txt
复制代码
```

在你的_requirements.txt_文件中添加以下依赖项。

```ini
beanie==1.11.0
fastapi==0.78.0
uvicorn==0.17.6
复制代码
```

从你的终端安装这些依赖项。

```ruby
(venv)$ pip install -r requirements.txt
复制代码
```

在_app/main.py_文件中，定义一个运行应用程序的入口点。

```ini
import uvicorn

if __name__ == "__main__":
    uvicorn.run("server.app:app", host="0.0.0.0", port=8000, reload=True)
复制代码
```

在这里，我们指示该文件在8000端口运行一个[Uvicorn](https://link.juejin.cn/?target=https%3A%2F%2Fwww.uvicorn.org%2F "https://www.uvicorn.org/")服务器，并在每次文件修改时重新加载。

在通过入口点文件启动服务器之前，在_app/server/app.py_中创建一个基本路由。

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/", tags=["Root"])
async def read_root() -> dict:
    return {"message": "Welcome to your beanie powered app!"}
复制代码
```

从你的控制台运行入口点文件。

```shell
(venv)$ python app/main.py
复制代码
```

在你的浏览器中导航到[http://localhost:8000](https://link.juejin.cn/?target=http%3A%2F%2Flocalhost%3A8000%2F "http://localhost:8000/")。你应该看到。

```json
{
  "message": "Welcome to your beanie powered app!"
}
复制代码
```

## 我们在构建什么？

我们将建立一个产品评论应用程序，允许我们执行以下操作。

-   创建评论
-   读取评论
-   更新评论
-   删除评论

在开始编写路由之前，让我们用Beanie来为我们的应用程序配置数据库模型。

## 数据库模式

Beanie允许你创建[文档](https://link.juejin.cn/?target=https%3A%2F%2Froman-right.github.io%2Fbeanie%2Fapi-documentation%2Fdocument%2F "https://roman-right.github.io/beanie/api-documentation/document/")，然后可以用来与数据库中的集合交互。文档代表你的数据库模式。它们可以通过创建继承自 Beanie 的`Document` 类的子类来定义。`Document` 类是由Pydantic的`BaseModel` ，它使得定义集合和数据库模式以及在交互式Swagger文档页面中显示的示例数据变得容易。

例子。

```python
from beanie import Document


class TestDrivenArticle(Document):
    title: str
    content: str
    date: datetime
    author: str
复制代码
```

定义的文档代表了文章将如何被存储在数据库中。然而，它是一个普通的文档类，没有与之相关的数据库集合。为了关联一个集合，你只需要添加一个`Settings` 类作为子类。

```python
from beanie import Document


class TestDrivenArticle(Document):
    title: str
    content: str
    date: datetime
    author: str


    class Settings:
        name = "testdriven_collection"
复制代码
```

现在我们对模式的创建有了一个概念，我们将为我们的应用程序创建模式。在 "app/server/models "文件夹中，创建一个名为_product_review.py_ 的新文件。

```python
from datetime import datetime

from beanie import Document
from pydantic import BaseModel
from typing import Optional


class ProductReview(Document):
    name: str
    product: str
    rating: float
    review: str
    date: datetime = datetime.now()

    class Settings:
        name = "product_review"
复制代码
```

由于`Document` 类是由Pydantic提供的，我们可以定义模式数据的例子，以使开发人员更容易从交互式Swagger文档中使用API。

像这样添加`Config` 子类。

```python
from datetime import datetime

from beanie import Document
from pydantic import BaseModel
from typing import Optional


class ProductReview(Document):
    name: str
    product: str
    rating: float
    review: str
    date: datetime = datetime.now()

    class Settings:
        name = "product_review"

    class Config:
        schema_extra = {
            "example": {
                "name": "Abdulazeez",
                "product": "TestDriven TDD Course",
                "rating": 4.9,
                "review": "Excellent course!",
                "date": datetime.now()
            }
        }
复制代码
```

因此，在上面的代码块中，我们定义了一个名为`ProductReview` 的Beanie文档，它代表了产品评论将如何被存储。我们还定义了一个集合，`product_review` ，数据将被存储在这里。

我们将在路由中使用这个模式来执行适当的请求体。

最后，让我们定义一下更新产品评论的模式。

```python
class UpdateProductReview(BaseModel):
    name: Optional[str]
    product: Optional[str]
    rating: Optional[float]
    review: Optional[str]
    date: Optional[datetime]

    class Config:
        schema_extra = {
            "example": {
                "name": "Abdulazeez Abdulazeez",
                "product": "TestDriven TDD Course",
                "rating": 5.0,
                "review": "Excellent course!",
                "date": datetime.now()
            }
        }
复制代码
```

上面的`UpdateProductReview` 类是[BaseModel](https://link.juejin.cn/?target=https%3A%2F%2Fpydantic-docs.helpmanual.io%2Fusage%2Fmodels%2F "https://pydantic-docs.helpmanual.io/usage/models/")类型的，它允许我们只对请求体中的字段进行修改。

有了这个模式，在继续编写路由之前，让我们先设置MongoDB和我们的数据库。

在本节中，我们将对MongoDB进行布线，并配置我们的应用程序与之通信。

> 根据[维基百科](https://link.juejin.cn/?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FMongoDB "https://en.wikipedia.org/wiki/MongoDB")，MongoDB是一个跨平台的面向文档的数据库程序。被归类为NoSQL数据库程序，MongoDB使用类似JSON的文档，并有可选的模式。

### MongoDB设置

如果你的机器上还没有安装MongoDB，请参考文档中的[安装](https://link.juejin.cn/?target=https%3A%2F%2Fdocs.mongodb.com%2Fmanual%2Finstallation%2F "https://docs.mongodb.com/manual/installation/")指南。一旦安装完毕，继续按照指南的要求运行[mongod](https://link.juejin.cn/?target=https%3A%2F%2Fdocs.mongodb.com%2Fmanual%2Freference%2Fprogram%2Fmongod%2F%23bin.mongod "https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod")守护进程。一旦完成，你可以通过`mongo` shell命令连接到实例，来验证MongoDB已经启动并运行。

作为参考，本教程使用MongoDB社区版v5.0.7。

```css
$ mongo --version
MongoDB shell version v5.0.7

Build Info: {
    "version": "5.0.7",
    "gitVersion": "b977129dc70eed766cbee7e412d901ee213acbda",
    "modules": [],
    "allocator": "system",
    "environment": {
        "distarch": "x86_64",
        "target_arch": "x86_64"
    }
}
复制代码
```

### 设置数据库

在_database.py_ 中，添加以下内容。

```python
from beanie import init_beanie
import motor.motor_asyncio

from app.server.models.product_review import ProductReview


async def init_db():
    client = motor.motor_asyncio.AsyncIOMotorClient(
        "mongodb://localhost:27017/productreviews"
    )

    await init_beanie(database=client.db_name, document_models=[ProductReview])
复制代码
```

在上面的代码块中，我们导入了[init_beanie](https://link.juejin.cn/?target=https%3A%2F%2Froman-right.github.io%2Fbeanie%2Ftutorial%2Finitialization%2F "https://roman-right.github.io/beanie/tutorial/initialization/")方法，它负责初始化由[motor.motor_asyncio](https://link.juejin.cn/?target=https%3A%2F%2Fmotor.readthedocs.io%2Fen%2Fstable%2Fapi-asyncio%2Fasyncio_motor_client.html%23motor.motor_asyncio.AsyncIOMotorClient "https://motor.readthedocs.io/en/stable/api-asyncio/asyncio_motor_client.html#motor.motor_asyncio.AsyncIOMotorClient")驱动的数据库引擎。`init_beanie` 方法需要两个参数。

1.  `database` - 要使用的数据库的名称。
2.  `document_models` - 一个定义的文档模型的列表--在我们的例子中，是 模型。`ProductReview`

`init_db` 函数将在应用程序的启动事件中被调用。更新_app.py_以包括启动事件。

```python
from fastapi import FastAPI

from app.server.database import init_db


app = FastAPI()


@app.on_event("startup")
async def start_db():
    await init_db()


@app.get("/", tags=["Root"])
async def read_root() -> dict:
    return {"message": "Welcome to your beanie powered app!"}
复制代码
```

现在我们有了我们的数据库配置，让我们来编写路由。

## 路由

在这一节中，我们将建立路由，从应用程序中对数据库进行CRUD操作。

1.  POST review
2.  GET单个评论和GET所有评论
3.  PUT 单一评论
4.  DELETE 单一评论

在 "routes "文件夹中，创建一个名为_product_review.py_ 的文件。

```javascript
from beanie import PydanticObjectId
from fastapi import APIRouter, HTTPException
from typing import List

from app.server.models.product_review import ProductReview, UpdateProductReview


router = APIRouter()
复制代码
```

在上面的代码块中，我们导入了`PydanticObjectId` ，它将用于在检索单个请求时对ID参数进行类型提示。我们还导入了`APIRouter` 类，它负责处理路由操作。我们还导入了我们之前定义的模型类。

Beanie文档模型允许我们用更少的代码直接与数据库交互。例如，要检索一个数据库集合中的所有记录，我们所要做的就是。

```ini
data = await ProductReview.find_all().to_list()
return data # A list of all records in the collection.
复制代码
```

在我们继续编写CRUD操作的路由函数之前，让我们在_app.py_中注册一下路由。

```python
from fastapi import FastAPI

from app.server.database import init_db
from app.server.routes.product_review import router as Router


app = FastAPI()
app.include_router(Router, tags=["Product Reviews"], prefix="/reviews")


@app.on_event("startup")
async def start_db():
    await init_db()


@app.get("/", tags=["Root"])
async def read_root() -> dict:
    return {"message": "Welcome to your beanie powered app!"}
复制代码
```

### 创建

在_routes/product_review.py_中，添加以下内容。

```python
@router.post("/", response_description="Review added to the database")
async def add_product_review(review: ProductReview) -> dict:
    await review.create()
    return {"message": "Review added successfully"}
复制代码
```

在这里，我们定义了路由函数，它接受一个类型为`ProductReview` 的参数。如前所述，文档类可以直接与数据库交互。

新记录是通过调用[create()](https://link.juejin.cn/?target=https%3A%2F%2Froman-right.github.io%2Fbeanie%2Fapi-documentation%2Fdocument%2F%23documentcreate "https://roman-right.github.io/beanie/api-documentation/document/#documentcreate")方法创建的。

上面的路由期望的有效载荷与此类似。

```json
{
  "name": "Abdulazeez",
  "product": "TestDriven TDD Course",
  "rating": 4.9,
  "review": "Excellent course!",
  "date": "2022-05-17T13:53:17.196135"
}
复制代码
```

测试该路由。

```vbnet
$ curl -X 'POST' \
  'http://0.0.0.0:8000/reviews/' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "Abdulazeez",
  "product": "TestDriven TDD Course",
  "rating": 4.9,
  "review": "Excellent course!",
  "date": "2022-05-17T13:53:17.196135"
}'
复制代码
```

上面的请求应该返回一个成功的消息。

```json
{
  "message": "Review added successfully"
}
复制代码
```

### 阅读

接下来是使我们能够检索单个评论和数据库中存在的所有评论的路由。

```less
@router.get("/{id}", response_description="Review record retrieved")
async def get_review_record(id: PydanticObjectId) -> ProductReview:
    review = await ProductReview.get(id)
    return review


@router.get("/", response_description="Review records retrieved")
async def get_reviews() -> List[ProductReview]:
    reviews = await ProductReview.find_all().to_list()
    return reviews
复制代码
```

在上面的代码块中，我们定义了两个函数。

1.  在第一个函数中，该函数接收一个类型为`ObjectiD` 的ID，这是MongoDB IDs的默认编码。使用[get()](https://link.juejin.cn/?target=https%3A%2F%2Froman-right.github.io%2Fbeanie%2Fapi-documentation%2Fdocument%2F%23documentget "https://roman-right.github.io/beanie/api-documentation/document/#documentget")方法检索该记录。
2.  在第二个函数中，我们使用[find_all()](https://link.juejin.cn/?target=https%3A%2F%2Froman-right.github.io%2Fbeanie%2Ftutorial%2Ffinding-documents%2F%23finding-documents "https://roman-right.github.io/beanie/tutorial/finding-documents/#finding-documents")方法检索了所有的评论。`to_list()` 方法是附加的，所以结果是以列表形式返回。

> 另一个可以用来检索单个条目的方法是[find_one()](https://link.juejin.cn/?target=https%3A%2F%2Froman-right.github.io%2Fbeanie%2Ftutorial%2Ffinding-documents%2F%23finding-single-documents "https://roman-right.github.io/beanie/tutorial/finding-documents/#finding-single-documents")方法，它需要一个条件。比如说。
> 
> ```ini
> # Return a record who has a rating of 4.0
> await ProductReview.find_one(ProductReview.rating == 4.0)
> 复制代码
> ```

让我们测试第一条路线来检索所有记录。

```ruby
$ curl -X 'GET' \
  'http://0.0.0.0:8000/reviews/' \
  -H 'accept: application/json'
复制代码
```

响应。

```css
[  {    "_id": "62839ad1d9a88a040663a734",    "name": "Abdulazeez",    "product": "TestDriven TDD Course",    "rating": 4.9,    "review": "Excellent course!",    "date": "2022-05-17T13:53:17.196000"  }]
复制代码
```

接下来，让我们测试检索与提供的ID相匹配的单一记录的路由。

```ruby
$ curl -X 'GET' \
  'http://0.0.0.0:8000/reviews/62839ad1d9a88a040663a734' \
  -H 'accept: application/json'
复制代码
```

响应。

```json
{
  "_id": "62839ad1d9a88a040663a734",
  "name": "Abdulazeez",
  "product": "TestDriven TDD Course",
  "rating": 4.9,
  "review": "Excellent course!",
  "date": "2022-05-17T13:53:17.196000"
}
复制代码
```

接下来，让我们编写更新审查记录的路由。

```less
@router.put("/{id}", response_description="Review record updated")
async def update_student_data(id: PydanticObjectId, req: UpdateProductReview) -> ProductReview:
    req = {k: v for k, v in req.dict().items() if v is not None}
    update_query = {"$set": {
        field: value for field, value in req.items()
    }}

    review = await ProductReview.get(id)
    if not review:
        raise HTTPException(
            status_code=404,
            detail="Review record not found!"
        )

    await review.update(update_query)
    return review
复制代码
```

在这个函数中，我们过滤掉了没有更新的字段，以防止用`None` 覆盖现有字段。

要更新一条记录，需要一个更新查询。我们定义了一个更新查询，用请求体中传递的数据覆盖现有字段。然后我们检查该记录是否存在。如果它存在，它就会被更新，更新后的记录会被返回，否则就会出现404异常。

让我们测试一下这个路由。

```vbnet
$ curl -X 'PUT' \
  'http://0.0.0.0:8000/reviews/62839ad1d9a88a040663a734' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "Abdulazeez Abdulazeez",
  "product": "TestDriven TDD Course",
  "rating": 5
}'
复制代码
```

响应。

```json
{
  "_id": "62839ad1d9a88a040663a734",
  "name": "Abdulazeez Abdulazeez",
  "product": "TestDriven TDD Course",
  "rating": 5.0,
  "review": "Excellent course!",
  "date": "2022-05-17T13:53:17.196000"
}
复制代码
```

### 删除

最后，让我们编写负责删除一条记录的路由。

```python
@router.delete("/{id}", response_description="Review record deleted from the database")
async def delete_student_data(id: PydanticObjectId) -> dict:
    record = await ProductReview.get(id)

    if not record:
        raise HTTPException(
            status_code=404,
            detail="Review record not found!"
        )

    await record.delete()
    return {
        "message": "Record deleted successfully"
    }
复制代码
```

所以，我们首先检查记录是否存在，然后再继续删除记录。记录是通过调用[delete()](https://link.juejin.cn/?target=https%3A%2F%2Froman-right.github.io%2Fbeanie%2Fapi-documentation%2Fdocument%2F%23documentdelete "https://roman-right.github.io/beanie/api-documentation/document/#documentdelete")方法删除的。

让我们测试一下这个路由。

```ruby
$ curl -X 'DELETE' \
  'http://0.0.0.0:8000/reviews/62839ad1d9a88a040663a734' \
  -H 'accept: application/json'
复制代码
```

响应。

```json
{
  "message": "Record deleted successfully"
}
复制代码
```

我们已经成功建立了一个由FastAPI、MongoDB和Beanie ODM驱动的CRUD应用。

## 总结

在本教程中，你学到了如何用FastAPI、MongoDB和Beanie ODM创建一个CRUD应用程序。通过回顾本教程开头的目标，进行快速的自我检查，你可以在[GitHub](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FYoungestdev%2Ffastapi-beanie "https://github.com/Youngestdev/fastapi-beanie")上找到本教程中使用的代码。


