

自python3.5开始，PEP484为python引入了类型注解(type hints)，typing的主要作用有：
1. 类型检查，防止运行时出现参数、返回值类型不符。
2. 作为开发文档附加说明，方便使用者调用时传入和返回参数类型。
3. 模块加入不会影响程序的运行不会报正式的错误，pycharm支持typing检查错误时会出现黄色警告。


我们再看下一些相对复杂的数据结构，例如列表、元组、字典等类型怎么样来声明。 可想而知了，列表用 list 表示，元组用 tuple 表示，字典用 dict 来表示，那么很自然地，在声明的时候我们就很自然地写成这样了：

```python
names: list = ['Germey', 'Guido']
version: tuple = (3, 7, 4)
operations: dict = {'show': False, 'sort': True}
```

这么看上去没有问题，确实声明为了对应的类型，但实际上并不能反映整个列表、元组的结构，比如我们只通过类型注解是不知道 names 里面的元素是什么类型的，只知道 names 是一个列表 list 类型，实际上里面都是字符串 str 类型。我们也不知道 version 这个元组的每一个元素是什么类型的，实际上是 int 类型。但这些信息我们都无从得知。

**因此说，仅仅凭借 list、tuple 这样的声明是非常 “弱” 的，我们需要一种更强的类型声明。** 

这时候我们就需要借助于 typing 模块了，它提供了非常 “强 “的类型支持，比如 `List[str]`、`Tuple[int, int, int]` 则可以表示由 str 类型的元素组成的列表和由 int 类型的元素组成的长度为 3 的元组。所以上文的声明写法可以改写成下面的样子：

```python
from typing import List, Tuple, Dict  
  
names: List[str] = ['Germey', 'Guido']  
version: Tuple[int, int, int] = (3, 7, 4)  
operations: Dict[str, bool] = {'show': False, 'sort': True}  
```


这样一来，变量的类型便可以非常直观地体现出来了。 目前 typing 模块也已经被加入到 Python 标准库中，不需要安装第三方模块，我们就可以直接使用了。

[Python 中 typing 模块和类型注解的使用 | 静觅](https://cuiqingcai.com/7071.html#List)