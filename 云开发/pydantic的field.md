
Field 可用于提供有关字段和验证的额外信息，如设置必填项和可选，设置最大值和最小值，字符串长度等限制

关于 Field 字段参数说明
- Field(None) 是可选字段，不传的时候值默认为None
- Field(…) 是设置必填项字段
- title 自定义标题，如果没有默认就是字段属性的值
- description 定义字段描述内容


```python
class Dname:

STR = "The display name of the photographer"

MAX_LENGTH = 16

PATH_PARAM = Path(..., title = STR, max_length = MAX_LENGTH)

  

class Fname:

STR = "The first name of the photographer"

MAX_LENGTH = 32

  

class Lname:

STR = "The last name of the photographer"

MAX_LENGTH = 32

  

class Interests:

STR = "The interests of the photographer"

  

class PhotographerDesc(BaseModel):

display_name: str = Field (None, title = Dname.STR, max_length = Dname.MAX_LENGTH)

first_name: str = Field (None, title = Fname.STR, max_length = Dname.MAX_LENGTH)

last_name: str = Field (None, title = Lname.STR, max_length = Lname.MAX_LENGTH)

interests: List[str] = Field (None, title = Interests.STR)
```

![[Pasted image 20230111205946.png]]

[FastAPI（13）- 详解 Fields，针对 Pydantic Model 内部字段添加额外校验和元数据 - 小菠萝测试笔记 - 博客园](https://www.cnblogs.com/poloyy/p/15312715.html)