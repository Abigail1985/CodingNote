App.js
```js
import './App.css';
function App() {
  return (
    <p>
      Hello world 
    </p>
  );
}
export default App;
```

Index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
reportWebVitals();

```

很明显， App是一个component。APP 是DOM Tree最顶端的component, 它是一个html 文件。 App.js 里面的App function 返回这个html component（文件），可以用 <App / >来表示， App.js 本身并没有render功能。 想要render这个component，我们要在index.js 里面把这个component render 出来。就把app当作普通的component 就可以了
