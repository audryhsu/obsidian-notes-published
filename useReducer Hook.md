---
creation date: 2022-04-25 11:51
aliases: 
tags: 🖥️
---

# [useReducer Hook](useReducer%20Hook.md)
---
[React](./React.md)'s useReducer hook helps separate state management concerns from presentational concerns for components by managing updates to state. It's design is based on [Flux](./Flux.md), and uses similar pattern to [Redux](./Redux.md). 

### Mental Model 🧠
⚓ Captain uses the engine order telegraph (dispatcher) to dispatch different speed commands (actions) for the ship. The engine room engineers (reducer function) interpret the action and change the ships speed (state) accordingly. ⛴️

[Redux mental model](./Redux.md#Mental%20Model) also works. If you understand that, then you understand `useReducer` and you can implement shared state with much less code than [Redux](./Redux.md) 

#### How is it different than [Redux](./Redux.md) store? 
Redux provides a *global store* to contain state above the entire application, `useReducer` creates an independent component co-located store.  
While both use reducer functions...
- Redux can combine all reducers into one ultimate reducer
- Redux uses a ONE dispatch function that consumes *any action type* for *any reducer* 
- Each `useReducer` hook comes with its own specific dispatch function that sends actions to only that specific reducer function.   

#### Basic setup
- `reducer` is a [pure function](./State%20in%20React%20and%20Redux.md#Why%20pure%20functions%20are%20required) that contains the logic on *how* to update state based on the `action` object, and will return a *new state object*. 
	- must  [React > Updating state in an immutable way](./React.md#Updating%20state%20in%20an%20immutable%20way)
	- must take `state` and `action` as required parameters
- `initialState` is a value to initialize `state` variable
- `action` is an object that describes what happened and must include a `type` property that will determine how the `reducer` will handle the action 
- `dispatch(action)` - special function created by `useReducer` hook that sends the action to the `reducer`.
	- call dispatch to update state with action, which gets forwarded to reducer
```js
import { useReducer } from 'react'

const Component = () {
	const [state, dispatch] = useReducer(reducer, initialState)
	const action = {
		type: 'ActionType',
		data: { /*optional payload*/}
	}
	return (
		<button onClick{ () => dispatch(action)} />
	)
}

// Example reducer function that changes a counter's state
function reducer(state, action) {
  let newState;
  switch (action.type) {
    case 'increase':
      newState = { counter: state.counter + 1 };
      break;
    case 'descrease':
      newState = { counter: state.counter - 1 };
      break;
    default:
      throw new Error();
  }
  return newState;
}

```

![Pasted image 20220425120844.png](./images/Pasted%20image%2020220425120844.png)

# Example
```js
// Stopwatch example 
import { useReducer, useEffect, useRef } from "react";

const initialState = {
  isRunning: false,
  time: 0
};

export default function Stopwatch() {
  const [state, dispatch] = useReducer(reducer, initialState);
  const idRef = useRef(0);

  useEffect(() => {
    if (!state.isRunning) {
      return;
    }
    console.log('ticking!')
    idRef.current = setInterval(() => dispatch({ type: "tick" }), 1000);
    return () => {
      console.log('clearing interval')
      clearInterval(idRef.current);
      idRef.current = 0;
    };
  }, [state.isRunning]);

  return (
    <div className="stopwatch">
      {state.time}s
      <div>
        <button onClick={() => dispatch({ type: "start" })}>Start</button>
        <button onClick={() => dispatch({ type: "stop" })}>Stop</button>
        <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
      </div>
    </div>
  );
}

function reducer(state, action) {
  switch (action.type) {
    case "start":
      return { ...state, isRunning: true };
    case "stop":
      return { ...state, isRunning: false };
    case "reset":
      return { isRunning: false, time: 0 };
    case "tick":
      return { ...state, time: state.time + 1 };
    default:
      throw new Error();
  }
}
```

---
Tags: [Programming](Programming.md) - 

Reference: [DP useReducer](https://dmitripavlutin.com/react-usereducer/)

Related: 