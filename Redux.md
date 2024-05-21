---
creation date: 2022-04-07 00:12
aliases:
  - React-Redux
tags: 🖥️
---

# [Redux](Redux.md)
---
[Redux](Redux.md) is a pattern and library for **managing and updating app state** using events called "actions". It is a centralized store for state that needs to be used across the app with rules ensuring state can only be updated in a predictable pattern. Redux makes it **easier to understand when, where, why, and how state is being updated**. ^5d996b

Single page applications must manage more state than ever before. Redux attempts to **make state mutations predictable** by imposing restrictions on how and when updates can happen. Colloquially, "Redux" refers to using a Redux store and the React-Redux library together

### Why use Redux
Redux helps *manage state* globally, or state that is needed across many parts of the application. 
Redux is most helpful when:
1. large amount of app state needed in many places in the app
2. app state is frequently updated
3. logic to update state may be complex
4. app is not small codebase

Redux can be integrated with any UI framework, but it's most commonly used with [React](./React.md). Redux helps when [lifting state up](./React.md#Lifting%20State) becomes too complex.  

🔑 Redux extracts shared state into a centralized location *outside* the component tree, so any component can access the state or trigger actions regardless of where in the tree hierarchy they are.

### Principles of Redux
1. Single source of truth for global state is the single store
2. State is read-only: the only way to change it is by emitting an action and describing what happened
3. Changes are made with [pure functions](./State%20in%20React%20and%20Redux.md#Why%20pure%20functions%20are%20required) (reducers)

### Key Redux concepts
Actions are dispatched (sent) to the store via dispatch method. Stores are subscribed to callback functions that get called when state is changed. 

Stores role is similar to a model in a MVC, but they manage the state of many objects

![Pasted image 20220407121116.png](./images/Pasted%20image%2020220407121116.png)

#### Mental Model
In terms of Stephen Grider's metaphor with Insurance Company, the `store` is the container encompasses the entire data flow, or the *entire company*:
- Customer triggers an action (a form to file a claim or create a policy) is dispatched 
 by the `store` to all of Insurance Co's departments (reducers). 
- Each department gets the most current data from the store state (master filing cabinet) and determines if the form (action) is relevant to them or not. 
	- If it is, they do some processing based on the form type (action type) 
	- Departments send a new state object to the store's centralized spot
	- If action is irrelevant, they return a copy of previous state to `store`
![Drawing 2022-04-07 16.04.12.excalidraw](./images/Drawing%202022-04-07%2016.04.12.svg)

#### Actions
**Actions** are plain JavaScript objects with `type` fields that *describe something that happened* in the app. 

**Action creators** are functions that create action objects, e.g function factory.

#### Reducers

> [!NOTE]
> 
> Redux reducers reduce a set of actions (over time) into a single state. They get their name from JavaScript `Array.prototype.reduce()`. 

**Reducers** are functions that receive current state and an action object, and decides how to update state, similar to how an event listener that handles events based on the action (event type). 

> [!important]
> 
> Reducers return a *new state object* and do not directly mutate state. React compares the old state object to the new one to determine whether an update has been made and to re-render.
> 

Reducers follow strict rules
1. Reducers calculate new state only based on `state` and `action`
2. Reducers must make immutable updates (e.g. copies) of existing state
3. [Reducers should be pure functions](./State%20in%20React%20and%20Redux.md#Principles%20of%20React%20Redux) -- not cause side effects or be async, and ==should replace state objects with a new changed object== 

Reducers *should not be called directly from the application* code, and only be used  as a parameter to the`createStore()` function that creates the data store.

#### Stores
The store can:
- holds most recent copy of state
- holds a list of listener functions that are invoked whenever state is changed
- method to get state data
- dispatches actions that:
	- call the reducer to update state
	- invoke all listener functions

**Stores** bring together state, actions, and reducers. Store responsibilities are:
1. hold current app state inside
2. allow access to current state via `store.getState()`
3. allow state to be updated via `store.dispatch(action)`
4. registers listener callbacks via `store.subscribe(listener)`
	1. unregister listeners via `unsubscribe` function returned by `store.subscribe()`

*There will only be a single store in Redux application*. Multiple reducers should be used to split data handling logic. 


**Simplified version of a store source code** from [Redux docs](https://redux.js.org/tutorials/fundamentals/part-4-store):
```js
function createStore(reducer, preloadedState) {
  let state = preloadedState
  const listeners = []

  function getState() {
    return state
  }

  function subscribe(listener) {
    listeners.push(listener)
    return function unsubscribe() {
      const index = listeners.indexOf(listener)
      listeners.splice(index, 1)
    }
  }

  function dispatch(action) {
    state = reducer(state, action)
    listeners.forEach(listener => listener())
  }

  dispatch({ type: '@@redux/INIT' })

  return { dispatch, subscribe, getState }
}
```

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) - [Front-end Development](Front-end%20Development.md)

Reference: 

Related: [React](./React.md) - [State in React and Redux](./State%20in%20React%20and%20Redux.md)