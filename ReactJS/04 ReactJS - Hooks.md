# Hooks
Hooks 本质上就是一类特殊的函数，它们可以为你的函数型组件（function component）注入一些特殊的功能。

React 提供了一些内置的 Hook，比如 useState。你也可以创建一些自己的 custom Hook 来在组件间复用某些状态型的逻辑和行为。

### useState
``` js
// class 写法
import React,{ Component } from 'react';
class Example extends Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}

// 使用 Hooks
import { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

useState 的唯一参数就是 state 的初始值，这里的 state 的初始值并不要求一定是一个javascript字面量对象。。

在一个function component中使用多次state Hook：

``` js
function ExampleWithManyStates() {
  const [age, setAge] = useState(42);
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  const [fruit, setFruit] = useState('banana');
  // ...
}
```

### useEffect
useEffect 可以看做 componentDidMount、componentDidUpdate、和 componentWillUnmount 的大集合，它是一个完整的处理副作用的机制。

``` js
// class 写法
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}

// 用 useEffect
import { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

### useContext
``` js
function Example() {
  const locale = useContext(LocaleContext);
  const theme = useContext(ThemeContext);
  // ...
}

function Todos() {
  const [todos, dispatch] = useReducer(todosReducer);
  // ...
```

#