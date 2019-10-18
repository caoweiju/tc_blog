---
title: 文章翻译：如何使用react的Hook来请求数据
date: 2019-10-18 12:30:40
tags:
- react
- Hook
categories: 
- 翻译
- JavaScript
---

## 如何在react的Hook中请求数据
[原文：How to fetch data with React Hooks?](https://www.robinwieruch.de/react-hooks-fetch-data)

In this tutorial, I want to show you how to fetch data in React with Hooks by using the state and effect hooks. We will use the widely known Hacker News API to fetch popular articles from the tech world. You will also implement your custom hook for the data fetching that can be reused anywhere in your application or published on npm as standalone node package.

本文将会讲述如何在React中通过使用useState和uesEffect来fetch异步获取数据，例子中将使用广为人知的Hacker News API来获取技术领域上的一些受欢迎的文章，同时还会尝试使用自定义Hook来获取数据，以便在开发者的工程或者npm包中来提高复用性。

If you don't know anything about this new React feature, checkout this introduction to React Hooks. If you want to checkout the finished project for the showcased examples that show how to fetch data in React with Hooks, checkout this GitHub repository.

如果你还不太清楚react的Hook新特性，那么建议先去[官方文档](https://reactjs.org/docs/hooks-intro.html)或者[关于hook的介绍](https://www.robinwieruch.de/react-hooks/)查看相关内容，本文的全部完整示例代码都在[这个github仓库中](https://github.com/the-road-to-learn-react/react-hooks-introduction)

If you just want to have a ready to go React Hook for data fetching: npm install use-data-api and follow the documentation. Don't forget to star it if you use it :-)

如果你只是想通过使用Hook来获取数据，可以通过`npm install use-data-api`，并配合文档[use-data-api文档](https://github.com/the-road-to-learn-react/use-data-api)来使用，如果有帮助的话请➕星；

Note: In the future, React Hooks are not be intended for data fetching in React. Instead, a feature called Suspense will be in charge for it. The following walkthrough is nonetheless a great way to learn more about state and effect hooks in React.

>注意：在将来，React Hooks并不计划一直成为异步获取数据的方式，而是使用以后的新特性 Suspense 来获取数据，不过通过本文的内容依然是一个学习好state和effect相关Hook的不错方法。

<!-- more -->

### DATA FETCHING WITH REACT HOOKS
If you are not familiar with data fetching in React, checkout my extensive data fetching in React article. It walks you through data fetching with React class components, how it can be made reusable with Render Prop Components and Higher-Order Components, and how it deals with error handling and loading spinners. In this article, I want to show you all of it with React Hooks in function components.

如果你对于react中获取数据不太熟悉，可以先查看相关内容进行了解，[关于react获取数据文章](https://www.robinwieruch.de/react-fetching-data/)，文中会讲述怎么在class组件获取数据，并通过Render props和高阶组件的方式来提高复用，同时还有关于错误处理和加载状态的部分，而本文将会说明在react中如何使用Hook来达到上述一样的功能；

````jsx
import React, { useState } from 'react';
function App() {
  const [data, setData] = useState({ hits: [] });
  return (
    <ul>
      {data.hits.map(item => (
        <li key={item.objectID}>
          <a href={item.url}>{item.title}</a>
        </li>
      ))}
    </ul>
  );
}
export default App;
````
The App component shows a list of items (hits = Hacker News articles). The state and state update function come from the state hook called useState that is responsible to manage the local state for the data that we are going to fetch for the App component. The initial state is an empty list of hits in an object that represents the data. No one is setting any state for this data yet.

这个App组件展示了一个列表，其中hits是指上面提到的Hacker News articles文章列表，使用useState来返回state和修改state的方法函数，我们获取的数据就会通过这个方法来设置到组件的state中，初始state就是一个空数组，并且没有调用设置state的方法。

We are going to use axios to fetch data, but it is up to you to use another data fetching library or the native fetch API of the browser. If you haven't installed axios yet, you can do so by on the command line with npm install axios. Then implement your effect hook for the data fetching:

本文将使用 axios 来获取数据，但是这可以根据你自己的习惯来选择其他的库或者直接使用fetch原生API，如果还没有安装 axios，那么可以使用`npm install axios`来安装，然后使用 useEffect 来获取数据；

````jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';
function App() {
  const [data, setData] = useState({ hits: [] });
  useEffect(async () => {
    const result = await axios(
      'https://hn.algolia.com/api/v1/search?query=redux',
    );
    setData(result.data);
  });
  return (
    <ul>
      {data.hits.map(item => (
        <li key={item.objectID}>
          <a href={item.url}>{item.title}</a>
        </li>
      ))}
    </ul>
  );
}
export default App;
````
The effect hook called useEffect is used to fetch the data with axios from the API and to set the data in the local state of the component with the state hook's update function. The promise resolving happens with async/await.

通过使用 useEffect 来调用 axios 来获取数据，获取数据后通过useState返回的setState方法来修改state，state的变更会驱动DOM的更新渲染；其中使用了 async/await 来请求数据；

However, when you run your application, you should stumble into a nasty loop. The effect hook runs when the component mounts but also when the component updates. Because we are setting the state after every data fetch, the component updates and the effect runs again. It fetches the data again and again. That's a bug and needs to be avoided. We only want to fetch data when the component mounts. That's why you can provide an empty array as second argument to the effect hook to avoid activating it on component updates but only for the mounting of the component.

但是，以上代码运行时会进入一个循环请求数据的状态，useEffect会因为DOM的更新而不断的执行，useEffect中求数据后设置state引起组件更新，组件更新渲染后会执行useEffect从而重新请求数据，再次重新设置state，这将会导致死循环；我们需要避免这种情况，我们需要只在第一次挂载组件的时候请求就好了，useEffect方法通过第二个参数【deps 依赖】传入一个空数组来控制，只在组件的挂载过程去执行，其他时间不执行。

````jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';
function App() {
  const [data, setData] = useState({ hits: [] });
  useEffect(async () => {
    const result = await axios(
      'https://hn.algolia.com/api/v1/search?query=redux',
    );
    setData(result.data);
  }, []);
  return (
    <ul>
      {data.hits.map(item => (
        <li key={item.objectID}>
          <a href={item.url}>{item.title}</a>
        </li>
      ))}
    </ul>
  );
}
export default App;
````
The second argument can be used to define all the variables (allocated in this array) on which the hook depends. If one of the variables changes, the hook runs again. If the array with the variables is empty, the hook doesn't run when updating the component at all, because it doesn't have to watch any variables.

useEffect的第二个参数是一个包括了在当前 useEffect 中所使用的的变量的数组，如果其中某个变量变更【Object.is判断】了，那么在最近一次渲染后，这个useEffect会被重新执行；如果传入的是一个空数组，那么除了第一次挂载组件时会执行，其他时间更新组件的时候不会执行，因为不依赖任何变量，也就是依赖不会变更，经常被用于模拟 componentDidMount，但是还是存在区别的。

There is one last catch. In the code, we are using async/await to fetch data from a third-party API. According to the documentation every function annotated with async returns an implicit promise: "The async function declaration defines an asynchronous function, which returns an AsyncFunction object. An asynchronous function is a function which operates asynchronously via the event loop, using an implicit Promise to return its result. ". However, an effect hook should return nothing or a clean up function. That's why you may see the following warning in your developer console log: 07:41:22.910 index.js:1452 Warning: useEffect function must return a cleanup function or nothing. Promises and useEffect(async () => ...) are not supported, but you can call an async function inside an effect.. That's why using async directly in the useEffect function isn't allowed. Let's implement a workaround for it, by using the async function inside the effect.

使用空数组后还存在一个问题，我们使用了 async/await 方式来请求接口，async/await的规范有：async 函数通过事件循环来执行异步操作，返回一个promise对象。这和react规定 useEffect 需要返回一个清除函数或者无返回值这一要求不符，所以运行上面的代码将会在控制台看到一段日志提示：07:41:22.910 index.js:1452 Warning: useEffect function must return a cleanup function or nothing。返回promise或者使用useEffect(async () => ...)形式是不行的，但是可以在useEffect 内部来调用，可以查看一下的代码：
````jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';
function App() {
  const [data, setData] = useState({ hits: [] });
  useEffect(() => {
    const fetchData = async () => {
      const result = await axios(
        'https://hn.algolia.com/api/v1/search?query=redux',
      );
      setData(result.data);
    };
    fetchData();
  }, []);
  return (
    <ul>
      {data.hits.map(item => (
        <li key={item.objectID}>
          <a href={item.url}>{item.title}</a>
        </li>
      ))}
    </ul>
  );
}
export default App;
````
That's data fetching with React hooks in a nutshell. But continue reading if you are interested about error handling, loading indicators, how to trigger the data fetching from a form, and how to implement a reusable data fetching hook.

### 交互触发数据请求
Great, we are fetching data once the component mounts. But what about using an input field to tell the API in which topic we are interested in? "Redux" is taken as default query. But what about topics about "React"? Let's implement an input element to enable someone to fetch other stories than "Redux" stories. Therefore, introduce a new state for the input element.

我们上面已经完成了在一个组件挂载是发起请求，不过有的时候我们需要经过和用户交互获取数据来发起请求获取数据，我们来实践一下通过input表单来确定我们需要何时请求何种数据。
````jsx
import React, { Fragment, useState, useEffect } from 'react';
import axios from 'axios';
function App() {
  const [data, setData] = useState({ hits: [] });
  const [query, setQuery] = useState('redux');
  useEffect(() => {
    const fetchData = async () => {
      const result = await axios(
        'https://hn.algolia.com/api/v1/search?query=redux',
      );
      setData(result.data);
    };
    fetchData();
  }, []);
  return (
    <Fragment>
      <input
        type="text"
        value={query}
        onChange={event => setQuery(event.target.value)}
      />
      <ul>
        {data.hits.map(item => (
          <li key={item.objectID}>
            <a href={item.url}>{item.title}</a>
          </li>
        ))}
      </ul>
    </Fragment>
  );
}
export default App;
````
At the moment, both states are independent from each other, but now you want to couple them to only fetch articles that are specified by the query in the input field. With the following change, the component should fetch all articles by query term once it mounted.

我们有两个独立的state了，此时希望能够根据input表单的内容来请求数据，通过下面的修改来将query关联到请求中。
````jsx
useEffect(() => {
    const fetchData = async () => {
      const result = await axios(
        `http://hn.algolia.com/api/v1/search?query=${query}`,
      );
      setData(result.data);
    };
    fetchData();
  }, []);
````
One piece is missing: When you try to type something into the input field, there is no other data fetching after the mounting triggered from the effect. That's because you have provided the empty array as second argument to the effect. The effect depends on no variables, so it is only triggered when the component mounts. However, now the effect should depend on the query. Once the query changes, the data request should fire again.

还有一个问题需要调整，因为我们手动修改input的内容时，并不会重新发起请求，这是因为上面提到的useEffect的的依赖是一个空数组，这样在变量变更时，组件更新渲染后并不会再次调用useEffect，所以我们需要把我们需要的依赖 query 添加到useEffect的第二个参数依赖中，如下：
````jsx
useEffect(() => {
    const fetchData = async () => {
      const result = await axios(
        `http://hn.algolia.com/api/v1/search?query=${query}`,
      );
      setData(result.data);
    };
    fetchData();
  }, [query]);
````
The refetching of the data should work once you change the value in the input field. But that opens up another problem: On every character you type into the input field, the effect is triggered and executes another data fetching request. How about providing a button that triggers the request and therefore the hook manually?

以上是的在修改input的内容时会重新请求所需数据，但是每次的修改都会重新请求数据，最好能够通过按钮来控制何时进行数据请求；通过修改依赖，依赖于一个经过button被点击后才会修改的state，这样就可以实现想要的结果。

````jsx
function App() {
  const [data, setData] = useState({ hits: [] });
  const [query, setQuery] = useState('redux');
  const [search, setSearch] = useState('');
  useEffect(() => {
    const fetchData = async () => {
      const result = await axios(
        `http://hn.algolia.com/api/v1/search?query=${query}`,
      );
      setData(result.data);
    };
    fetchData();
  }, [search]);
  return (
    <Fragment>
      <input
        type="text"
        value={query}
        onChange={event => setQuery(event.target.value)}
      />
      <button type="button" onClick={() => setSearch(query)}>
        Search
      </button>
      <ul>
        {data.hits.map(item => (
          <li key={item.objectID}>
            <a href={item.url}>{item.title}</a>
          </li>
        ))}
      </ul>
    </Fragment>
  );
}
````

### 异步组件卸载后依然可以setState的问题修改
在react中，一个组件中如果在异步的获取数据过程中，该组件已经被卸载，那么在异步数据获取到后setState依然会被执行，我们可以在hook中通过useEffect的返回清除函数来避免这种情况；
````jsx
const useDataApi = (initialUrl, initialData) => {
  const [url, setUrl] = useState(initialUrl);
  const [state, dispatch] = useReducer(dataFetchReducer, {
    isLoading: false,
    isError: false,
    data: initialData,
  });
  useEffect(() => {
    let didCancel = false;
    const fetchData = async () => {
      dispatch({ type: 'FETCH_INIT' });
      try {
        const result = await axios(url);
        if (!didCancel) {
          dispatch({ type: 'FETCH_SUCCESS', payload: result.data });
        }
      } catch (error) {
        if (!didCancel) {
          dispatch({ type: 'FETCH_FAILURE' });
        }
      }
    };
    fetchData();
    return () => {
      didCancel = true;
    };
  }, [url]);
  return [state, setUrl];
};
````
如果组件卸载的话，会执行didCancel来置为true，这样异步请求 axios 返回的时候，设置state的时候会判断 didCancel 来确定当前的组件是否已经卸载，这也是js中一种巧妙的闭包的使用。


