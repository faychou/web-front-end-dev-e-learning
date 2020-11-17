# Hooks
Hooks 本质上就是一类特殊的函数，它们可以为你的函数型组件（function component）注入一些特殊的功能。

React 提供了一些内置的 Hook，比如 useState。你也可以创建一些自己的 custom Hook 来在组件间复用某些状态型的逻辑和行为。

目前 React 内置了 10 种 Hook：

useState
useReducer
useContext
useRef
useEffect
useLayoutEffect
useCallback
useMemo
useImperativeHandle
useDebugValue

## 钩子函数
### useState
状态钩子 useState 用于定义组件的 State。

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

// Hooks 写法
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

useState 的唯一参数就是 state 的初始值，这里的 state 的初始值并不要求一定是一个 javascript 字面量对象。

在一个 function component 中使用多次 state Hook：

``` js
function ExampleWithManyStates() {
  const [age, setAge] = useState(42);
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  const [fruit, setFruit] = useState('banana');
  // ...
}
```

### useEffect
函数组件能保存状态，但是对于异步请求，副作用的操作还是无能为力，所以 React 提供了 useEffect 来帮助开发者处理函数组件的副作用。useEffect 可以看做 componentDidMount、componentDidUpdate、和 componentWillUnmount 的大集合，它是一个完整的处理副作用的机制。

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

// useEffect 写法
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

useEffect(callback, [source])接受两个参数：

* callback: 钩子回调函数，可以用来做一些副作用比如异步请求，修改外部参数等行为；
* source: 设置触发条件，是一个数组，如果数组中的值变化才会触发 执行 useEffect 第一个参数中的函数；
* 没有传入 [source] 参数时，默认在每次 render 时都会优先调用上次保存的回调中返回的函数，后再重新调用回调；
* 如果第二个参数传递一个空数组 []，这种情况下只有在组件初始化或销毁的时候才会触发，用来代替 componentDidMount 和 componentWillUnmount 慎用；
* 返回值(如果有)则在组件销毁或者调用函数前调用。

``` js
const [count, setCount] = useState(0);

useEffect(() => {
    const id = setInterval(() => {
        setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
}, [count]);
```



### useContext
useContext 获取 context 对象。

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

### useReducer
类似于 Redux 思想的实现，但其并不足以替代 Redux，可以理解成一个组件内部的 redux。

* 并不是持久化存储，会随着组件被销毁而销毁；
* 属于组件内部，各个组件是相互隔离的，单纯用它并无法共享数据；
* 配合useContext的全局性，可以完成一个轻量级的 Redux。

### useCallback
缓存回调函数，避免传入的回调每次都是新的函数实例而导致依赖组件重新渲染，具有性能优化的效果。

### useMemo
用于缓存传入的 props，避免依赖的组件每次都重新渲染。

``` js
const data = useMemo(() => ({
    a,
    b,
    c,
    d: 'xxx'
}), [a, b, c]);

// 可以用 useCallback 代替
const fn = useMemo(() => () => {
    // do something
}, [a, b]);

const memoComponentsA = useMemo(() => (
    <ComponentsA {...someProps} />
), [someProps]);
```

``` js
function Example(props) {
    const [count, setCount] = useState(0);
    const [foo] = useState("foo");

    const main = useMemo(() => (
        <div>
            <Item key={1} x={1} foo={foo} />
            <Item key={2} x={2} foo={foo} />
            <Item key={3} x={3} foo={foo} />
            <Item key={4} x={4} foo={foo} />
            <Item key={5} x={5} foo={foo} />
        </div>
    ), [foo]);

    return (
        <div>
            <p>{count}</p>
            <button onClick={() => setCount(count + 1)}>setCount</button>
            {main}
        </div>
    );
}
```

### useRef

获取组件的真实节点。

### useLayoutEffect:
* DOM 更新同步钩子。用法与 useEffect 类似，只是区别于执行时间点的不同。
* useEffect 属于异步执行，并不会等待 DOM 真正渲染后执行，而 useLayoutEffect 则会真正渲染后才触发；
* 可以获取更新后的 state。

### 自定义钩子(useXxxxx)
基于 Hooks 可以引用其它 Hooks 这个特性，我们可以编写自定义钩子，如上面的useMounted。又例如，我们需要每个页面自定义标题。

``` js
function useTitle(title) {
  useEffect(
    () => {
      document.title = title;
    });
}

// 使用:
function Home() {
	const title = '我是首页'
	useTitle(title)
	
	return (
		<div>{title}</div>
	)
}
```


>> 注意：

>> * 避免在循环/条件判断/嵌套函数 中调用 hooks，保证调用顺序的稳定；
>> * 只有函数定义组件和 hooks 可以调用 hooks，避免在类组件或者普通函数中调用；
>> * 不能在useEffect中使用useState，React 会报错提示；
>> * 类组件不会被替换或废弃，不需要强制改造类组件，两种方式能并存。