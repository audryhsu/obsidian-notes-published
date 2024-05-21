---
creation date: 2022-04-05 12:55
aliases: 
tags: 🖥️
---

# [Web Storage API](Web%20Storage%20API.md)
---
The Web Storage API provides mechanisms by which browsers can store key/value pairs that persist between page reloads, in a much more intuitive fashion than using cookies.

The two main mechanisms are *session storage* and *local storage*. Data stored here is never sent to the server, and have higher storage limits than cookies.

Session storage - data is stored until the browser or tab is closed.
Local storage - same as sesion storage but *persists even when the browser is closed* and reopened! 
- no expiration date unless cleared through JavaScript or Browser cache

### Interface
We can access session and local storage via the `Window.sessionStorage` and `Window.localStorage` properties to create an instance of `Storage` object. 

The `Storage` object has methods to manipulate the data such as `setItem`, `getItem`, `removeItem`, `key`, and `length.`
# Examples
```js

```



---
Tags: [Programming](Programming.md) - [Web API](Web%20API.md) - [Web Development](Web%20Development.md)

Reference: [MDN Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)

Related: 