React 的组件可以定义为 class 或函数的形式。class 组件目前提供了更多的功能，这些功能将在此章节中详细介绍。如需定义 class 组件，需要**继承** `React.Component`：

```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

在 `React.Component` 的子类中有个**必须定义**的 [`render()`](https://zh-hans.reactjs.org/docs/react-component.html#render) 函数。本章节介绍其他方法均为可选。

**我们强烈建议你不要创建自己的组件基类。** 在 React 组件中，[代码重用的主要方式是组合而不是继承](https://zh-hans.reactjs.org/docs/composition-vs-inheritance.html)。

当 `render` 被调用时，它会检查 `this.props` 和 `this.state` 的变化并返回以下类型之一：

-   **React 元素**。通常通过 JSX 创建。例如，`<div />` 会被 React 渲染为 DOM 节点，`<MyComponent />` 会被 React 渲染为自定义组件，无论是 `<div />` 还是 `<MyComponent />` 均为 React 元素。
-   **数组或 fragments**。 使得 render 方法可以返回多个元素。欲了解更多详细信息，请参阅 [fragments](https://zh-hans.reactjs.org/docs/fragments.html) 文档。
-   **Portals**。可以渲染子节点到不同的 DOM 子树中。欲了解更多详细信息，请参阅有关 [portals](https://zh-hans.reactjs.org/docs/portals.html) 的文档
-   **字符串或数值类型**。它们在 DOM 中会被渲染为文本节点
-   **布尔类型或 `null`**。什么都不渲染。（主要用于支持返回 `test && <Child />` 的模式，其中 test 为布尔类型。)

`render()` 函数应该为纯函数，这意味着在不修改组件 state 的情况下，每次调用时都返回相同的结果，并且它不会直接与浏览器交互。


### constructor()

```
constructor(props)
```

**如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。**

在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，应在其他语句之前调用  `super(props)`。否则，`this.props` 在构造函数中可能会出现未定义的 bug。

通常，在 React 中，构造函数仅用于以下两种情况：

-   通过给 `this.state` 赋值对象来初始化[内部 state](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html)。
-   为[事件处理函数](https://zh-hans.reactjs.org/docs/handling-events.html)绑定实例

在 `constructor()` 函数中**不要调用 `setState()` 方法**。如果你的组件需要使用内部 state，请直接在构造函数中为 **`this.state` 赋值初始 state**：

```
constructor(props) {
  super(props);
  // 不要在这里调用 this.setState()
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
}
```

只能在构造函数中直接为 `this.state` 赋值。如需在其他方法中赋值，你应使用 `this.setState()` 替代。

要避免在构造函数中引入任何副作用或订阅。如遇到此场景，请将对应的操作放置在 `componentDidMount` 中。