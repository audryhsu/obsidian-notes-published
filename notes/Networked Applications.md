---
creation date: 2021-12-27 17:34
aliases: 
tags: 🖥️
---

# [Networked Applications](Networked Applications.md)
---

Notes on templates, routes, frameworks, and libraries

## Application logic
- server-side processing uses application logic, which determines how the data from the HTTP request is used, and how the body of the HTTP response is formulated

## Templates
- templating is a way to separate application logic and presentation
- templates are molds for web pages and define a consistent structure. They contain placeholders for data.
- template engines combine templates and data, and update placeholders with data
- Many template engines will compile a template source as a function, and the function can be called  later in the program
## Routes
- A route is a combination of HTTP method and URL path and the application logic that is executed when that combination is matched to generate an HTTP response
- *request handler* is the application logic that is executed
- Routes use URL paths as abstractions for a type of request rather than a location within the server file system (in the old days)
- Routing is handled by a routing engine, which abstracts away complexity of matching routes and executing requeset handlers 

## Middleware
- middleware function in the context of a Node application as accepting `req` and `res` objects, doing something with those objects, then passing them to the next function.
	- many middleware functions can be called for each request/response cycle in a chain, and something like `finalhanlder` module terminates middleware chain 

From [ExpressJS Guide](https://expressjs.com/en/guide/writing-middleware.html):
	_Middleware_ functions are functions that have access to the [request object](https://expressjs.com/en/4x/api.html#req) (`req`), the [response object](https://expressjs.com/en/4x/api.html#res) (`res`), and the `next` function in the application’s request-response cycle. The `next` function is a function in the Express router which, when invoked, executes the middleware succeeding the current middleware.

Middleware functions can perform the following tasks:
-   Execute any code.
-   Make changes to the request and the response objects.
-   End the request-response cycle.
-   Call the next middleware in the stack.

# TLDR
We've used Node's `HTTP.createServer` module to create an http server that is primarily concerned with listening on our http port and for incoming requests. 
- http server delegates to the routing engine (`router` module) to parse the http request method and path to route the request to the proper path, either:
	-  fetching a resource from the project file system or,
	- dynamically creating a resource based on request data and leveraging our templates 
![Pasted image 20211227194136.png](../images/Pasted%20image%2020211227194136.png)
# Utilities

## `fs` module
- Node module for working with file system
- `fs.readfile(path, callback)` asynchronously reads entire contents of a file
	-  the callback is passed two arguments `(err, data)` where `data` is the contents of the file

---
Tags: [Programming](Programming.md) - [JavaScript](JavaScript.md) - [Web Apps](Web%20Apps.md)

Reference: [Nodejs fs.html API](https://nodejs.org/api/fs.html#fsreadfilepath-options-callback)

Related: 