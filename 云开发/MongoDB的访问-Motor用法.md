
**Motor提供了一个基于协程的API，用于对MongoDB的非阻塞访问。**

### 创建客户端

##### 指定主机和端口号

```ini
import motor.motor_asyncio
client = motor.motor_asyncio.AsyncIOMotorClient('localhost', 27017)
复制代码
```

##### 使用用户名和密码

```rust
motor.motor_asyncio.AsyncIOMotorClient('mongodb://root:123456@localhost:27017')
复制代码
```

### 获取数据库

MongoDB的单个实例可以支持多个独立的数据库。在开放式客户端中，您可以使用点表示法或括号表示法来获取对特定数据库的引用：

```ini
db = client.test_database
db = client['test_database']
复制代码
```

创建对数据库的引用不会执行I / O，也不需要 **await** 表达式。

### 获取集合

一个集合是一组存储在MongoDB中的文档，并且可以被认为是大致在关系数据库中的表的当量。获取Motor中的集合与获取数据库的工作方式相同：

```ini
collection = db.test_collection
collection = db['test_collection']
复制代码
```

就像获取对数据库的引用一样，获取对集合的引用不会产生I / O并且不需要 **await** 表达式。

### 插入文档（insert_one）

与PyMongo一样，Motor使用Python字典表示MongoDB文档。要存储在MongoDB中的文档，在 **await** 表达式中调用 **insert_one()** ：

```scss
async def do_insert():
    document = {'key': 'value'}
    result = await db.test_collection.insert_one(document)  # insert_one只能插入一条数据
    print('result %s' % repr(result.inserted_id))

loop = asyncio.get_event_loop()
loop.run_until_complete(do_insert())
# result ObjectId('...')
复制代码
```

### 批量插入文档（insert_many）

```scss
async def do_insert():
    result = await db.test_collection.insert_many(
        [{'i': i} for i in range(2000)])  # insert_many可以插入一条或多条数据，但是必须以列表(list)的形式组织数据
    print('inserted %d docs' % (len(result.inserted_ids),))

loop = asyncio.get_event_loop()
loop.run_until_complete(do_insert())
# inserted 2000 docs
复制代码
```

### 查询一个文档（find_one）

使用 **find_one()** 得到匹配查询的第一个文档。例如，要获取密钥“i”的值小于1的文档：

```scss
async def do_find_one():
    document = await db.test_collection.find_one({'i': {'$lt': 1}})  # find_one只能查询一条数据
    pprint.pprint(document)

loop = asyncio.get_event_loop()
loop.run_until_complete(do_find_one())
# {'_id': ObjectId('...'), 'i': 0}
复制代码
```

**注意：结果是一个字典匹配我们之前插入的字典。**

### 查询多个文档（find）

使用 **find()** 要查询的一组文档。 **find()** 没有I / O，也不需要 **await** 表达式。它只是创建一个 **AsyncIOMotorCursor** 实例。当您调用 **to_list()** 或为循环执行异步时 **(async for)** ，查询实际上是在服务器上执行的。

查询 “ i ” 小于5的所有文档：

```css
async def do_find():
    cursor = db.test_collection.find({'i': {'$lt': 5}}).sort('i')
    for document in await cursor.to_list(length=100):
        pprint.pprint(document)

loop = asyncio.get_event_loop()
loop.run_until_complete(do_find())
# {'_id': ObjectId('...'), 'i': 0}
# {'_id': ObjectId('...'), 'i': 1}
# {'_id': ObjectId('...'), 'i': 2}
# {'_id': ObjectId('...'), 'i': 3}
# {'_id': ObjectId('...'), 'i': 4}
复制代码
```

**一length** ，调用方法 **to_list** 的必传参数，防止 **Motor** 从缓冲中获取的文档数量不受限制，此处限制为100。

### 使用 async for 查询所有文档
[[python的async和await]]

您可以在循环中一次处理一个文档：**async for**

```css
async def do_find():
    c = db.test_collection
    async for document in c.find({}):  # 查询所有文档
        pprint.pprint(document)

loop = asyncio.get_event_loop()
loop.run_until_complete(do_find())
# {'_id': ObjectId('...'), 'i': 0}
# {'_id': ObjectId('...'), 'i': 1}
复制代码
```

您可以在开始迭代之前对查询应用排序，跳过或限制：

```css
async def do_find():
    cursor = db.test_collection.find({'i': {'$lt': 4}})
    # Modify the query before iterating
    cursor.sort('i', -1).skip(1).limit(2)  # 对查询应用排序(sort)，跳过(skip)或限制(limit)
    async for document in cursor:
        pprint.pprint(document)

loop = asyncio.get_event_loop()
loop.run_until_complete(do_find())
# {'_id': ObjectId('...'), 'i': 2}
# {'_id': ObjectId('...'), 'i': 1}
复制代码
```

游标 **(cursor)** 实际上并不是单独从服务器检索每个文档; 它可以大批量地获取文档。

### 使用count_documents()查询集合中的文档数量

```python
async def do_count():
    n = await db.test_collection.count_documents({})  # 查询集合内所有文档数量
    print('%s documents in collection' % n)
    n = await db.test_collection.count_documents({'i': {'$gt': 1000}})  # 按条件查询集合内文档数量
    print('%s documents where i > 1000' % n)

loop = asyncio.get_event_loop()
loop.run_until_complete(do_count())
# 2000 documents in collection
# 999 documents where i > 1000
复制代码
```

### 更新文档（推荐使用update_one或update_many）

**replace_one()** 更改文档。它需要两个参数：一个指定要替换哪个文档的_查询_和一个替换文档。该查询遵循与 **find()** 或 相同的语法 **find_one()** 。替换一个文档的示例：

```scss
async def do_replace():
    coll = db.test_collection
    old_document = await coll.find_one({'i': 50})
    print('found document: %s' % pprint.pformat(old_document))
    _id = old_document['_id']

    old_document['i'] = -1  # 修改文档(dict)的key, value
    old_document['new'] = 'new'  # 增加文档(dict)的key, value
    del old_document['i']  # 删除文档(dict)的key, value

    result = await coll.replace_one(
        {'_id': _id}, old_document)  # replace_one第一个参数为查询条件, 第二个参数为更新后的文档
    print('replaced %s document' % result.modified_count)
    new_document = await coll.find_one({'_id': _id})
    print('document is now %s' % pprint.pformat(new_document))

loop = asyncio.get_event_loop()
loop.run_until_complete(do_replace())
# found document: {'_id': ObjectId('...'), 'i': 50}
# replaced 1 document
# document is now {'_id': ObjectId('...'), 'key': 'value'}
复制代码
```

除了 **_id** 不变，**replace_one()** 会对修改后文档的所有字段做更新操作， **慎用** 。

**update_one()** 与MongoDB的修饰符运算符一起使用来更新文档的一部分并保持其余部分不变。我们将找到“i”为51的文档，并使用 **$set** 运算符将“key”设置为“value”：

```dart
async def do_update():
    coll = db.test_collection
    result = await coll.update_one({'i': 51}, {'$set': {'key': 'value'}})  # 仅新增或更改该文档的某个key
    print('updated %s document' % result.modified_count)
    new_document = await coll.find_one({'i': 51})
    print('document is now %s' % pprint.pformat(new_document))

loop = asyncio.get_event_loop()
loop.run_until_complete(do_update())
# updated 1 document
# document is now {'_id': ObjectId('...'), 'i': 51, 'key': 'value'}
复制代码
```

“key”设置为“value”，“i”仍为51。

**update_one()** 只影响它找到的第一个文档，您可以使用 **update_many()** 更新所有文档：

```dart
await coll.update_many({'i': {'$gt': 100}}, {'$set': {'key': 'value'}})
复制代码
```

### 删除文档

**delete_many()** 采用与语法相同的 **find()** 查询。**delete_many()** 立即删除所有匹配的文档。

```python
async def do_delete_many():
    coll = db.test_collection
    n = await coll.count_documents({})
    print('%s documents before calling delete_many()' % n)
    result = await db.test_collection.delete_many({'i': {'$gte': 1000}})
    print('%s documents after' % (await coll.count_documents({})))

loop = asyncio.get_event_loop()
loop.run_until_complete(do_delete_many())
# 2000 documents before calling delete_many()
# 1000 documents after
复制代码
```

### Commands

MongoDB上的所有操作都在内部实现为命令。

使用以下 **command()** 方法 运行它们 **AsyncIOMotorDatabase** ：

```css
from bson import SON

async def use_distinct_command():
    response = await db.command(SON([("distinct", "test_collection"),
                                     ("key", "i")]))

loop = asyncio.get_event_loop()
loop.run_until_complete(use_distinct_command())
复制代码
```

由于命令参数的顺序很重要，因此不要使用Python dict来传递命令的参数。相反，养成使用PyMongo附带bson.SON的 **bson** 模块的习惯。

许多命令都有特殊的辅助方法，例如 **create_collection()** or **aggregate()** ，但这些方便的命令基于 **command()** 方法。

推荐阅读：[官方文档](https://link.juejin.cn/?target=https%3A%2F%2Fmotor.readthedocs.io%2Fen%2Fstable%2F "https://motor.readthedocs.io/en/stable/"), [MongoDB的基本概念和操作](https://juejin.cn/post/6844903873207861261#heading-1 "https://juejin.cn/post/6844903873207861261#heading-1")

  
https://juejin.cn/post/6844903896813404173  