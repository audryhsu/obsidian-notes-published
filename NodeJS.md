---
creation date: 2022-03-22 22:05
aliases: 
tags: 🖥️
---

# [NodeJS](NodeJS.md)
---
 NodeJS is a JavaScript runtime based on Google's Chrome V8 JavaScript engine. 
 
 A Node.js app runs in a single [process](./notes/Processes%20vs%20Threads.md), without creating a new thread for every request. Node.js provides a set of asynchronous I/O primitives in its standard library that prevent JavaScript code from blocking and generally, libraries in Node.js are written using non-blocking paradigms, making blocking behavior the exception rather than the norm.
 - NodeJS resumes I/O operations (reading from database/filesystem or network)   when response comes back from 

### [Concurrency](./Concurrency.md) and Throughput
[JavaScript](./JavaScript.md) execution in Node.js is **single** threaded, so [concurrency](./Concurrency.md) refers to the [Event Loop](./Event%20Loop.md)'s capacity to execute JavaScript callback functions after completing other work. 



### Creating a simple web server

Node has a built-in `http` module that we can spin up simple servers that run on localhost
```js
const http = require('http')

// create a new web server with an event handler register to server that is called every time an HTTP request is made to localhost:3001
// respond with status code 200 and plaintext
// send a plain text response
const app = http.createServer((request, response) => {
  response.writeHead(200, { 'Content-Type': 'text/plain' })
  response.end('Hello World')
})

const PORT = 3001
app.listen(PORT)
console.log(`Server running on port ${PORT}`)
```

# Examples
```js

```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) - [Backend Development](Backend%20Development.md) 

Reference:  https://nodejs.org/en/docs/guides/blocking-vs-non-blocking/

Related: [ExpressJS](./ExpressJS.md) - [npm](./npm.md)