在 JavaScript 中，有三种包含字符串的方式。

1.  双引号：`"Hello"`.
2.  单引号：`'Hello'`.
3.  反引号：`` `Hello` ``.

双引号和单引号都是“简单”引用，在 JavaScript 中两者几乎没有什么差别。

反引号是 **功能扩展** 引号。它们允许我们通过将变量和表达式包装在 `${…}` 中，来将它们嵌入到字符串中。例如：

```javascript
let name = "John"; 
// 嵌入一个变量 
alert( `Hello, _${__name}_!` ); // Hello, John! 
// 嵌入一个表达式 
alert( `the result is _${__1 + 2}_` ); // the result is 3
```
