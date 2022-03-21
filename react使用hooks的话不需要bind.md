There's no need to bind functions/callbacks in functional components since there's no `this` in functions. In classes, it was important to bind `this` because we want to ensure that the `this`in the callbacks referred to the component's instance itself. However, doing `.bind` in the constructor has another useful property of creating the functions _once_ during the entire lifecycle of the component and a new callback wasn't created in every call of `render()`. To do only initialize the callback once using React hooks, you would use `useCallback`.

### Classes

```javascript
class Foo extends Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    console.log('Click happened');
  }

  render() {
    return <Button onClick={this.handleClick}>Click Me</Button>;
  }
}
```

### Hooks

```javascript
function Foo() {
  const memoizedHandleClick = useCallback(
    () => {
      console.log('Click happened');
    },
    [], // Tells React to memoize regardless of arguments.
  );
  return <Button onClick={memoizedHandleClick}>Click Me</Button>;
}
```

[javascript - How can I bind function with hooks in React? - Stack Overflow](https://stackoverflow.com/questions/53215067/how-can-i-bind-function-with-hooks-in-react)