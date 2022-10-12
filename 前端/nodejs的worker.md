一个worker是使用一个构造函数创建的一个对象(e.g. [`Worker()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/Worker "Worker()")) 运行一个命名的JavaScript文件 - 这个文件包含将在工作线程中运行的代码; workers 运行在另一个全局上下文中,不同于当前的[`window`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window). 因此，在 [`Worker`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker) 内通过 [`window`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window)获取全局作用域 (而不是[`self`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/self "self")) 将返回错误。



### [生成一个专用worker](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers#%E7%94%9F%E6%88%90%E4%B8%80%E4%B8%AA%E4%B8%93%E7%94%A8worker "Permalink to 生成一个专用worker")

创建一个新的worker很简单。你需要做的是调用[`Worker()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/Worker "Worker()") 的构造器，指定一个脚本的URI来执行worker线程（main.js）：

```
var myWorker = new Worker('worker.js');
```