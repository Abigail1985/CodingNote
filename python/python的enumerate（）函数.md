[Python enumerate() 函数 | 菜鸟教程](https://www.runoob.com/python/python-func-enumerate.html)

## 描述

enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。

Python 2.3. 以上版本可用，2.6 添加 start 参数。

### 语法

以下是 enumerate() 方法的语法:

enumerate(sequence, [start=0])

### 参数

-   sequence -- 一个序列、迭代器或其他支持迭代对象。
-   start -- 下标起始位置的值。

### 返回值

返回 enumerate(枚举) 对象。

---

## 实例

以下展示了使用 enumerate() 方法的实例：

>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']  
>>> list(enumerate(seasons))  
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]  
>>> list(enumerate(seasons, start=1))       # 下标从 1 开始  
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]  

## 普通的 for 循环

>>> i = 0  
>>> seq = ['one', 'two', 'three']  
>>> for element in seq:  
...     print i, seq[i]  
...     i += 1  
...   
0 one  
1 two  
2 three  

## for 循环使用 enumerate

>>> seq = ['one', 'two', 'three']  
>>> for i, element in enumerate(seq):  
...     print i, element  
...   
0 one  
1 two  
2 three