---
creation date: 2022-04-26 07:55
aliases: 
tags: 🖥️
---

# [Prop Drilling](Prop%20Drilling.md)
---
*Prop drilling* is a concept in [React](./React.md) that refers to  passing down props through several intermediary components of the component tree (that may not use the prop) to share the prop to a deeper nested children component that needs access. 

Two ways to solve for prop drilling:
1. use [React Context API](./React%20Context%20API.md) (also a hook)
2. Use component composition (using children components as mixins to another component that gets passed down the Component Tree.)



---
Tags: [Programming](Programming.md) - 

Reference:

Related: 