## GitHub

https://github.com/ycaptain/modernjs-todomvc

## 需求分析

https://github.com/tastejs/todomvc/blob/master/app-spec.md#functionality

-   No Todos: 隐藏 `#main` 和 `#footer`
    

-   New Todo:
    
    -   利用 `autofous` 属性自动聚焦输入框
        
    -   回车创建 `todo`
        
    -   `trim()` 清空输入前后的空格
        
    -   空检测，无内容不创建新 `todo`
        

-   Mark all as complete
    
    -   标记全部 `todos` 为完成
        

-   Item
    
    -   点击 `checkbox` 修改 `todo` 的状态为 完成/未完成
        
    -   双击 `<label>` 进入编辑模式，将 `.editing` 加入 `li` 的 `class`
        
    -   `hover` 时展示销毁按钮，点击删除 `.destroy` 该todo
        

-   Editing
    
    -   编辑态隐藏 `item` 的其它按钮
        
    -   当 失焦 `blur` 或点回车时，保存编辑结果，并从 `li` 的 `class` 中移除 `.editing`
        
    -   `trim()` 清空输入前后的空格
        
    -   空检测，无内容则删除该 `todo`
        
    -   当按下 退出键 `escapse` 时，退出编辑态，恢复编辑前的数据
        

-   Counter
    
    -   展示当前剩余未完成 `todo` 数量
        
    -   数字用标签 `<strong>` 包装
        
    -   处理单复数，`item or items`
        

-   Clear completed button
    
    -   点击 `Clear completed` 删除所有完成项
        
    -   当没有未完成项时隐藏该按钮
        

-   Persistence
    
    -   将数据保存到 `localStorage`
        
    -   使用字段名 `id`, `title`, `completed`
        
    -   `localStorage` 字段名: `todos-[framework]`
        
    -   编辑状态不保存
        

-   Routing
    
    -   设置路由状态为 `#/` (all - 默认), `#/active`, `#/completed`
        
    -   当路由变化时，过滤 `todo list` 中的数据为对应数据
        
    -   更新 `class` `selected` 到对应的路由按钮上
        

## 开发过程

### 初始化

#### 创建 GitHub Repo

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=MjRiZTE5MjM3MzQ2MzhmYTNhY2I2MDY5M2FlYWNjYTRfSTVPbzlpb2xpaG1VM25oeHpGOUpnV21vbXdja3B3eFBfVG9rZW46Ym94Y25PS09EcWlXY2duaTQyc0hWNDVBNnhjXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

  

#### 创建项目

```Shell
npx @modern-js/create modernjs-todomvc
```

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=NzVkMDc2YWIzYzE0MmRjMDFkZDNmNmFhNWNiYzA1MzdfNzhpNFA4R0ZEdkQ0aTNDWjdDTjFKZTVQclpvRFBLb2hfVG9rZW46Ym94Y242ZnUyTWVTSTQwVE54ZFgySElwSXJmXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=YjEzMGNiYTZlZmZjZGViMGYyM2MxYWZmNmNmMGU3ZmFfV0FXcHc5dU9hWjEyT0xjYUlqc29zMkVPaWVqSXhUVjdfVG9rZW46Ym94Y25nd3ZsVlhPRWVPWVhkQUlrWmFZUkhjXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=NTllOGFmYjMwNjU4NjFhODExZWFiYzkwMmFkNTU2NzVfUVdDTTVRbG1VbzBNeGNqRWpMT2VyUjRXRExhVW1yMjVfVG9rZW46Ym94Y25QdVJmaTFiRWVwWFMweVRJNjZMbHdkXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

#### 绑定 GitHub

```Shell
cd modernjs-todomvc
git remote add origin <GitHub 仓库地址>
git push origin -u origin main
```

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=YWY2MzdjMTc0YTM1Mjk1YTU2N2E1Y2RhN2ZhYTkwMDBfbmx5Z0tnSmJwQlozNDhFRnY2NWdZbDEwUERLbzhrbkRfVG9rZW46Ym94Y25DUUIxemVDS0NMbEVtaWlHdGdHWlljXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

### 样式还原

#### 进入示例网站，分析样式来源

> 示例网站：https://todomvc.com/examples/react/#/

进入示例网站，打开 DevTools，分析得知，项目中只有两个 CSS 文件，并且所有样式均来自这两个 CSS 文件，不存在行内样式。

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=NWZjZjUxMzUzN2RkY2VkMzBhOGExNThmYTgxMzVjMGRfUk11Y1ZmeE5KZHUwTkViNVhNZ1NCZkd0SnBVY0lpbmlfVG9rZW46Ym94Y244YjhIQVVHOGxOMW9TcThoM0NBRnRkXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

#### 复制样式文件

右键点击 CSS 文件链接，将 `base.css` 和 `index.css` 文件复制到项目中。

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=NWFiZjA0MTZjODliODJlNDIzYWUwNDY1YTA4NGU5Njlfa2psRWJIUEx3RUJ6YlAyaHFPMG9XTXZFNENSZHdTVHpfVG9rZW46Ym94Y252d1RITjJxTXFhZ1A3YjRQYkJWdVdtXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2QyZTg5NzdlOTcwZDhlYTVhZmE5ZjlmMGJjYjNhMTdfYzZ4VkwyVWVpb2h1emZUanZwNlJSem5USEdMRTJzMWlfVG9rZW46Ym94Y244QUo2NGhDMTk1SVJqcFkyS0JPRTZjXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=OGU5NmJhN2Q1NDI5ZmUzMzNjMzQ2ZmFlN2I2N2JkODBfelFmWnpSZUJnZkdQYm1XVDZxa3VaeGp0UFE1WDNNU3NfVG9rZW46Ym94Y25sQ01WdmVYYkFpMFVEUWQxV3JMWHJjXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

#### 载入样式文件

在 `src/App.tsx` 中替换原来的样式文件

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDljYzkxM2RkMjQxNmU3ZTEzM2RhNGEzNTZhYzA4Y2VfT0x5MHhqbG1xMlhPRFhNeVJmU0hKWm4wME4zd3Q1eEdfVG9rZW46Ym94Y252VkhPTlBQdTcwQllSUWJjenFtckdmXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

#### 查看示例 DevTools Elements 面板，分析 HTML 结构

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=Nzg2NGU5YWE5ZmE5YzRjYzAwY2VmZDgzZTVkMWNjMTVfVzlqZldqem5jN3h0VzN4MXpHNkluV0xDMWNyTjRWeW5fVG9rZW46Ym94Y25LeWZxRUtvU3dpZkJzTlg1SktvQThiXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

分析得知，Todo MVC 相关 HTML 结构都在 class 属性为 `todoapp` 的 `<section>` 元素内。

  

section 元素下用一个 `<div>` 标签包裹，其下分为三大模块：

-   header: 包含一个大标题 todos 和一个 input 输入框
    

-   section:
    
    -   全选按钮 input 和样式标签 label
        
    -   无序列表元素 ul，包含多个 li，其中每个 li 中包含
        
        -   div
            
            -   checkbox，标记该 todo 完成情况
                
            -   label，表示该元素的内容
                
            -   button，删除按钮
                
        -   input，编辑态的输入框，当 li 的 class 包含 `editing` 时展示
            

-   footer
    
    -   span，展示剩余未完成 todo 个数
        
    -   ul，过滤路由，切换 All，Active，Completed 三种状态
        

> Tips: 鼠标 hover 到 Elements 面板中的标签上，网页对应的模块会高亮展示

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2U1NmVkMzAzY2NmZGYyMjRlMTdkMTY4NTkyM2YyMWZfTElmbE9SQnJvOXpKNGdxVEhucnhONFJRd1RpWExYRmRfVG9rZW46Ym94Y25NcERBR3VleXlQZUc4SkVOMkk0anhoXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

#### 还原 header

创建文件夹 `src/components`，用于存放视图组件

创建文件 `src/components/Header.tsx`

```TypeScript
import React from 'react';

export function Header() {
  return (
    <header className="header">
      <h1>todos</h1>
      <input className="new-todo" placeholder="What needs to be done?" />
    </header>
  );
}
```

移除 `src/App.tsx` 默认代码，导入 Header 组件

```TypeScript
import React from 'react';
import { Header } from './components/Header';
import './styles/index.css';
import './styles/base.css';

function App() {
  return (
    <section className="todoapp">
      <Header />
    </section>
  );
}
export default App;
```

运行程序，查看效果

```TypeScript
pnpm dev
```

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=YjllNGNlNjRmNTc3MjUzZGM3NjM0OWYwZDYzOTI4ZThfOWozaUxPbWNEZk15eU11QmZLZkZRMUozRW52eWQ1aWtfVG9rZW46Ym94Y25OOHJibkpad25DYU1LUmpod2lsZ1lnXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

#### 还原 `main`

创建文件 `src/components/Main.tsx`

```TypeScript
import React from 'react';

export function Main() {
  return (
    <section className="main">
      <input id="toggle-all" className="toggle-all" type="checkbox" />
      <label htmlFor="toggle-all"></label>
      <ul className="todo-list">
        <li>
          <div className="view">
            <input className="toggle" type="checkbox" />
            <label>Hello World</label>
            <button className="destroy" type="button"></button>
          </div>
          <input className="edit" value="Hello" />
        </li>
        <li>
          <div className="view">
            <input className="toggle" type="checkbox" />
            <label>Gemo</label>
            <button className="destroy" type="button"></button>
          </div>
          <input className="edit" value="ByteDance" />
        </li>
      </ul>
    </section>
  );
}
```

在 `src/App.tsx` 中载入 Main 组件

```TypeScript
import React from 'react';
import { Header } from './components/Header';
import { Main } from './components/Main';
import './styles/index.css';
import './styles/base.css';

function App() {
  return (
    <section className="todoapp">
      <Header />
      <Main />
    </section>
  );
}
export default App;
```

预览效果

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=NDNmZjllNGVlZmFmOWU1NmM5MzcxNDU3ZjZhMmNiZTBfRUNyRzY5TzlFQW1Yc0RBcUVSdHRKSHo1QmQ1aXFNZzNfVG9rZW46Ym94Y25XbmxhZnVHUHZaaU1LVkRma283RVZnXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

#### 还原 footer

创建文件 `src/components/Footer.tsx`

```TypeScript
import React from 'react';

export function Footer() {
  return (
    <footer className="footer">
      <span className="todo-count">
        <strong>2</strong>
        <span> </span>
        <span>items</span>
        <span> left</span>
      </span>
      <ul className="filters">
        <li>
          <a href="#/" className="selected">
            All
          </a>
        </li>
        <span> </span>
        <li>
          <a href="#/active">Active</a>
        </li>
        <span> </span>
        <li>
          <a href="#/completed">Completed</a>
        </li>
      </ul>
      <button className="clear-completed" type="button">
        Clear completed
      </button>
    </footer>
  );
}
```

在 `src/App.tsx` 中载入 Footer 组件

```TypeScript
import React from 'react';
import { Header } from './components/Header';
import { Main } from './components/Main';
import { Footer } from './components/Footer';
import './styles/index.css';
import './styles/base.css';

function App() {
  return (
    <section className="todoapp">
      <Header />
      <Main />
      <Footer />
    </section>
  );
}
export default App;
```

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDdhOWYyNDhhOGQwMzdhYjJmMjMzZjY4ODgyY2UzMTBfQ09ZelZLb2F2cmpCSjJncjg1TGg5eVNEcHoxdkxWU0dfVG9rZW46Ym94Y25xVTdlWElYYzVqeG96Q2dGR3h4SktmXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

### 功能实现

所有功能逻辑都在 Main 和 Footer 组件，Header 组件不包含功能逻辑。

回顾 [需求分析](https://bytedance.feishu.cn/docx/doxcn06C6xFkQjpgnyJFH8rbUVh#doxcn2K0iO0oMmosYsNPKhwYn3b)

  

#### 下载所需依赖

```Shell
pnpm add nanoid classnames
pnpm add -D @types/nanoid
```

  

#### 创建 `todoModel` 管理 `todos` 的「增删改查」

创建 `src/utils.ts`

```TypeScript
import { nanoid } from 'nanoid';
import { TodoItem } from './models/todoModel';

// 生成唯一 id
export function generateId() {
  return nanoid();
}
// 从 localStorage 获取或向 localStorage 同步数据
export function store(namespace: string, data?: Array<TodoItem>) {
  if (data) {
    return localStorage.setItem(namespace, JSON.stringify(data));
  }

  const localStore = localStorage.getItem(namespace);
  return (localStore && JSON.parse(localStore)) || [];
}
```

创建 `src/models/todoModel`

```TypeScript
import { generateId, store } from '../utils';

export class TodoModel {
  // 成员变量类型定义
  key: string;

  todos: Array<TodoItem>;

  onChanges: Array<Subscriber>;

  // 初始化 todos, 通过 key 从 store 中读取历史缓存
  constructor(key: string) {
    this.key = key;
    this.todos = store(key);
    this.onChanges = [];
  }

  // 添加监听器
  subscribe(onChange: Subscriber) {
    this.onChanges.push(onChange);
  }

  // 触发监听器，并更新 store
  inform() {
    store(this.key, this.todos);
    this.onChanges.forEach(function (cb) {
      cb();
    });
  }

  // 添加 todo
  addTodo(title: string) {
    this.todos = this.todos.concat({
      id: generateId(),
      title,
      completed: false,
    });

    this.inform();
  }

  // 切换所有 todo 到 完成/未完成 状态
  toggleAll(checked: boolean) {
    this.todos = this.todos.map(function (todo) {
      return {
        ...todo,
        completed: checked,
      };
    });
    
    this.inform();
  }

  // 切换特定 todo 状态
  toggle(todoToToggle: TodoItem) {
    this.todos = this.todos.map(function (todo) {
      return todo !== todoToToggle
        ? todo
        : {
            ...todo,
            completed: !todo.completed,
          };
    });

    this.inform();
  }

  // 删除特定 todo
  destroy(todo: TodoItem) {
    this.todos = this.todos.filter(function (candidate) {
      return candidate !== todo;
    });

    this.inform();
  }

  // 修改特定 todo 内容
  save(todoToSave: TodoItem, text: string) {
    this.todos = this.todos.map(function (todo) {
      return todo !== todoToSave
        ? todo
        : {
            ...todo,
            title: text,
          };
    });

    this.inform();
  }

  // 删除所有已完成 todo
  clearCompleted() {
    this.todos = this.todos.filter(function (todo) {
      return !todo.completed;
    });

    this.inform();
  }
}

// 订阅者类型定义
export type Subscriber = () => void;
// 单个 Todo 类型定义
export interface TodoItem {
  id: string;
  title: string;
  completed: boolean;
}
```

  

#### 引入 todoModel

修改 `src/App.tsx`

  

#### 创建新 todo

header 包含以下业务逻辑

-   编辑新 todo: handleChange
    

-   快捷键 Enter 创建新 todo: handleNewTodoKeyDown
    

  

修改 `src/components/Header.tsx`

```TypeScript
import React, { ChangeEvent, KeyboardEvent } from 'react';

interface Props {
  newTodo: string;
  handleNewTodoKeyDown: (event: KeyboardEvent<HTMLInputElement>) => void;
  handleNewTodoChange: (event: ChangeEvent<HTMLInputElement>) => void;
}

export function Header(props: Props) {
  const { newTodo, handleNewTodoChange, handleNewTodoKeyDown } = props;

  return (
    <header className="header">
      <h1>todos</h1>
      <input
        className="new-todo"
        placeholder="What needs to be done?"
        value={newTodo}
        onKeyDown={handleNewTodoKeyDown}
        onChange={handleNewTodoChange}
        autoFocus={true}
      />
    </header>
  );
}
```

  

修改 `src/App.tsx`

```TypeScript
import React, { ChangeEvent, KeyboardEvent, useState } from 'react';
import { Header } from './components/Header';
import { Main } from './components/Main';
import { Footer } from './components/Footer';
import { TodoModel } from './models/todoModel';
import './styles/index.css';
import './styles/base.css';

const KeyCodes = {
  Enter: 'Enter',
  Escape: 'Escape',
};

const model = new TodoModel('TodoMVC');

function App() {
  const [newTodo, setNewTodo] = useState('');

  const handleNewTodoKeyDown = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.code !== KeyCodes.Enter) {
      return;
    }

    event.preventDefault();

    const val = newTodo.trim();

    if (!val) {
      return;
    }

    model.addTodo(val);
    setNewTodo(val);
  };

  const handleNewTodoChange = (event: ChangeEvent<HTMLInputElement>) => {
    setNewTodo(event.target.value);
  };

  return (
    <section className="todoapp">
      <Header
        newTodo={newTodo}
        handleNewTodoChange={handleNewTodoChange}
        handleNewTodoKeyDown={handleNewTodoKeyDown}
      />
      <Main />
      <Footer />
    </section>
  );
}

export default App;
```

  

#### todo-list 业务逻辑

之前我们已经像素级还原了 `todo-list`，接下来需要基于真实数据来渲染

  

分析得出，每一个 `todo` 都有相同的功能，它们应作为组件复用，同时拥有相同的接口

  

对于组件外部，接口包括

-   `todo`: 元数据，包含 `todo` 的内容，完成状态和 `id`
    

-   `onToggle`: 切换 `todo` 的完成状态的回调函数
    

-   `onEdit`: 进入编辑态时触发，修改顶层维护的编辑对象（用于表示处于编辑态的 `todo`）的回调函数
    

-   `onSave`: 保存创建新 `todo` 时的回调函数
    

-   `onCancel`: 取消 `todo` 编辑态时的回调函数
    

-   `onDestroy`: 删除 `todo` 时的回调函数
    

  

对于组件内部，接口包括

-   `editText`: 临时保存的编辑态内容，初始值为 `todo.title`
    

-   `handleEdit`: 进入编辑态
    

-   `handleSubmit`: 创建新 `todo`
    

-   `handleChange`: 修改编辑态内容
    

-   `handleKeyDown`: 处理快捷键逻辑
    

  

首先，将之前像素级还原的相同结构的 `todo` 抽成一个独立的组件

  

创建文件 `src/components/Todo.tsx`

```TypeScript
import React, { ChangeEvent, useState, KeyboardEvent } from 'react';
import cx from 'classnames';
import { TodoItem } from '../models/todoModel';

interface Props {
  todo: TodoItem;
  editing: boolean;
  onToggle: () => void;
  onEdit: () => void;
  onSave: (v: string) => void;
  onCancel: () => void;
  onDestroy: () => void;
}

const KeyCodes = {
  Enter: 'Enter',
  Escape: 'Escape',
};

export function Todo(props: Props) {
  const { todo, editing, onToggle, onEdit, onSave, onCancel, onDestroy } =
    props;

  const [editText, setEditText] = useState(todo.title);

  const handleChange = (event: ChangeEvent<HTMLInputElement>) => {
    setEditText(event.target.value);
  };

  const handleEdit = () => {
    onEdit();
    setEditText(todo.title);
  };

  const handleSubmit = () => {
    const val = editText.trim();

    if (!val) {
      onDestroy();
      return;
    }

    onSave(val);
    setEditText(val);
  };

  const handleKeyDown = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.code === KeyCodes.Enter) {
      handleSubmit();
    } else if (event.code === KeyCodes.Escape) {
      onCancel();
      setEditText(todo.title);
    }
  };

  return (
    <li
      className={cx({
        editing,
        completed: todo.completed,
      })}>
      <div className="view">
        <input
          className="toggle"
          type="checkbox"
          checked={todo.completed}
          onChange={onToggle}
        />
        <label onDoubleClick={handleEdit}>{todo.title}</label>
        <button className="destroy" onClick={onDestroy} type="button"></button>
      </div>
      <input
        className="edit"
        value={editText}
        onChange={handleChange}
        onBlur={handleSubmit}
        onKeyDown={handleKeyDown}
      />
    </li>
  );
}
```

修改 `src/components/Main.tsx`

```TypeScript
import React from 'react';
import { TodoItem } from '../models/todoModel';
import { Todo } from './Todo';

interface Props {
  todos: Array<TodoItem>;
  editing: string;
  onToggle: (todo: TodoItem) => void;
  onEdit: (todo: TodoItem) => void;
  onSave: (v: TodoItem, val: string) => void;
  onCancel: () => void;
  onDestroy: (todo: TodoItem) => void;
}

export function Main(props: Props) {
  const { todos, editing, onToggle, onEdit, onSave, onCancel, onDestroy } =
    props;

  return (
    <section className="main">
      <input id="toggle-all" className="toggle-all" type="checkbox" />
      <label htmlFor="toggle-all"></label>
      <ul className="todo-list">
        {todos.map(todo => (
          <Todo
            key={todo.id}
            todo={todo}
            editing={editing === todo.id}
            onCancel={onCancel}
            onDestroy={() => onDestroy(todo)}
            onEdit={() => onEdit(todo)}
            onSave={(v: string) => onSave(todo, v)}
            onToggle={() => onToggle(todo)}
          />
        ))}
      </ul>
    </section>
  );
}
```

修改 `src/App.tsx`

```TypeScript
import React, { ChangeEvent, KeyboardEvent, useState } from 'react';
import { Header } from './components/Header';
import { Main } from './components/Main';
import { Footer } from './components/Footer';
import { TodoModel, TodoItem } from './models/todoModel';
import './styles/index.css';
import './styles/base.css';

const KeyCodes = {
  Enter: 'Enter',
  Escape: 'Escape',
};

const model = new TodoModel('TodoMVC');

function App() {
  const [newTodo, setNewTodo] = useState('');
  const [editing, setEditing] = useState('');

  const handleNewTodoKeyDown = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.code !== KeyCodes.Enter) {
      return;
    }

    event.preventDefault();

    const val = newTodo.trim();

    if (!val) {
      return;
    }

    model.addTodo(val);
    setNewTodo(val);
  };

  const handleNewTodoChange = (event: ChangeEvent<HTMLInputElement>) => {
    setNewTodo(event.target.value);
  };

  const onToggle = (todo: TodoItem) => {
    model.toggle(todo);
  };

  const onDestroy = (todo: TodoItem) => {
    model.destroy(todo);
  };

  const onEdit = (todo: TodoItem) => {
    setEditing(todo.id);
  };

  const onSave = (todo: TodoItem, val: string) => {
    model.save(todo, val);
    setEditing('');
  };

  const onCancel = () => {
    setEditing('');
  };

  return (
    <section className="todoapp">
      <Header
        newTodo={newTodo}
        handleNewTodoChange={handleNewTodoChange}
        handleNewTodoKeyDown={handleNewTodoKeyDown}
      />
      <Main
        editing={editing}
        todos={model.todos}
        onToggle={onToggle}
        onDestroy={onDestroy}
        onEdit={onEdit}
        onSave={onSave}
        onCancel={onCancel}
      />
      <Footer />
    </section>
  );
}

export default App;
```

预览效果

暂时无法在飞书文档外展示此内容

可以看到，这时我们可以创建新的 `todo`，但是切换 `todo` 完成状态和删除 `todo` 都没有直接生效，需要刷新或者进入编辑态才能拿到正确的视图。

> React 函数组件只有当内部的 state 或者传入的 props 发生变更时才会重渲染，重新走一遍函数内的逻辑。

  

实际上，即使是创建 `todo` 也不应响应到视图上。这是因为 model 是从 React 组件外传入，一次性执行。对于 `Main` 组件来说，上层组件传给它的 `todos` 已经固定，不会触发视图的更新。只有当页面刷新，从缓存中重新获取数据后才渲染了正确的视图。

  

这里我们切换 `todo` 完成状态和删除 `todo` 没有直接生效是符合预期的。

  

但是创建 `todo` 或进入编辑态时，我们更新了 `src/App.tsx` 中的状态，导致组件重渲染，将此时新的状态重新传递到了下层组件。

  

#### 监听 model 更新

`todoModel` 中，我们简单实现了 「观察者模式」，每当 `todos` 修改时，会调用 `inform()` 去通知所有通过 `model.subscribe` 订阅 `model` 事件的观察者。

  

> 注意：在订阅中直接重新更新整个应用不是常规操作，实际应用中可以尝试自己封装 hook 或者引入 redux, mobx, xstate 等状态管理库，利用第三方库封装好的接口进行状态同步和视图更新。

  

修改 `src/App.tsx`,引入 forceUpdate 刷新组件

```TypeScript
import React, { ChangeEvent, KeyboardEvent, useEffect, useState } from 'react';
import { Header } from './components/Header';
import { Main } from './components/Main';
import { Footer } from './components/Footer';
import { TodoModel, TodoItem } from './models/todoModel';
import './styles/index.css';
import './styles/base.css';

const KeyCodes = {
  Enter: 'Enter',
  Escape: 'Escape',
};

const model = new TodoModel('TodoMVC');

function App() {
  const [newTodo, setNewTodo] = useState('');
  const [editing, setEditing] = useState('');
  const [, forceUpdate] = useState(0);

  useEffect(() => {
    model.subscribe(() => forceUpdate(prev => prev + 1));
  }, []);

  const handleNewTodoKeyDown = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.code !== KeyCodes.Enter) {
      return;
    }

    event.preventDefault();

    const val = newTodo.trim();

    if (!val) {
      return;
    }

    model.addTodo(val);
    setNewTodo('');
  };

  const handleNewTodoChange = (event: ChangeEvent<HTMLInputElement>) => {
    setNewTodo(event.target.value);
  };

  const onToggle = (todo: TodoItem) => {
    model.toggle(todo);
  };

  const onDestroy = (todo: TodoItem) => {
    model.destroy(todo);
  };

  const onEdit = (todo: TodoItem) => {
    setEditing(todo.id);
  };

  const onSave = (todo: TodoItem, val: string) => {
    model.save(todo, val);
    setEditing('');
  };

  const onCancel = () => {
    setEditing('');
  };

  return (
    <section className="todoapp">
      <Header
        newTodo={newTodo}
        handleNewTodoChange={handleNewTodoChange}
        handleNewTodoKeyDown={handleNewTodoKeyDown}
      />
      <Main
        editing={editing}
        todos={model.todos}
        onToggle={onToggle}
        onDestroy={onDestroy}
        onEdit={onEdit}
        onSave={onSave}
        onCancel={onCancel}
      />
      <Footer />
    </section>
  );
}

export default App;
```

如此，视图将按预期更新。

  

#### Toggle All

修改 `src/components/Main.tsx` ，新增 `onToggleAll` props，计算当前未完成 todo 数量

```TypeScript
import React from 'react';
import { TodoItem } from '../models/todoModel';
import { Todo } from './Todo';

interface Props {
  todos: Array<TodoItem>;
  editing: string;
  onToggle: (todo: TodoItem) => void;
  onToggleAll: (checked: boolean) => void;
  onEdit: (todo: TodoItem) => void;
  onSave: (v: TodoItem, val: string) => void;
  onCancel: () => void;
  onDestroy: (todo: TodoItem) => void;
}

export function Main(props: Props) {
  const {
    todos,
    editing,
    onToggle,
    onToggleAll,
    onEdit,
    onSave,
    onCancel,
    onDestroy,
  } = props;

  const activeTodoCount = todos.reduce(function (accum, todo) {
    return todo.completed ? accum : accum + 1;
  }, 0);

  return (
    <section className="main">
      <input
        id="toggle-all"
        className="toggle-all"
        type="checkbox"
        checked={activeTodoCount === 0}
        onChange={event => onToggleAll((event.target as any).checked)}
      />
      <label htmlFor="toggle-all"></label>
      <ul className="todo-list">
        {todos.map(todo => (
          <Todo
            key={todo.id}
            todo={todo}
            editing={editing === todo.id}
            onCancel={onCancel}
            onDestroy={() => onDestroy(todo)}
            onEdit={() => onEdit(todo)}
            onSave={(v: string) => onSave(todo, v)}
            onToggle={() => onToggle(todo)}
          />
        ))}
      </ul>
    </section>
  );
}
```

修改 `src/App.tsx`

```TypeScript
import React, { ChangeEvent, KeyboardEvent, useEffect, useState } from 'react';
import { Header } from './components/Header';
import { Main } from './components/Main';
import { Footer } from './components/Footer';
import { TodoModel, TodoItem } from './models/todoModel';
import './styles/index.css';
import './styles/base.css';

const KeyCodes = {
  Enter: 'Enter',
  Escape: 'Escape',
};

const model = new TodoModel('TodoMVC');

function App() {
  const [newTodo, setNewTodo] = useState('');
  const [editing, setEditing] = useState('');
  const [, forceUpdate] = useState(0);

  useEffect(() => {
    model.subscribe(() => forceUpdate(prev => prev + 1));
  }, []);

  const handleNewTodoKeyDown = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.code !== KeyCodes.Enter) {
      return;
    }

    event.preventDefault();

    const val = newTodo.trim();

    if (!val) {
      return;
    }

    model.addTodo(val);
    setNewTodo('');
  };

  const handleNewTodoChange = (event: ChangeEvent<HTMLInputElement>) => {
    setNewTodo(event.target.value);
  };

  const onToggle = (todo: TodoItem) => {
    model.toggle(todo);
  };

  const onToggleAll = (checked: boolean) => {
    model.toggleAll(checked);
  };

  const onDestroy = (todo: TodoItem) => {
    model.destroy(todo);
  };

  const onEdit = (todo: TodoItem) => {
    setEditing(todo.id);
  };

  const onSave = (todo: TodoItem, val: string) => {
    model.save(todo, val);
    setEditing('');
  };

  const onCancel = () => {
    setEditing('');
  };

  return (
    <section className="todoapp">
      <Header
        newTodo={newTodo}
        handleNewTodoChange={handleNewTodoChange}
        handleNewTodoKeyDown={handleNewTodoKeyDown}
      />
      <Main
        editing={editing}
        todos={model.todos}
        onToggle={onToggle}
        onToggleAll={onToggleAll}
        onDestroy={onDestroy}
        onEdit={onEdit}
        onSave={onSave}
        onCancel={onCancel}
      />
      <Footer />
    </section>
  );
}

export default App;
```

  

#### 存在 todos 数据时才展示 Main 和 Footer

修改 `src/App.tsx`

```TypeScript
import React, { ChangeEvent, KeyboardEvent, useEffect, useState } from 'react';
import { Header } from './components/Header';
import { Main } from './components/Main';
import { Footer } from './components/Footer';
import { TodoModel, TodoItem } from './models/todoModel';
import './styles/index.css';
import './styles/base.css';

const KeyCodes = {
  Enter: 'Enter',
  Escape: 'Escape',
};

const model = new TodoModel('TodoMVC');

function App() {
  const [newTodo, setNewTodo] = useState('');
  const [editing, setEditing] = useState('');
  const [, forceUpdate] = useState(0);

  useEffect(() => {
    model.subscribe(() => forceUpdate(prev => prev + 1));
  }, []);

  const handleNewTodoKeyDown = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.code !== KeyCodes.Enter) {
      return;
    }

    event.preventDefault();

    const val = newTodo.trim();

    if (!val) {
      return;
    }

    model.addTodo(val);
    setNewTodo('');
  };

  const handleNewTodoChange = (event: ChangeEvent<HTMLInputElement>) => {
    setNewTodo(event.target.value);
  };

  const onToggle = (todo: TodoItem) => {
    model.toggle(todo);
  };

  const onToggleAll = (checked: boolean) => {
    model.toggleAll(checked);
  };

  const onDestroy = (todo: TodoItem) => {
    model.destroy(todo);
  };

  const onEdit = (todo: TodoItem) => {
    setEditing(todo.id);
  };

  const onSave = (todo: TodoItem, val: string) => {
    model.save(todo, val);
    setEditing('');
  };

  const onCancel = () => {
    setEditing('');
  };

  return (
    <section className="todoapp">
      <Header
        newTodo={newTodo}
        handleNewTodoChange={handleNewTodoChange}
        handleNewTodoKeyDown={handleNewTodoKeyDown}
      />
      {Boolean(model.todos.length) && (
        <>
          <Main
            editing={editing}
            todos={model.todos}
            onToggle={onToggle}
            onToggleAll={onToggleAll}
            onDestroy={onDestroy}
            onEdit={onEdit}
            onSave={onSave}
            onCancel={onCancel}
          />
          <Footer />
        </>
      )}
    </section>
  );
}

export default App;
```

![](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDUyYzFhNGY4OTY1M2Y2YTM5M2Y0MTZlYjg2ODkzYjlfRDNaQjZYSVNLdzZ6TkZWMWRuNmFXbWVYTkJQVG1taEJfVG9rZW46Ym94Y25ocG9tSWw2WXJMd2lTbUVHMHZwaktnXzE2NjMzMTEzNjg6MTY2MzMxNDk2OF9WNA)

#### 清空已完成 todo

修改 `src/components/Footer.tsx`

```TypeScript
import React from 'react';

interface Props {
  hasCompletedTodos: boolean;
  onClearCompleted: () => void;
}

export function Footer(props: Props) {
  const { hasCompletedTodos, onClearCompleted } = props;

  return (
    <footer className="footer">
      <span className="todo-count">
        <strong>2</strong>
        <span> </span>
        <span>items</span>
        <span> left</span>
      </span>
      <ul className="filters">
        <li>
          <a href="#/" className="selected">
            All
          </a>
        </li>
        <span> </span>
        <li>
          <a href="#/active">Active</a>
        </li>
        <span> </span>
        <li>
          <a href="#/completed">Completed</a>
        </li>
      </ul>
      {hasCompletedTodos && (
        <button
          className="clear-completed"
          onClick={onClearCompleted}
          type="button">
          Clear completed
        </button>
      )}
    </footer>
  );
}
```

修改 `src/App.tsx`

```TypeScript
import React, { ChangeEvent, KeyboardEvent, useEffect, useState } from 'react';
import { Header } from './components/Header';
import { Main } from './components/Main';
import { Footer } from './components/Footer';
import { TodoModel, TodoItem } from './models/todoModel';
import './styles/index.css';
import './styles/base.css';

const KeyCodes = {
  Enter: 'Enter',
  Escape: 'Escape',
};

const model = new TodoModel('TodoMVC');

function App() {
  const [newTodo, setNewTodo] = useState('');
  const [editing, setEditing] = useState('');
  const [, forceUpdate] = useState(0);

  const activeTodoCount = model.todos.reduce(function (accum, todo) {
    return todo.completed ? accum : accum + 1;
  }, 0);

  const completedTodoCount = model.todos.length - activeTodoCount;

  useEffect(() => {
    model.subscribe(() => forceUpdate(prev => prev + 1));
  }, []);

  const handleNewTodoKeyDown = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.code !== KeyCodes.Enter) {
      return;
    }

    event.preventDefault();

    const val = newTodo.trim();

    if (!val) {
      return;
    }

    model.addTodo(val);
    setNewTodo('');
  };

  const handleNewTodoChange = (event: ChangeEvent<HTMLInputElement>) => {
    setNewTodo(event.target.value);
  };

  const onToggle = (todo: TodoItem) => {
    model.toggle(todo);
  };

  const onToggleAll = (checked: boolean) => {
    model.toggleAll(checked);
  };

  const onDestroy = (todo: TodoItem) => {
    model.destroy(todo);
  };

  const onEdit = (todo: TodoItem) => {
    setEditing(todo.id);
  };

  const onSave = (todo: TodoItem, val: string) => {
    model.save(todo, val);
    setEditing('');
  };

  const onCancel = () => {
    setEditing('');
  };

  const onClearCompleted = () => {
    model.clearCompleted();
  };

  return (
    <section className="todoapp">
      <Header
        newTodo={newTodo}
        handleNewTodoChange={handleNewTodoChange}
        handleNewTodoKeyDown={handleNewTodoKeyDown}
      />
      {Boolean(model.todos.length) && (
        <>
          <Main
            editing={editing}
            todos={model.todos}
            onToggle={onToggle}
            onToggleAll={onToggleAll}
            onDestroy={onDestroy}
            onEdit={onEdit}
            onSave={onSave}
            onCancel={onCancel}
          />
          <Footer
            onClearCompleted={onClearCompleted}
            hasCompletedTodos={completedTodoCount !== 0}
          />
        </>
      )}
    </section>
  );
}

export default App;
```

  

#### 路由

修改 `src/App.tsx`，新增路由监听，todos 过滤

```TypeScript
import React, { ChangeEvent, KeyboardEvent, useEffect, useState } from 'react';
import { useLocation } from '@modern-js/runtime/router';
import { Header } from './components/Header';
import { Main } from './components/Main';
import { Footer, NowShowing } from './components/Footer';
import { TodoModel, TodoItem } from './models/todoModel';
import './styles/index.css';
import './styles/base.css';

const KeyCodes = {
  Enter: 'Enter',
  Escape: 'Escape',
};

const model = new TodoModel('TodoMVC');

// eslint-disable-next-line max-statements
function App() {
  const [newTodo, setNewTodo] = useState('');
  const [editing, setEditing] = useState('');
  const [nowShowing, setNowShowing] = useState<NowShowing>('all');
  const [, forceUpdate] = useState(0);

  const { hash } = useLocation();

  const activeTodoCount = model.todos.reduce(function (accum, todo) {
    return todo.completed ? accum : accum + 1;
  }, 0);

  const completedTodoCount = model.todos.length - activeTodoCount;

  let showingTodos = model.todos;

  if (nowShowing === 'active') {
    showingTodos = model.todos.filter(todo => !todo.completed);
  }

  if (nowShowing === 'completed') {
    showingTodos = model.todos.filter(todo => todo.completed);
  }

  useEffect(() => {
    model.subscribe(() => forceUpdate(prev => prev + 1));
  }, []);

  useEffect(() => {
    if (hash.includes('active')) {
      setNowShowing('active');
    } else if (hash.includes('completed')) {
      setNowShowing('completed');
    } else {
      setNowShowing('all');
    }
  }, [hash]);

  const handleNewTodoKeyDown = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.code !== KeyCodes.Enter) {
      return;
    }

    event.preventDefault();

    const val = newTodo.trim();

    if (!val) {
      return;
    }

    model.addTodo(val);
    setNewTodo('');
  };

  const handleNewTodoChange = (event: ChangeEvent<HTMLInputElement>) => {
    setNewTodo(event.target.value);
  };

  const onToggle = (todo: TodoItem) => {
    model.toggle(todo);
  };

  const onToggleAll = (checked: boolean) => {
    model.toggleAll(checked);
  };

  const onDestroy = (todo: TodoItem) => {
    model.destroy(todo);
  };

  const onEdit = (todo: TodoItem) => {
    setEditing(todo.id);
  };

  const onSave = (todo: TodoItem, val: string) => {
    model.save(todo, val);
    setEditing('');
  };

  const onCancel = () => {
    setEditing('');
  };

  const onClearCompleted = () => {
    model.clearCompleted();
  };

  return (
    <section className="todoapp">
      <Header
        newTodo={newTodo}
        handleNewTodoChange={handleNewTodoChange}
        handleNewTodoKeyDown={handleNewTodoKeyDown}
      />
      {Boolean(model.todos.length) && (
        <>
          <Main
            editing={editing}
            todos={showingTodos}
            onToggle={onToggle}
            onToggleAll={onToggleAll}
            onDestroy={onDestroy}
            onEdit={onEdit}
            onSave={onSave}
            onCancel={onCancel}
          />
          <Footer
            activeTodoCount={activeTodoCount}
            nowShowing={nowShowing}
            onClearCompleted={onClearCompleted}
            hasCompletedTodos={completedTodoCount !== 0}
          />
        </>
      )}
    </section>
  );
}

export default App;
```

  

修改 `src/components/Footer.tsx`

```TypeScript
import React from 'react';
import cx from 'classnames';

interface Props {
  nowShowing: NowShowing;
  hasCompletedTodos: boolean;
  onClearCompleted: () => void;
}

export type NowShowing = 'all' | 'active' | 'completed';

export function Footer(props: Props) {
  const { nowShowing, hasCompletedTodos, onClearCompleted } = props;

  return (
    <footer className="footer">
      <span className="todo-count">
        <strong>2</strong>
        <span> </span>
        <span>items</span>
        <span> left</span>
      </span>
      <ul className="filters">
        <li>
          <a href="#/" className={cx({ selected: nowShowing === 'all' })}>
            All
          </a>
        </li>
        <span> </span>
        <li>
          <a
            href="#/active"
            className={cx({ selected: nowShowing === 'active' })}>
            Active
          </a>
        </li>
        <span> </span>
        <li>
          <a
            href="#/completed"
            className={cx({ selected: nowShowing === 'completed' })}>
            Completed
          </a>
        </li>
      </ul>
      {hasCompletedTodos && (
        <button
          className="clear-completed"
          onClick={onClearCompleted}
          type="button">
          Clear completed
        </button>
      )}
    </footer>
  );
}
```

  

#### footer 复数

修改 `src/utils.ts`，新增复数判断函数

```TypeScript
import { nanoid } from 'nanoid';
import { TodoItem } from './models/todoModel';

// 生成唯一 id
export function generateId() {
  return nanoid();
}
// 从 localStorage 获取或向 localStorage 同步数据
export function store(namespace: string, data?: Array<TodoItem>) {
  if (data) {
    return localStorage.setItem(namespace, JSON.stringify(data));
  }

  const localStore = localStorage.getItem(namespace);
  return (localStore && JSON.parse(localStore)) || [];
}

export function pluralize(count: number, word: string) {
  return count === 1 ? word : `${word}s`;
}
```

修改 `src/App.tsx`，为 Footer 组件传入 activeTodoCount

```TypeScript
import React, { ChangeEvent, KeyboardEvent, useEffect, useState } from 'react';
import { useLocation } from '@modern-js/runtime/router';
import { Header } from './components/Header';
import { Main } from './components/Main';
import { Footer, NowShowing } from './components/Footer';
import { TodoModel, TodoItem } from './models/todoModel';
import './styles/index.css';
import './styles/base.css';

const KeyCodes = {
  Enter: 'Enter',
  Escape: 'Escape',
};

const model = new TodoModel('TodoMVC');

function App() {
  const [newTodo, setNewTodo] = useState('');
  const [editing, setEditing] = useState('');
  const [nowShowing, setNowShowing] = useState<NowShowing>('all');
  const [, forceUpdate] = useState(0);

  const { hash } = useLocation();

  const activeTodoCount = model.todos.reduce(function (accum, todo) {
    return todo.completed ? accum : accum + 1;
  }, 0);

  const completedTodoCount = model.todos.length - activeTodoCount;

  useEffect(() => {
    model.subscribe(() => forceUpdate(prev => prev + 1));
  }, []);

  useEffect(() => {
    if (hash.includes('active')) {
      setNowShowing('active');
    } else if (hash.includes('completed')) {
      setNowShowing('completed');
    } else {
      setNowShowing('all');
    }
  }, [hash]);

  const handleNewTodoKeyDown = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.code !== KeyCodes.Enter) {
      return;
    }

    event.preventDefault();

    const val = newTodo.trim();

    if (!val) {
      return;
    }

    model.addTodo(val);
    setNewTodo('');
  };

  const handleNewTodoChange = (event: ChangeEvent<HTMLInputElement>) => {
    setNewTodo(event.target.value);
  };

  const onToggle = (todo: TodoItem) => {
    model.toggle(todo);
  };

  const onToggleAll = (checked: boolean) => {
    model.toggleAll(checked);
  };

  const onDestroy = (todo: TodoItem) => {
    model.destroy(todo);
  };

  const onEdit = (todo: TodoItem) => {
    setEditing(todo.id);
  };

  const onSave = (todo: TodoItem, val: string) => {
    model.save(todo, val);
    setEditing('');
  };

  const onCancel = () => {
    setEditing('');
  };

  const onClearCompleted = () => {
    model.clearCompleted();
  };

  return (
    <section className="todoapp">
      <Header
        newTodo={newTodo}
        handleNewTodoChange={handleNewTodoChange}
        handleNewTodoKeyDown={handleNewTodoKeyDown}
      />
      {Boolean(model.todos.length) && (
        <>
          <Main
            editing={editing}
            todos={model.todos}
            onToggle={onToggle}
            onToggleAll={onToggleAll}
            onDestroy={onDestroy}
            onEdit={onEdit}
            onSave={onSave}
            onCancel={onCancel}
          />
          <Footer
            activeTodoCount={activeTodoCount}
            nowShowing={nowShowing}
            onClearCompleted={onClearCompleted}
            hasCompletedTodos={completedTodoCount !== 0}
          />
        </>
      )}
    </section>
  );
}

export default App;
```

修改 `src/components/Footer.tsx`，根据未完成 todos 展示数量及单位

```TypeScript
import React from 'react';
import cx from 'classnames';
import { pluralize } from '@/utils';

interface Props {
  activeTodoCount: number;
  nowShowing: NowShowing;
  hasCompletedTodos: boolean;
  onClearCompleted: () => void;
}

export type NowShowing = 'all' | 'active' | 'completed';

export function Footer(props: Props) {
  const { activeTodoCount, nowShowing, hasCompletedTodos, onClearCompleted } =
    props;

  return (
    <footer className="footer">
      <span className="todo-count">
        <strong>{activeTodoCount}</strong>
        <span> </span>
        <span>{pluralize(activeTodoCount, 'item')}</span>
        <span> left</span>
      </span>
      <ul className="filters">
        <li>
          <a href="#/" className={cx({ selected: nowShowing === 'all' })}>
            All
          </a>
        </li>
        <span> </span>
        <li>
          <a
            href="#/active"
            className={cx({ selected: nowShowing === 'active' })}>
            Active
          </a>
        </li>
        <span> </span>
        <li>
          <a
            href="#/completed"
            className={cx({ selected: nowShowing === 'completed' })}>
            Completed
          </a>
        </li>
      </ul>
      {hasCompletedTodos && (
        <button
          className="clear-completed"
          onClick={onClearCompleted}
          type="button">
          Clear completed
        </button>
      )}
    </footer>
  );
}
```

### 上传 GitHub

```TypeScript
git commit -am "feat: Modern.js Todo MVC"
git push
```