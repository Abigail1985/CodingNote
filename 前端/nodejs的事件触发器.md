Node.js 的大部分核心 API 都是围绕惯用的异步事件驱动架构构建的，在该架构中，某些类型的对象（称为"触发器"）触发命名事件，使 `Function` 对象（"监听器"）被调用。

例如：[`net.Server`](http://nodejs.cn/api/net.html#class-netserver) 对象在每次有连接时触发事件；[`fs.ReadStream`](http://nodejs.cn/api/fs.html#class-fsreadstream) 在打开文件时触发事件；[流](http://nodejs.cn/api/stream.html)在每当有数据可供读取时触发事件。

**所有触发事件的对象都是 `EventEmitter` 类的实例。 这些对象暴露了 `eventEmitter.on()` 函数，允许将一个或多个函数绑定到对象触发的命名事件。** 通常，事件名称是驼峰式字符串，但也可以使用任何有效的 JavaScript 属性键。

**当 `EventEmitter` 对象触发事件时，所有绑定到该特定事件的函数都会被同步地调用。** 被调用的监听器返回的任何值都将被忽略和丢弃。

以下示例展示了使用单个监听器的简单的 `EventEmitter` 实例。 **`eventEmitter.on()` 方法用于注册监听器，`eventEmitter.emit()` 方法用于触发事件。**

```js
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();
myEmitter.on('event', () => {
  console.log('an event occurred!');
});
myEmitter.emit('event');
```

