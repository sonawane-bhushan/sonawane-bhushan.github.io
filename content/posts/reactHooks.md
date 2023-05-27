---
title: "React Hooks"
date: 2023-05-25T11:30:03+00:00
weight: 1
aliases: ["/react-hooks"]
tags: ["react", "hooks", "javascript", "nodejs", "beginners"]
author: "Bhushan Sonawane"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "What, Why and How About React Hooks"
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

React is the most popular javascript library used for web and mobile app development. React follows `component` based architecture. A component is the smallest reusable portion of the code. There are two types of components: react class component and functional component. Class components are defined as JavaScript classes that extend the base `React.Component` class. They are also known as "stateful components" because they can manage and update their own internal state. 

Functional components are more simple and lightweight. They are javascript functions which take props as input and return jsx form of components ui. Functional components were traditionally stateless. But as a part of React 16.8 hooks were introduced which allowed state handling with functional components as well and also added lifecycle behavior similar to class components. 

Functional components are now very popular in use due to their popularity, simplicity and reusability. React also encourages to use hooks wherever possible. 

So far we know hooks are the feature released as part of react 16.8 that allowed functional components to handle states and have lifecycle behaviors similar to class components. There are different types of hooks provided by react and we can create our own custom hooks as well. 

Hooks let you use different React features from your components. Hooks provide a more concise and intuitive way to write reusable and stateful logic within functional components.

## The general rules of React Hooks

1. Hooks should be always at top level, Hooks should not be called inside loops, conditions or nested functions
2. Hooks should be used in functional components only
3. Hooks should not be called from javascript functions

Some of the hooks by react are as follows: 

State Hooks - useState, useReducer
Context Hooks - useContext
Ref Hooks - useRef, useImperativeHandle
Effect Hooks - useEffect, useLayoutEffect, useInsertionEffect
Performance Hooks - useMemo, useCallback

Let's take a look at some of the mostly used hooks

## useState

useState is a React Hook that lets you add a state variable to your component. It returns an array with two elements: the current state value and a function to update that state. 

`const [state, setState] = useState(initialState);`

It is convention to name state variables as [something, setSomething] using array destructuring. initialState is the value you want to set at the start, it can be of any type. If you pass a function as initialState, it will be treated as an initializer function. It should be pure, should take no arguments, and should return a value of any type. React will call your initializer function when initializing the component, and store its return value as the initial state.

state will have a current state and setState is a function we can use to set the state to different value and trigger re-render. 

```javascript:
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment The Count !</button>
    </div>
  );
}
```

Here in the above example we are using count as a state with the initial value set as 0. We are using the count to display as part of return and onClick of the button we are calling setCount with incrementing count by 1. So, whenever the button is clicked it will rerender with the new value of the count and so on.

## useEffect 

useEffect is a React Hook that lets you synchronize a component with an external system. Used to manage side-effects like API calls, subscriptions, timers, mutations, and more. It runs after every render and accepts a function that can contain the side effect code. You can also specify dependencies to control when the effect should run.

`useEffect(setup, dependencies?)`

The useEffect hook accepts two arguments: a function and an optional array of dependencies. 

setup - this is the code which will run as part of every render. This can include the initial render and subsequent re-renders caused by changes in the component's state or props.

dependencies (optional) -  The optional array of dependencies determines when the effect should run. If the dependencies array is empty, the effect runs only once, after the initial render. It will be executed after every render of the component. If the dependencies array contains values, the effect will run whenever any of those values change.

useEffect can be used to fetch data, to subscribe to events, to cleanup and dependency handling, etc. 

```javascript:
import React, { useEffect, useState } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetchData()
      .then((result) => setData(result))
      .catch((error) => console.error(error));
  }, []);

  return <p>{data}</p>;
}
```

Here useEffect is used to fetchData and save its value to state. Since dependencies are empty the useEffect will run only once after initial render and then on subsequent renders of component. 

## useContext 

useContext is a React Hook that lets you read and subscribe to context from your component. It takes a context object created with React.createContext and returns the current context value. 

`const value = useContext(SomeContext)`

SomeContext - The context that you’ve previously created with createContext. The context itself does not hold the information, it only represents the kind of information you can provide or read from components.

```javascript:
import React, { useContext } from 'react';

const ThemeContext = React.createContext('light');

function ThemedText() {
  const theme = useContext(ThemeContext);

  return <p>Current theme: {theme}</p>;
}
```

## Creating custom hooks 

You can create your own custom hooks to encapsulate reusable stateful logic and share it across multiple components. Custom hooks follow a naming convention of starting with the word "use" and can use other built-in hooks. Custom hooks are best to create reusable blocks of code eg. to fetch the data from the url which can be reused in multiple components. 

```javascript:
import { useEffect, useState } from 'react';

const useFetch = (url) => {
    const [data, setData] = useState(null);
    const [isPending, setIsPending] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {

        const abortCont = new AbortController();

        setTimeout(() => {
            fetch(url, { signal: abortCont.signal })
                .then(res => {
                    if (!res.ok) {
                        throw Error('Could not fetch the data for that resource');
                    }
                    return res.json();
                })
                .then(data => {
                    setData(data);
                    setIsPending(false);
                    setError(null);
                })
                .catch(err => {
                    if (err.name === 'AbortError') {
                        console.log('fetch aborted');
                    }
                    else {
                        setIsPending(false);
                        setError(err.message);
                    }
                })
        }, 1000);

        return () => abortCont.abort();
    }, [url]);

    return { data, isPending, error }
}

export default useFetch;
```

Here in the above example a custom hook is created with the name useFetch. This hook fetches the data from the url and has error handling as well as status check whether it is done or pending. It internally makes use of useState and useEffect hooks. This hook can be used throughout wherever we need to fetch data from some url. 


***

This was all about hooks in react, why they were added, what they do and how to use them. Hope it was helpful to you folks. Thank you so much for reading. 

Happy Coding !!


## References
 
1. [React docs]( https://react.dev/reference/react)
2. [W3 schools](https://www.w3schools.com/react/react_hooks.asp)
