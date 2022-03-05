我们学习了与用户交互的 3 个浏览器的特定函数：

`alert`显示信息。

```js
alert("Hello");
```

`prompt`显示信息要求用户输入文本。点击确定返回文本，点击取消或按下 Esc 键返回 `null`。

```js
let age = prompt('How old are you?', 100); 
alert(`You are ${age} years old!`); // You are 100 years old!
```

`confirm` 显示信息等待用户点击确定或取消。点击确定返回 `true`，点击取消或按下 Esc 键返回 `false`。

```js
let isBoss = confirm("Are you the boss?"); 
alert( isBoss ); // 如果“确定”按钮被按下，则显示 true
```

这些方法都是模态modal的：它们暂停脚本的执行，并且不允许用户与该页面的其余部分进行交互，直到窗口被解除。

上述所有方法共有两个限制：

1.  模态窗口的确切位置由浏览器决定。通常在页面中心。
2.  窗口的确切外观也取决于浏览器。我们不能修改它。