---
creation date: 2022-04-26 07:52
aliases: 
tags: 🖥️
---

# [React Context + Reducer Hooks vs Redux](React%20Context%20+%20Reducer%20Hooks%20vs%20Redux.md)
---
Avoid using Context + Reducer as a make-shift re-implementation of Redux

##### ❓How is [React Context API](./React%20Context%20API.md) different from [React-Redux](./Redux.md)?
- Both can be used to avoid [Prop Drilling](./Prop%20Drilling.md) and use [Dependency Injection](./Dependency%20Injection.md) design patterns 
- Context is how state (that exists somewhere already) is shared with other components. It does not "manage" state.
- Context + `useReducer` resembles React + Redux and can pass the current state value (as opposed to a Redux store instance)
- Redux was designed to easily track how state is changing overtime (e.g. created, stored, and updated), which is "state management" by definition
- Redux comes with middleware to add more logic to handling dispatched actions, tools to track state history and diffs over time, better features for managing side effects

###### Definitions
![React Context API](./React%20Context%20API.md#^8d8711)
![Redux](./Redux.md#^5d996b)

##### ❓When should I use [|Context](./React%20Context%20API.md) & [useReducer](./useReducer%20Hook.md) instead of [React-Redux](./Redux.md)? 
**Use Context when:**
- passing down values to nested components without prop-drilling
**Use Context + useReducer when:**
- managing moderately complex component state *AND* passing state value down to nested components to avoid prop drilling

**Use Redux when:**
- there is moderate to high complexity state management
- need traceability for state changes over time
- strong separation of state management logic from presentational/UI layer
- More complex state management logic, need multiple reducers, or need Redux middleware helpers
- Need bug reports
- Team of developers

---
Tags: [Programming](Programming.md) - 

Reference:  [Context vs Redux](https://blog.isquaredsoftware.com/2021/01/context-redux-differences/)

Related: 