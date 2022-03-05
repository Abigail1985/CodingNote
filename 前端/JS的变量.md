如果将变量想象成一个“数据”的盒子，盒子上有一个唯一的标注盒子名字的贴纸。这样我们能更轻松地掌握“变量”的概念。

例如，变量 `message` 可以被想象成一个标有 `"message"` 的盒子，盒子里面的值为 `"Hello!"`：

我们可以在盒子内放入任何值。**我们可以将任何类型的值存入变量。例如，一个变量可以在前一刻是个字符串，下一刻就存储一个数字.** 允许这种操作的编程语言，例如 JavaScript，被称为“动态类型”（dynamically typed）的编程语言，意思是虽然编程语言中有不同的数据类型，但是你定义的变量并不会在定义后，被限制为某一数据类型。

并且，这个盒子的值，我们想改变多少次，就可以改变多少次：
```js run
let message;

message = 'Hello!';

message = 'World!'; // 值改变了

alert(message);
```

当值改变的时候，之前的数据就被从变量中删除了：

在 JavaScript 中创建一个变量，我们需要用到 `let` 关键字。

下面的语句创建（也可以称为 **声明** 或者 **定义**）了一个名称为 “message” 的变量：

```js
let message;
```

现在，我们可以通过赋值运算符 `=` 为变量添加一些数据：

```js
let message;

*!*
message = 'Hello'; // 将字符串 'Hello' 保存在名为 message 的变量中
*/!*
```

现在这个字符串已经保存到与该变量相关联的内存区域了，我们可以通过使用该变量名称访问它：

```js run
let message;
message = 'Hello!';

*!*
alert(message); // 显示变量内容
*/!*
```

[变量](https://zh.javascript.info/variables)

#前端 
#javascript 