#### 扩展语句

[扩展语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)符允许一个表达式在原地展开， 当需要多个参数 (比如函数调用时) 或者多个值(比如字面量数组) 。

**例如：**现在你有一个数组，你想创建一个新数组，并将刚才那个作为它的一部分，用array的字面语法是不够的，你不得不写一些代码实现它，比如用些`push`, `splice`, `concat等等。但是用`spread syntax就没问题了：

```
var parts = ['shoulder', 'knees'];
var lyrics = ['head', ...parts, 'and', 'toes'];
```

类似的，扩展语句也可以用在函数调用的时候:

```
function f(x, y, z) { }
var args = [0, 1, 2];
f(...args);
```