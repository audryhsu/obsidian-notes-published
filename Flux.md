---
creation date: 2022-04-25 11:57
aliases: 
tags: 🖥️
---

# [Flux](Flux.md)
---
Flux was the predecessor to [Redux](./Redux.md) (both developed by Facebook) to make state management easier by separating state from [React](./React.md) components into *stores* in a *unidirectional data flow*. Flux and Redux are more pattern than framework. 

There is a single dispatcher that is the central hub that manages all data flow in Flux applications. It is a registry of callbacks into stores -- its job is to distribute actions to all stores. Stores then update themselves. 

![Pasted image 20220407152309.png](./images/Pasted%20image%2020220407152309.png)



---
Tags: [Programming](Programming.md) - [Front-end Development](Front-end%20Development.md)

Reference:

Related: 