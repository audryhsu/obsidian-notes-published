---
creation date: 2022-02-22 09:54
aliases: 
tags: 🖥️
---

# [Cross-Origin Resource Sharing](Cross-Origin%20Resource%20Sharing.md)
---
A cross-origin request is any request that tries to access a resource from a different origin (scheme/protocol, port, or host). These requests could be for an image, a JavaScript file, or any other resource. 

To protect against [XSS](./HTTP.md#Cross%20site%20scripting%20XSS) and CSRF attacks, browsers by default do not allow cross-origin requests, including [XMLHttpRequest](./XMLHttpRequest.md) objects. To facilitate secure sharing of cross-origin resources, the W3C established the Cross-Origin Resource Sharing specification that defines a communication protocol for browsers and servers to determine whether a cross-origin request is good or not (succeed or fail). 

Implementation:
- Every XHR request sent by a browser automatically includes an `Origin` header that contains the origin of the requesting page. 
- Server determines whether it should provide a response based on the request's `Origin`
	- If yes, server sends the response back with the same origin in the `Access-Control-Allow-Origin` header
- Browser looks at the `Access-Control-Allow-Origin` header in the response and confirms it contains the correct origin (the initial origin that the browser sent)
	- If yes, browser allows the response to pass to the application
	- If not, browser raises an error and blocks script access to the response 

If the server wants to make a resource available to everyone, it can send the same header, but with a value of \*:
`Access-Contorl-Allow-Origin: *`


## Examples
Resources with different origins from  http://mysite.com/doc1. 
```
https://mysite.com/doc1 (different scheme/protocol)
http://mysite.com:4000/doc1 (different port)
http://anothersite.com/doc1 (different host)
```
- If the web page from this URL requests a resource from any of the following URLs, it will be considered a cross-origin request.


---
Tags: [Programming](Programming.md) - [Web Development](Web%20Development.md) - [HTTP](./HTTP.md)

Reference:

Related: 