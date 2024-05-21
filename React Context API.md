---
creation date: 2022-04-25 11:01
aliases: 
tags: 🖥️
---

# [React Context API](React%20Context%20API.md)
---
[React](./React.md) Context provides **a way to share state** (e.g. "global variables") through component tree without having to pass props down manually through all levels. Drawing on [Flux](./Flux.md) architecture, data is stored in an "external" store that child components can access, however it is not a *state management* tool, since *all* consumer components are re-rendered anytime context is changed. ^8d8711

⭐ Context solves the problem of [Prop Drilling](./Prop%20Drilling.md) through the concept of [Dependency Injection](./Dependency%20Injection.md). 
🤔 Make sure you know when to use [React Context + Reducer Hooks vs Redux](./React%20Context%20+%20Reducer%20Hooks%20vs%20Redux.md)

### Mental Model 🧠
Context API is like a Super Mario Bro's pipe that creates a shortcut to accessing a different part of the map. The top of the pipe is the `Context.Provider` that sends the context value through the warp to the other end where a component asks for it using `useContext(myContext)` hook. 
![Pasted image 20220426082942.png](./images/Pasted%20image%2020220426082942.png)

Common use cases:
- theme data (e.g. dark or light mode)
- user data (who is the currently authenticated user ), user settings
- user locale or language
- application configuration

Tradeoffs:
- adds complexity - creating context, context provider, `useContext` hook
- harder to unit test components
```ad-caution
Context is not a drop-in replacement for application state management when you are *updating* state, because all context consumers are notified and re-rendered. Context makes it easier to *consume* global variables that do not change often (e.g. read-only).

```

### How to use Context
![Pasted image 20220425160202.png](./images/Pasted%20image%2020220425160202.png)
**Workflow:**
2. Create context instance using `createContext(<default value>)` method
	1. Use react's factory function to create a context instance, which accepts optional default value. Default value is returned when a consumer component isn't wrapped inside the provider but tried to access context value (using `useContext(context)` hook or `<Context.Consumer>`)
3. Providing context by wrapping the `Context.Provider` around component tree 🌲
	1. 
4. Consume the context using `Context.Consumer` to read the value from any component
	1. ✅ easiest way to consume is using the `useContext()` React hook, which returns the context `value`
	2. Second way is to use a render function

`Context.Provider` is a component from the context instance we created that can provide context to descendant components anywhere lower in the component tree.
- It comes with a `value` prop that you can set the context to
- All components that will consume the context `value` must be wrapped by the `Context.Provider` in order to have access

`Context.Consumer` is another component that subscribes to context changes within a function component. 
#### Changing Context
You can change context value in the component that the has the `<Context.Provider>` wrapper.

- All components that consume the context and are descendants of Provider will be notified and re-render when Provide `value` prop changes.

- use `useState` or `useReducer` to update context value
- provide the `setState` function as part of the context object that consumers can access

**Sharing user information with Context**
```js
import { createContext, useState, useContext, useMemo } from 'react';
// pass an object with context value userName AND a function to setState
const UserContext = createContext({
  userName: '',
  setUserName: () => {},
});

function App() {
  const [userName, setUserName] = useState('John Smith');
  const value = useMemo(
    () => ({ userName, setUserName }), 
    [userName]
  );
  
  return (
    <UserContext.Provider value={value}> // Provide context that is hooked into state
      <UserNameInput />
      <UserInfo />
    </UserContext.Provider>
  );
}
function UserNameInput() {
// consume context AND function to change it
  const { userName, setUserName } = useContext(UserContext); 
  const changeHandler = event => setUserName(event.target.value);
  return (
    <input
      type="text"
      value={userName}
      onChange={changeHandler}
    />
  );
}
function UserInfo() {
	// consume context
  const { userName } = useContext(UserContext);
  return <span>{userName}</span>;
}

```



---
Tags: [Programming](Programming.md) - 

Reference: https://dmitripavlutin.com/react-context-and-usecontext/

Related: 