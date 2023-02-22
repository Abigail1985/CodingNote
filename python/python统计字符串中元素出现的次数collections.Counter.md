
```python
#对列表作用
list_01 = [1,9,9,5,0,8,0,9]  
print(Counter(list_01))  
#Counter({9: 3, 0: 2, 1: 1, 5: 1, 8: 1})
 
#对字符串作用
temp = Counter('abcdeabcdabcaba')
print(temp)  
#Counter({'a': 5, 'b': 4, 'c': 3, 'd': 2, 'e': 1})

```

以上其实是两种使用方法，一种是直接用，一种是实例化以后使用,如果要频繁调用的话，显然后一种更简洁
