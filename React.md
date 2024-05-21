---
creation date: 2022-03-17 13:30
aliases: 
tags: 🖥️
---

# [React](React.md)
---
React is a declarative and flexible JavaScript library for building user interfaces. It lets you compose complex UIs from small and isolated pieces of code called “components”.

React operates at the View layer of the MVC pattern and uses JSX, a syntax extension to [JavaScript](./JavaScript.md) that looks like HTML. React is a *library* for managing creation of *views* and is more narrowly focused than a Frontend MVC *framework* like [Angular](Angular.md). 

React was created by Facebook to improve the way large applications with lots of temporal data changes (e.g. likes, comments) are developed by using **reusable components** built by full featured programming languages (JavaScript) to render views instead of templates and HTML directives.

##### 🔗 Index 
- [ReactJS Forms](./ReactJS%20Forms.md)
- [useEffect Pitfalls](./useEffect%20Pitfalls.md)
- [React-Redux](./Redux.md)
- [State in React and Redux](./State%20in%20React%20and%20Redux.md)
- [React Context API](./React%20Context%20API.md)
- [useReducer Hook](./useReducer%20Hook.md)
- [React Context + Reducer Hooks vs Redux](./React%20Context%20+%20Reducer%20Hooks%20vs%20Redux.md)
- [useCallback Hook](./useCallback%20Hook.md)
- 

### Why use React
- **Views are easier to extend** and maintain because components are reusable. 
- **Renders are very efficient** since it compares previous render call to the new one to generate *minimal* set of changes to the DOM via a React element, which is a lightweight description of what to render.
- **JSX is more powerful than templating languages** since it's basically fully loaded JavaScript. We can inject array iteration methods like `map` directly inside our HTML, ternary operators, etc.

### Elements and Components
**Elements** are the smallest building blocks of React apps. These describe what you want to see on the screen.
- plain objects, not DOM nodes
- `ReactDOM` does the work of updating the DOM to match the React elements
- elements are immutable: it represents a UI at a certain point in time
	- to update the DOM, pass a new element to `ReactDOM.render`

**Components** are like JS functions, as they accept inputs ("props", or properties) and return React elements that describe what should appear on the screen.
- Components let you split the UI into independent, reusable pieces, and think about each piece in isolation. 
- use *props to pass data* to components
	- props can be hard coded values or {JavaScript expressions } that get evaluated
- Components can refer to other components
- We can create a component by defining a JS function or by [subclassing](./Subclassing%20and%20Code%20Reuse%20Patterns.md#Subclassing%20with%20class%20declarations)from `React.Component` 

There are three (?) main ways of sharing data between components in React: component state, props, and [|context](./React%20Context%20API.md). 

#### Component State
Components can be stateless, and also *stateful*. A component's state is similar to props, but fully private and controlled by the component (like classes)
- State can be as simple as a variable local to the component using [useState hook](React.md#useState%20hook)
- ~~We can encapsulate state inside our own component subclass with it's own private properties and methods, like in JavaScript. (Note: this is the old way)~~

==Components must act as [pure functions](./Side%20Effects.md#Pure%20functions) and never mutate their props==. To [maintain this principle](./State%20in%20React%20and%20Redux.md), components can manage their own state. 

> [!important]
> React rules of state:
> 1. Anytime the `setState` function is invoked and changes the state, the component (and all its sub-components) will rerender. 
> 2. Changing state must be done by setting state to a *new object*. Copy over any unchanged  properties to the new object.
> 

##### Special state properties
`state` - references an object that tracks the state of the component, like the current date and time 
- initialized in the component constructor function
- should *only be updated using `setState`* function
`setState()` - special method that updates the component's `state` given an object. ==Calls to `setState()` let React know that the state has changed, and call `render` method again to fresh the component and update the DOM.== 

###### Updating state in an immutable way
❓ How do we use `setState` if `state` is a constant? 
- Objects: use `Object.assign` to create a *copy* of state, then pass in an updated copy to `setState`
- Arrays: make a copy of an array using slice`[...]` , update the slice, then pass into `setState`


##### Lifting State
>One best practice in React is to *lift the state up in the component hierarchy*, aka state should be in the closest common ancestor, and then passed down through props.

This means we should keep state in the top level component (like App), and then pass state as props to sub-components. As apps grow, state management libraries like [React-Redux](./Redux.md) that use [Flux](./Fullstack%20Open%20Project%20(FSO).md#Flux%20and%20Redux) principles have become very popular and synonymous to use together. 

### Event handlers
We can pass references to event handler callbacks as component attributes, like `onClick` (name is by convention, not required). We *must* pass a function expression, anonymous function, or function reference.

⚠️ Do not pass a function invocation, which will cause an infinite loop of component renders (since the state is being updated upon each rerender...)

Note: here we are using the `useState` [hook](React.md#Hooks) and destructuring syntax to assign the state and setState functions to `counter` and `setCounter` respectively
```js
import { useState } from 'react'

const App = () => {
  const [ counter, setCounter ] = useState("0")
  const increaseByOne = () => setCounter(counter + 1)
  const setToZero = () => setCounter(0);

  return (
    <div>
    <Display counter={counter}/>
    <Button onClick={ increaseByOne} text="plus"/> // reference
    <Button onClick={ () => setCounter(0)} text="zero"/> // arrow ok
    </div>
  )
}
const Button = ({ onClick, text}) => (
  <button onClick={onClick}>
  {text}
  </button>
)
```
- the event handler changes the state of the `App` component with the `setCounter` function

### Hooks
Hooks allow you to *hook into* React state and lifecycle features from function components. They let you use state and other React features without writing a class. There are some built-in hooks like useState, and you can create your own to reuse stateful behavior between different components

> [!tip]
> Do *not* call hooks from inside of a loop, conditional expression, or any place that is not a function defining a component! It's important that hooks are called in the same order.

##### Rules of using Hooks ⚠️
1. Don't call hooks inside loops, conditionals, or nested functions. Always use at top level of React function.
2. Don't call hooks from regular JS functions. Instead:
	1. Call Hooks from React function Components
	2. Call hooks from custom hooks

#### useState hook
The `useState` hook returns a pair: the *current* state value (e.g. we've named it `counter` below) and *a function that lets you update it* (`setCounter`)

Name the function to update state `set{$NAME_OF_STATE_VARIABLE}` by convention. 
```js
import { useState } from 'react'

const App = () => {
  const [ counter, setCounter ] = useState("0")
  //...
```
- We use a `useState` hook to create the app state
	- set the initial value of `counter` variable to `0`
	- specify `setCounter` as the special method that can set the state (e.g. access and change the `counter` variable)

We can use the State Hook multiples times:
```js
function ExampleWithManyStates() {
  // Declare multiple state variables!
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
}
```

#### useEffect hooks
The Effect Hook lets you perform side effects in function components. **Data fetching,** setting up a subscription, and manually changing the DOM in React components are all examples of side effects. Inside the hook, you tell React that your component needs to do *something after each render* after DOM updates. 

###### How to use it
- By default, `useEffect` runs *after* first render and *every time* the state is updated
- state is updated if at least one of the values specified as part of effect's dependencies has changed since last render cycle
- `useEffect` hooks should be **called inside a component** to access the component state via function scope. React linter will throw an error if you don't.
- useEffect cannot be async, but callback fn can contain async functions

###### Order of operations
1. First body of Component function is executed and rendered for first time
2. `useEffect` callback is executed immediately after first render
3. If callback calls a `setState` function, another rerender occurs

Syntax: `useEffect(<callback fn>, [<values the effect depends on>]`

###### When to useEffect
- fetching data (or other async tasks)
- reading from local storage
- registering & deregistering event listeners
- setting and clearing timers

###### useEffect Dependencies
- The default behavior is to fire the effect after initial page load and after *every completed render*. 
*Dependencies* in second argument controls when to call side effect outside of the render cycle
- **First render only** - leave the array empty, then `useEffect` callback will only be invoked on first render. (this works great for initial data fetch!)

###### State Lifecycle Methods (prior to hooks)
Note: [Effect hooks](React.md#Effect%20hooks)  should be used instead of these. Effect hooks are an abstraction over the lifecycle methods. 
- While lifecycle methods are very explicit, it caused repetitive code and didn't have the best separation of concerns. 
~~We can use special lifecycle methods to that React will called after the component output is inserted in the DOM and also after the component is removed from the DOM.
- `componentDidMount()`
- `componentWillUnmount()`

### Modules
Convention is to declare each component in their own file as an ES6-module in the `src` folder under `components.`

To import a module, use  a relative path from the importing file: `import Note from ./components/Note`

Exporting a module
```js
import React from 'react'
const Note = ({note}) => {
  return (
    <li>{note.content}</li>
  )
}
export default Note // export and name component to be available externally 
```

### Inline Styling
React's philosophy is to divide the application based on logical functional entities, and since React components server as the functional entities, React encourages that styling is contained within the component. 


### Virtual DOM
In react, the [Virtual DOM](https://reactjs.org/docs/faq-internals.html#what-is-the-virtual-dom) is the representation of the UI tracked in memory that is synched with the "real" DOM  by ReactDOM library during a reconciliation process. 
When using React, developers will rarely interact with the DOM directly. Instead, we use JSX and React elements (that look like HTML) to define the appearance of components.  

When React app state changes, it renders a new *virtual DOM* defined by components. Since React stores older versions of Virtual DOM (how it tracks state), React compares old virtual DOM to the new virtual DOM and finds the optimal way to update the DOM using [DOM API](./Document%20Object%20Model.md)

This abstraction allows us to use a declarative style of defining how we want the UI to look. 


## Syntax Examples
###### Conditional Rendering
We can use if/else blocks to return different JSX codes, or use inline { expressions }
```js
const DisplayPopular = ({votes, anecdotes}) => {
  let max = votes.reduce((a,b) => {
    return Math.max(a,b)
  }, -Infinity)
  let idx = votes.indexOf(max)
  // ternary expression
  return <p>{ max < 1 ? "No votes yet" : anecdotes[idx]}</p>
}
```

###### Loops
Like with vanilla JS and the DOM, we can use loops to dynamically create JSX elements
```js
  let stats = {
    "good": good,
    "neutral": neutral,
    "bad": bad,
	//...
  }
const Statistics = ({stats}) => {
  let statElements = Object.keys(stats)
    .map(stat => {
      return <StatisticLine text={stat} value={stats[stat]} key={stat} />
  });
  return (
    <table>
    <tbody>
      {statElements}
    </tbody>
  </table>
  )
}
```
- Note that we must also create a `key` attribute inside the array iteration to help React identify which items have changed/added/removed
- Use a unique key that identifies the list item, such as object key, amongst siblings
- ❌ Anti-pattern: Only use item index as a last resort, since order isn't guaranteed!


---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) -[Front-end Development](Front-end%20Development.md)

Reference: [Recommended Egghead](https://egghead.io/courses/the-beginner-s-guide-to-react) | [Getting Started with React](https://www.taniarascia.com/getting-started-with-react/)

Related: [React-Redux](./Redux.md) - [State in React and Redux](./State%20in%20React%20and%20Redux.md)