# React Study

## JavaScript Recap

1. Template strings: Template strings allow you to embed expressions into strings using the `${}` syntax. They are enclosed in backticks (`).

   ```js
   const name = 'John';
   const age = 30;
   
   console.log(`My name is ${name} and I am ${age} years old.`);
   ```

2. array vs object vs map

   Objects in JavaScript are key-value pairs where the keys are always strings (or symbols), and the values can be any type of data, including functions, arrays, and other objects. Objects are very commonly used in JavaScript for data storage and manipulation.

   Maps, on the other hand, are a relatively new data structure in JavaScript (introduced in ES6) that allow you to store key-value pairs where the keys can be any type of data, not just strings. Maps also have built-in methods for getting and setting values, and they maintain the order of the entries.

   ```js
   // Empty array
   const emptyArray = [];
   
   // Array with elements
   const myArray = [1, 2, 3, 4, 5];
   
   // Array with mixed types
   const mixedArray = ['apple', 1, true, {name: 'John'}];
   
   const emptyObject = {};
   
   // Object with properties
   const person = {
     name: 'John',
     age: 30,
     isStudent: true
   };
   
   // Object with dynamic property names
   const dynamicObject = {
     [firstName]: 'John',
     [lastName]: 'Doe'
   };
   
   const myMap = new Map();
   myMap.set('name', 'John');
   myMap.set('age', 30);
   myMap.set('isStudent', true);
   ```

3. array methods

   - `push()`: adds one or more elements to the end of an array.

   - `pop()`: removes the last element from an array.

   - `shift()`: removes the first element from an array.

   - `unshift()`: adds one or more elements to the beginning of an array.

   - `slice()`: returns a new array containing a portion of an existing array.

   - `splice()`: removes elements from an array and/or inserts new elements at a specified index.

     ```js
     const myArray = [1, 2, 3, 4, 5];
     myArray.splice(2, 2, 'a', 'b');
     console.log(myArray); // [1, 2, 'a', 'b', 5]
     ```

   - `map()`: creates a new array by applying a function to each element of an existing array.

     ```js
     const mappedArray = myArray.map(num => num * 2);
     ```

   - `filter()`: creates a new array containing only the elements of an existing array that pass a specified condition.

     ```js
     const filteredArray = myArray.filter(num => num > 2);
     ```

   - `reduce()`: applies a function to each element of an array, resulting in a single output value.

     ```js
     const reducedValue = myArray.reduce((accumulator, currentValue) => accumulator + currentValue);
     ```

4. spread operator ...

   ```js
   const array1 = [1, 2, 3];
   const array2 = [4, 5, 6];
   const newArray = [...array1, ...array2];
   console.log(newArray); // [1, 2, 3, 4, 5, 6]
   ```

5. array/object destructuring

   ```js
   const myObject = { x: 1, y: 2, z: 3 };
   const { x, y, z } = myObject;
   console.log(x); // 1
   console.log(y); // 2
   console.log(z); // 3
   ```

6. Arraw function

   ```js
   const multiply = (a, b) => {
     return a * b;
   }
   
   //If the function body contains only a single expression, you can omit the curly braces and the return keyword. 
   const multiply = (a, b) => a * b;
   ```

7. lexical scope

   Lexical scope is a way of determining variable scope based on where they are defined in the code. In JavaScript, each function creates a new lexical scope, and variables declared inside that function are only accessible within that scope and any nested scopes.

8. immutability

   Immutability is a concept in programming where once a data structure is created, its values cannot be changed. Instead of modifying the original data structure, a new one is created with the updated values.

   Strings, Number, Boolean, Array, Object

   Arrays in JavaScript are mutable by default, use the `concat`, `slice`, or `spread` operator to create a new array with the desired values.

    objects in JavaScript are mutable by default, use the `Object.freeze` method.

9. Promises

   Promises are a way to handle asynchronous operations in JavaScript. They represent a value that may not be available yet, but will be at some point in the future. 

   ```js
   const myPromise = new Promise((resolve, reject) => {
     setTimeout(() => {
       const randomNumber = Math.floor(Math.random() * 10);
       if (randomNumber % 2 === 0) {
         resolve(randomNumber);
       } else {
         reject('Odd number');
       }
     }, 1000);
   });
   
   myPromise.then((result) => {
     console.log(`The result is ${result}`);
   }).catch((error) => {
     console.error(`Error: ${error}`);
   });
   ```

   

10. fetch api

    The Fetch API is a modern JavaScript API for making network requests to fetch resources from the server

    ```j
    fetch('https://jsonplaceholder.typicode.com/posts')
      .then(response => response.json())
      .then(data => console.log(data))
      .catch(error => console.error(error));
    ```

    

## React Intro

1. Import

React is a JavaScript library for building User Interfaces.

```javascript
import React from "react";
```

```js
const element = document.createElement("h2");
element.className = "container center";
element.style = "color: red; background-color: blue";

// this creates: <h2></h2>
// which you can then insert in the DOM:
document.body.appendChild(element);
```

2. create element

we can see all the properties by using `console.dir(element)`

Multiple classes can be set by separating the class names with a space character

changing the element's `class` happens through the `className` property and not `class`.
That's because the keyword `class` in JavaScript is **reserved** and is used for creating a JavaScript class that can be called with `new`

`document.createElement` VS `React.createElement`.

- Return value

  `document.createElement` returns a **DOM element** (for example a `div` or an `h1`). Whereas `React.createElement` returns an **object** that represents the DOM element. Because React operates a Virtual DOM, A virtual DOM is when a representation of the UI is kept in memory and synced with the DOM. This allows React to perform performance optimizations

- To write a text inside the element, you have to provide the 3rd parameter for React.createElement, called `children`

  ```
  React.createElement("h1", {}, "Hello World")
  ```

## React Dom Intro

ReactDOM is the glue between React and the DOM (**Document Object Model**).
React creates a virtual representation of your User Interface (in what we call a Virtual DOM). Then ReactDOM is the library that efficiently updates the DOM based on that Virtual DOM.

ReactDOM binds the idea of React to a web browser (example: Firefox, Chrome, Safari, Edge, etc.)

**reconciliation**: creating the virtual representation of your UI in the memory, and then ReactDOM receives that and syncs your UI

React + ReactDOM weigh 130 + 6 = 136KB.

```javascript
import {createRoot} from "react-dom/client";
```

```js
import {createRoot} from "react-dom/client";

const root = document.querySelector("#root");
createRoot(root).render(React.createElement("p", {}, "Hello World"));
```

1. We start by getting a reference to the root element from the page (using `querySelector` or `getElementById`).
2. We create the root of the React app using `createRoot(root)`.
3. On the result of `createRoot(root)`, we call `.render()` and pass to it our React element.

This **root** element that you pass to `ReactDOM` will become **completely managed by React**. So you should not write any kind of JavaScript that changes its content.

- An app built with React usually has a **single root element**

- It is also possible to have an existing website built with another technology other than React and then integrate **React** to make a certain part of that website interactive

```
<div id="react-cart"></div> root element
```

## JSX

This JSX syntax **may look similar to HTML, but it is NOT HTML**.

```jsx
createRoot(root).render(<h1>Hello World</h1>);
```

You can use these expressions in JSX by wrapping them with curly brackets `{}`

在 JSX 中，我们可以内嵌 JavaScript。

## State

state 就是 **由组件管理的状态的集合**。

```js
import React, { useState } from 'react'
```

通过调用 `useState()`，我们将会得到一个 state，以及一个供我们调用，用来修改 state 值的函数。

`useState()` 可以传入一个参数，用来初始化 state。它会返回一个数组，这个数组包含一个 state 和一个修改 state 值的函数。

```js
const [count, setCount] = useState(0)
```

我们不能直接修改 state，只能通过调用修改函数来修改它，否则，React 组件无法及时将数据的变化反映在 UI 中。

```react
import { useState } from 'react'

const Counter = () => {
  const [count, setCount] = useState(0)

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  )
}

ReactDOM.render(<Counter />, document.getElementById('app'))
```



## Props

我们称传入组件的初始值为 props。

```js
function WelcomeMessage({ myprop }) {
  return <p>{myprop}</p>
}

<WelcomeMessage myprop={'somevalue'} />
```

有一种被称为 `children` 的特殊 props，它代表了包含在组件的开始标签和结束标签之间的所有内容

```react
<WelcomeMessage> Here is some message </WelcomeMessage>
function WelcomeMessage({ children }) {
  return <p>{children}</p>
}
```



## 处理用户事件

```react
<button
  onClick={(event) => {
    /* handle the event */
  }}
>
  Click here
</button>
```

React 支持非常多的事件类型，如：`onKeyUp`，`onFocus`，`onChange`，`onMouseDown`，`onSubmit` 等。