

### 创建项目
可以用已存在的空目录来创建项目：

```
mkdir hello-modern 
cd hello-modern
npx @modern-js/create
```

### 导入todomvc的css模块
在项目根目录运行:
```
pnpm install todomvc-app-css
```

仓库是[GitHub - tastejs/todomvc-app-css: CSS for TodoMVC apps](https://github.com/tastejs/todomvc-app-css)

之后查看项目根目录的package.json,发现多了:
```
"dependencies": {

"@modern-js/runtime": "^1.0.0",

"react": "^17.0.1",

"react-dom": "^17.0.1",

"todomvc-app-css": "^2.4.2"

},
```

[前端手记 TodoMVC 之 CSS 篇](https://zddhub.com/memo/2016/08/27/todomvc-css.html)


### 使用React构建Todolist

[React 哲学 – React](https://zh-hans.reactjs.org/docs/thinking-in-react.html)


