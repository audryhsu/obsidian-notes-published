---
creation date: 2022-02-21 18:10
aliases: 
tags: 🖥️
---

# [XMLHttpRequest](XMLHttpRequest.md)
---
An XMLHttpRequest (XHR) object is part of the browser Web API that is commonly used in [AJAX](./AJAX.md) programming. It's used to retrieve data (not limited to XML) from a URL and enables web applications to update parts of the page without having to do a full page refresh. Yay single page applications!    ^9bd8e7

It inherits from the `XMLHttpRequestEventTarget` -> `EventTarget` interfaces. 

### Constructor 
Initialize a new XHR object by calling `new XMLHttpRequest()`

### Methods
- `open(method, url)` - initializes a request
-  `setRequestHeaders(header, value)` - sets values of HTTP request header
- `send([data])` - sends an asynchronous request to the server
- `abort()` - cancels and active request
- `getResponseHeader(header)` - Return response's value for `header`
	- must be called after `open()` and before `send()`

### Properties
*Note that some properties have `null`, `0`, or `''` default values if the request has not completed yet* 

- `timeout`- Maximum time a request can take to complete (in milliseconds).
	- If set, make sure to add an event handler for instances when  `timeout` event fires (e.g. response takes longer than the max time we set)
- `readyState`What state the request is in (see below)
- `responseText`- Raw text of the response's body.
- `responseType` - string value specifying type of data contained within the response, which directs browser on how to parse it when it returns `response` 
	- the default is '`text`'
	- valid values: `text`, `json`, `arraybuffer`, `blob`, and `document`
- `response` - parsed content of response; data type depends on the value of `XMLHttpRequest.responseType` request header
	- 
- [`XMLHttpRequest.statusText`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/statusText) - Returns a `DOMString` containing the response string returned by the HTTP server. Unlike [`XMLHttpRequest.status`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/status), this includes the entire text of the response message ("`200 OK`", for example).

### [Types of XMLHttpRequest events](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequestEventTarget)

![Pasted image 20220221135839.png](./images/Pasted%20image%2020220221135839.png)
- `loadend` event is the last to fire; fires when response loading done and all other events have fired. 
```ad-note
title: XHR events

The browswer considers any complete request-response cycle a complete response (i.e. not a network error or timeout event) as "loaded", and will fire the `load` event. 

The browser does **not** care about the response's semantic meaning (e.g. status code 300, 400, or 500). If the response sends a status code, then the browser considers cycle complete. It is the responsibility of the app code to examine the object's XHR.status to determine if the HTTP request was successful. 

```
### XHR Request Workflow
1. Create a new `XMLHttpRequest` object.
2. Use the `open` method on the XHR object to specify the method and URL for the request.
3. Use the `setRequestHeader` method on the XHR object to set headers you'd like to send with the request. Most of the headers will be added by the browser automatically.
4. Use the `send` method on the XHR object to trigger the whole action and on POST request we can also pass serialized data as an argument.
5. Attach an event handler for the `load` event to the XHR object to handle the response.
6. Attach an event handler for the `error` event to the XHR object to handle any connection errors. This is not required but it's a good practice.>)



# Examples

**Handlers for different XHR events**
```js

  request.addEventListener('load', (e) => {
    // do something with request.response! 
  });

  request.addEventListener('error', (e) => {
    console.log('Couldnt get a response from server');
  });
  request.addEventListener('timeout', (e) => {
    console.log('Based on request.timeout, this request took too long. Please try again.');
  });
  request.addEventListener('loadend', (e) => {
    console.log('Request-response cycle is done and always fires, no matter what the response status is.');
  });
```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: 