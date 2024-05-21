---
creation date: 2022-04-26 09:38
aliases: 
tags: 🖥️
---

# [useCallback Hook](useCallback%20Hook.md)
---
[React](./React.md)'s useCallback hook

### What problem does useCallback solve?
When you define a function inside a component (like an click handler), a new function object is created when the component re-renders. This is totally fine except in cases when a hook like `useEffect` is tracking a function object as a dependency. 

In JavaScript, function object equality *are only equal to themselves*. So if we are creating new function objects every render, `useEffect` will diff the objects and see it's a new object, triggering the side effect callback *every single time*.

`useCallback` hook returns the same function instance between renderings ([memoization](./Memoization.md)) 
- 

```

```



---
Tags: [Programming](Programming.md) - 

Reference:

Related: 