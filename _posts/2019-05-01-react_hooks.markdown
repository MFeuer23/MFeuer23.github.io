---
layout: post
title:      "React Hooks"
date:       2019-05-01 14:49:12 -0400
permalink:  react_hooks
---


React Hooks are a new feature that just came out in React 16.8 a couple of months ago. React Hooks allow us to use state and lifecycle methods in functional components. Before Hooks, in order to use these methods, you'd have to express your react component with an ES6 class (or `React.Component`). So while Hooks let us use React's features without classes, classes aren't being removed or discouraged--we are simply being given more options.

## **React's State Hook**

Here's an example of how you can now refactor a class component into a functional component while still being able to access and change state.

```
import React, { Component } from 'react';

class Counter extends Component {
  state = {
    count: 0
  };

  setCount = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return (
      <div>
        <h1>{this.state.count}</h1>

        <button onClick={this.setCount}>Count Up</button>
      </div>
    );
  }
}
```

With React Hooks, we can condense this into a functional component without losing any functionality. We can accomplish this with `useState()`

```
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => setCount(count + 1)}>Count Up</button>
    </div>
  );
}
```

### **useState()**

`useState()` uses [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) for arrays, similiarly to how we would pull props out of an object with object destructuring.

**Array Destructuring**
The first variable is the first item in the array.
```
// array destructuring
const users = ['edwin', 'clair'];

// grab in order and rename at the same time
const [cellist, violinist] = users;

console.log(cellist)
// expected output: "edwin"

console.log(violinist)
// expected output: "clair"
```

**Object Destructuring**
```
// object destructuring
const users = { admin: 'edwin', principal: 'clair' };

// grab the admin and principal but rename them to cellist and violinist
const { admin: cellist, principal: violinist } = users;

console.log(cellist)
// expected output: "edwin"

console.log(violinist)
// expected output: "clair"
```

So **`useState()`** gives us two variables that we can name whatever we want:
1. The first is **value**. Analogous to `this.state`
2. the second is a **function to update that value**. Analogous to `this.setState`

We also pass an **argument** to `useState()`. This argument represents the initial state value, so in the counter example, the initial value of `count` is set to `0`.

We use destructuring assignment to name our value `count` and our function `setCount`, passing `useState()` a value of `0` for the initial state value:
```
 const [count, setCount] = useState(0);
 ```
 
### **Why?**
 Classes can be [confusing](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines). Using Hooks solves a lot of the problems that Classes present, and it makes our code easier to reuse and organize.
 
### **Multiple State Hooks**
 We can even use `useState()` multiple times in the same function.
 ```
 import React, { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0);
  const [products, setProducts] = useState([{ name: 'Happiness', price: 'priceless' }]);
  const [fruit, setFruit] = useState('pear');

  return <div>{/_ use all those things here _/}</div>;
}
 ```
 
## **React's Effect Hook**
 The Effect Hook is used to move component lifecycle methods into functional components. Effects are similar to methods such as `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`. Effects will run after every render, including the first render. 
 
 ```
 import React, { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
	
	  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => setCount(count + 1)}>Count Up</button>
    </div>
  );
}
 ```
 
 This adds a new feature to our earlier counter example- we set the document title to include the number of clicks. We can use effects or "side effects" to fetch data, set up a subscription, and manually change the DOM. In React Classes, we put side effects into `componentDidMount` and `componentDidUpdate` so in order to perform the same side effect after every render (when the component is mounted and when it has been updated), we would have to duplicate the code between these two lifecycle methods.
 
 ```
  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }
```

###  **useEffect()**
`useEffect()` can also take a second argument, an array. This allows us to specify if we would like the side effect to run only once, on mount, or if we would like it to run only if one particular value as changed. To run something before a component unmounts, we have to return a function from `useEffect()`.

**componentDidMount** (Runs once)
```
// only run on mount. pass an empty array
useEffect(() => {
  // only runs once
}, []);
```

**componentDidUpdate** (Runs on changes)
```
// only run if count changes
useEffect(
  () => {
    // run here if count changes
  },
  [count]
);
```

**componentWillUnmount** (Runs before unmounting)
```
useEffect(() => {
  UsersAPI.subscribeToUserLikes();

  // unsubscribe
  return () => {
    UsersAPI.unsubscribeFromUserLikes();
  };
});
```

## **Conclusion**
Hopefully this was a helpful React Hooks orientation! Functional components are no longer "stateless!" Thank you for reading. 

![](https://media.giphy.com/media/BkVqfREIvC012/giphy.gif)

## **Resources**
*   [React Hooks Documentation](https://reactjs.org/docs/hooks-intro.html)
*   [Getting Started with React Hooks](https://scotch.io/tutorials/getting-started-with-react-hooks)
*   [Destructuring Assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)



