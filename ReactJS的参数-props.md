⽗组件通过向⼦组件传递 props 通信。⼦组件 通过对⽗组件暴露注册函数的接⼝来通知⽗组 件更新⾃身状态

用法: 

1. 使用 ES6 类写法，用 **this.props.属性名** 来取值。
2. 可以多层 props 来传值，在 ReactDOM.render 定义属性值，传给调用方法 App，再在调用的ES6类调用中用 props.属性直接赋值过去。



```javascript

var myStyle = {color:'red',textAlign:'center'}

class Name extends React.Component {
  render() {
    return <h1 style={myStyle}>网站名称：{this.props.name}</h1>;
  }
}
class Url extends React.Component {
  render() {
    return <h1>网站地址：{this.props.url}</h1>;
  }
}
class Nickname extends React.Component {
  render() {
    return <h1>网站地址：{this.props.nickname}</h1>;
  }
}

function App(props) {
    return (
        <div>
            <Name name={props.name}/>
            <Url  url={props.url}/>
            <Nickname  nickname={props.nickname}/>
        </div>
    );
}
																																																																										```

多个属性的传入注意不用逗号或分号隔开而是空格符隔开:

```javascript

ReactDOM.render(
     <App name={"菜鸟教程"} url={"http://www.runoob.com"} nickname={"Runoob"}/>,
    document.getElementById('example')
);

```

[React 元素渲染 | 菜鸟教程](https://www.runoob.com/react/react-rendering-elements.html)

#javascript 

#react

