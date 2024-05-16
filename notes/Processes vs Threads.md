---
tags: 📥️ 🌱
publish: true
aliases: 
cssclass: 
---

A computer process is top-level execution container, generally 1 process : 1 application.
- processes get separate memory space
- processes can talk to each other via inter-process communication (IPC)

Threads run *inside* a process
- threads in a process have shared memory space and can share variables
- manual synchronization still needed to avoid race conditions (managing threads is hard!)
	- Thread B waits until Thread A is done writing

[NodeJS](../NodeJS.md) solves this problem by just being single threaded (except for when it's not).
- Node is written in JavaScript and C++
- Javascript, V8, and event loop run on one (main) thread 
- If calling an async method in JavaScript that is C++ backed, could be run in parallel on separate threads

Blocking code means synchronous code, while JavaScript is non-blocking (it uses the event loop for it's [Concurrency](../Concurrency.md) model.)

Examples of blocking modules in NodeJS:
- fs (filesystem)

---
Tags: 

Reference: [NodeJS Event Loop Isn't Single Threaded Video](https://www.youtube.com/watch?v=zphcsoSJMvM)

Related: 