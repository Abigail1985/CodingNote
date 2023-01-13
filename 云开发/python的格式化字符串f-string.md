
f-string，亦称为格式化字符串常量（formatted string literals），是Python3.6新引入的一种字符串格式化方法，主要目的是使格式化字符串的操作更加简便。f-string在形式上是以 f 或 F 修饰符引领的字符串（f'xxx' 或 F'xxx'），以大括号 {} 标明被替换的字段；

f-string在本质上并不是字符串常量，而是一个在运行时运算求值的表达式：

>While other string literals always have a constant value, formatted strings are really expressions evaluated at run time. （与具有恒定值的其它字符串常量不同，格式化字符串实际上是运行时运算求值的表达式。） 
>
>—— Python Documentation 


f-string在功能方面不逊于传统的%-formatting语句和str.format()函数，同时性能又优于二者，且使用起来也更加简洁明了，因此对于Python3.6及以后的版本，推荐使用f-string进行字符串格式化。

## 简单使用

f-string用大括号 `{}` 表示被替换字段，其中直接填入替换内容：

```python
>>> name = 'Eric'
>>> f'Hello, my name is {name}'
'Hello, my name is Eric'

>>> number = 7
>>> f'My lucky number is {number}'
'My lucky number is 7'

>>> price = 19.99
>>> f'The price of this book is {price}'
'The price of this book is 19.99'

```

## 表达式求值与函数调用

f-string的大括号 `{}` 可以填入表达式或调用函数，Python会求出其结果并填入返回的字符串内：

```python
>>> f'A total number of {24 * 8 + 4}'
'A total number of 196'

>>> f'Complex number {(2 + 2j) / (2 - 3j)}'
'Complex number (-0.15384615384615388+0.7692307692307692j)'

>>> name = 'ERIC'
>>> f'My name is {name.lower()}'
'My name is eric'

>>> import math
>>> f'The answer is {math.log(math.pi)}'
'The answer is 1.1447298858494002'
```

## 引号、大括号与反斜杠

f-string大括号内所用的引号不能和大括号外的引号定界符冲突，可根据情况灵活切换 `'` 和 `"`：

```python
>>> f'I am {"Eric"}'
'I am Eric'
>>> f'I am {'Eric'}'
  File "<stdin>", line 1
    f'I am {'Eric'}'
                ^
SyntaxError: invalid syntax

```

若 `'` 和 `"` 不足以满足要求，还可以使用 `'''` 和 `"""`：

```python
>>> f"He said {"I'm Eric"}"
  File "<stdin>", line 1
    f"He said {"I'm Eric"}"
                ^
SyntaxError: invalid syntax

>>> f'He said {"I'm Eric"}'
  File "<stdin>", line 1
    f'He said {"I'm Eric"}'
                  ^
SyntaxError: invalid syntax

>>> f"""He said {"I'm Eric"}"""
"He said I'm Eric"
>>> f'''He said {"I'm Eric"}'''
"He said I'm Eric"
```

[Python格式化字符串f-string概览_sunxb10的博客-CSDN博客_python fstring](https://blog.csdn.net/sunxb10/article/details/81036693)