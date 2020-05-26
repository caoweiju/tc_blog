---
title: react hooks 的简析
date: 2020-05-26 17:23:52
tags:
- react
categories: 
- javascript
---

## 为什么需要react hooks

从2013开始对外发布react，作为开发者，对于react的开发习惯经历了一些变化，如下：

1. 最开始的React 提供 React.createClass API 创建 React 组件，【此时class还没有正式进入ecma规范】；
    - 此时开发者需要按照规范去使用createClass API，
2. React 在 V0.13.0 版本中引入 React.Component API，允许开发者使用原生 JavaScript class 方式创建组件；
    - 此时需要开发者了解 constructor 和 super调用，已经 this 的绑定；
    - 后面有了试验中 class filed 特性来帮助我们跳过 constructor；
    - 高阶组件、render props、生命周期分散处理等引入；
3. react hook带来的灵活，给函数组件注入了新的生命力；

[更多参考](https://react.caoweiju.com/src/hooks/readme.html)

<!-- more -->

## Hook的定义
Hook 是什么呢？下面是官方给出的定义：

> Hooks are functions that let you “hook into” React state and lifecycle features from function components

Hook 本质上是函数，它能够让函数组件也拥有状态和生命周期的特性。这意味着我们原本只能使用 class 组件实现的多数功能，现在用函数组件也能实现。不过 Hook 的使用范围也有一定限制，限定于函数组件和自定义 hook 中。

Hooks 名称统一约定以 use 前缀开头（比如 usexxx），因为 React 还无法自动识别哪些是普通 JavaScript 函数，哪些是 Hooks 函数。下面我们将介绍函数组价和 React Hooks 是如何解决 class 组件的痛点的。

### useState简介
useState 主要作用就是给函数组件赋予状态（State）。useState 的入参为状态的初始值，会返回两个出参：一个为读取状态的变量，另外一个为写入状态的函数，当然出参的命名是没有任何限制的。从下面示例可以看出，使用函数组件再也不用关心 super(props) 和 this 指针问题。
````
import React, { useState } from 'react';

function Counter () {
  const [count, setCount] = useState(0);
  function handleClick() {
    setCount(count + 1);
  }
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={handleClick}>
        Click me
      </button>
    </div>
  );
}
````
1. useState 第一个参数可以是初始值或者是一个返回初始值得初始化函数；
2. setState 的参数可以是一个新的state值，也可以是返回一个新的state的函数【这个函数的参数就是修改之前的state，这块看起来和class组件中state是相似的】；
3. setState 这里的setState可以命名为其他的，而且是执行setState将会直接替换原有的state，而不是合并；
4. setState 可能是异步的过程，所以如果更新state依赖之前的state，请使用函数作为setState的参数来更新state；
5. useState 是在函数组件或者自定义Hook中使用的，所以无论在mount还是update的时候都会被执行，但是update过程的执行不会重新生成state，而是返回最新的state和setState方法；

**注意：state中保留的最好是会影响渲染的数据，如果只是想要一个类似实例数据持久化，那么请使用useRef**

### useEffect简介
Effect Hook 可以让你在函数组件中执行副作用操作，而数据获取，设置订阅以及手动更改 React 组件中的 DOM 都属于副作用

> 如果你熟悉 React class 的生命周期函数，你可以把 useEffect Hook 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。

````
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
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
````
useEffect(didUpdate, deps) 该 Hook 接收一个包含命令式、且可能有副作用代码的函数。默认情况下，effect 将在每轮渲染结束后执行，但你可以通过第二个参数deps来控制让它 在只有某些值改变的时候 才执行。

注意点：

1. 清除Effect：组件卸载时需要清除 effect 创建的诸如订阅或计时器 ID 等资源。要实现这一点，useEffect 函数需返回一个清除函数。但是实际情况中，每次useEffect执行都会清除上一次的effect，而创造新的effect，也就是如果组件多次渲染（通常如此），则在执行下一个 effect 之前，上一个 effect 就已被清除。所以为了减少不必要的操作，我们应该考虑减少useEffect的执行，也就是通过控制useEffect的第二个参数deps；

2. 传给 useEffect 的函数会延迟调用，而且总是在浏览器屏幕完成渲染后延迟调用，而不是像class组件的didimount、didiupdate周期一样会合并然后渲染到屏幕中避免中间状态渲染出来【useLayoutEffect Hook 可以达到这种效果】；因此不应在 useEffect 的第一个参数对应的函数中执行阻塞浏览器更新屏幕的操作；

3. 依赖可以使得useEffect仅仅在某些条件下才会被执行，不过需要确保数组中包含了所有外部作用域中会发生变化且在 effect 中使用的变量，否则你的代码会引用到先前渲染中的旧变量，旧变量会保存就得值，而不是最新的值导致出现bug。而且依赖项数组不会作为参数传给 effect 函数。虽然从概念上来说它表现为：所有 effect 函数中引用的值都应该出现在依赖项数组中。未来编译器会更加智能，届时自动创建数组将成为可能。

### hooks 规则
React Hooks 虽然是函数，但不同普通函数，因此 React Hooks 有两条规则约束其使用：

1. 不能在循环语句、条件语句，或者嵌套函数内调用 Hooks。而应该 React 函数顶部调用 Hooks。

2. 不要在普通的 JavaScript 函数中调用 Hooks，而应该
    - 在 React 函数组件中调用 Hooks
    - 在自定义 Hooks 中调用 Hooks

不过你不用担心上面两条规则，官方提供了 eslint-plugin-react-hooks 插件能够帮你进行代码 eslint 静态检查。

## HOOKS 原理
主要是考虑闭包：

useState的简单模拟：
````js
import React from 'react';
import ReactDOM from 'react-dom';

let hooks = []; // 存放 hooks 的状态
let cursor = 0; // 当前 hooks 下标

function useState(initialValue) {
  hooks[cursor] = hooks[cursor] || initialValue;
  const currentCursor = cursor; // “记忆” Hook 的下标，用于后续赋值
  function setState(newValue) {
    hooks[currentCursor] = newValue;
    reRenderComponent();
  }
  return [hooks[cursor++], setState]; // 下标自增
}

// 重新渲染
function reRenderComponent() {
    ReactDOM.render(<App />, document.getElementById('root'));
}

function App() {
  var [count, setCount] = useState(0);
  return (
    <div>
      <div>{count}</div>
      <button onClick={() => { setCount(count + 1); }}>
        点击
      </button>
    </div>
  );
}
````

useEffect的简单实现：
````js
let hooks = [];
let cursor = 0;

function useEffect(callback, depArray) {
  const hasNoDeps = !depArray; // 没有设置依赖
  const deps = hooks[cursor];
  const hasChangedDeps = deps
    ? !depArray.every((el, i) => el === deps[i])
    : true; // 检查依赖是否有发生变化
  if (hasNoDeps || hasChangedDeps) {
    callback(); // 执行副作用
    hooks[cursor] = depArray;
  }
  cursor++;
}
````


