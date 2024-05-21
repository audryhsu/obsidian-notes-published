---
creation date: 2022-04-23 10:18
aliases: 
tags: 🖥️
---

# [useEffect Pitfalls](useEffect%20Pitfalls.md)
---
#### Rules of useEffect:
1. useEffect runs after initial render
2. By default, will invoke the callback fn with side effects after every render 
3. React renders after state change
4. if useEffect sets State, it will cause a rerender, which will trigger useEffect, INFINITELY

##### Hook updates state but does not define any dependencies
Problem: Your useEffect hook updates state inside the callback and creates infinite render loop. ♾️

Solution: List *dependencies* for the effect hook to control when the side-effect should run. 
- dependency could be a different state variable, but shouldn't be the same state variable that useEffect is updating
- If I want useEffect to increment counter state variable, add the input value as the dependency.
- Now, instead of useEffect callback incrementing counter every render, it will only increment when the input value changes

Solution: Use the `useRef` hook 

##### Hook updates state and dependency is an object 
Problem: useEffect hook updates depending on changes to a state variable that is an *object* and the side-effect is updating (creating a new copy) of that object.
- To update a property inside the state variable, we pass in a new object to the setState function. 
- State variable is replaced with a new object with values copied, which is change in state and triggers a re-render.
- state variable is
- this is a dfiferent flavor of the Problem 1

Solution: Avoid using objects as dependencies all together.
Solution: Define dependency as a *specific property* of the object (should be a primitive value)

### Summary
- Fetch data -- usually on initial render, or when an event happens that triggers an event handler 
- 


---
Tags: [Programming](Programming.md) - 

Reference: https://dmitripavlutin.com/react-useeffect-infinite-loop/

Related: 