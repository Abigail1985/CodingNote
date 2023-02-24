
if not 判断是否为NONE  
代码中经常会有判断变量是否为NONE的情况,主要有三种写法:

第一种: if x is None(最清晰)

第二种: if not x

第三种: if not x is None

```python
>>> x = 1  
>>> not x  
False  
>>> x = [1]  
>>> not x  
False  
>>> x = 0  
>>> not x  
True  
>>> x = [0]         # You don't want to fall in this one.  
>>> not x  
False 
```

使用if not x这种写法的前提是：
**必须清楚x等于None, False, 空字符串"", 0, 空列表[], 空字典{}, 空元组()时对你的判断没有影响才行**  

在[python](https://so.csdn.net/so/search?q=python&spm=1001.2101.3001.7020)中 None, False, 空字符串"", 0, 空列表[], 空字典{}, 空元组()都相当于False  

因此在使用列表的时候，如果你想区分x==[]和x==None两种情况的话, 此时`if not x:`将会出现问题：

```python

>>> x = []  
>>> y = None  
>>>   
>>> x is None  
False  
>>> y is None  
True  
>>>   
>>>   
>>> not x  
True  
>>> not y  
True  
>>>   
>>>   
>>> not x is None  
>>> True  
>>> not y is None  
False  
>>> 
```


