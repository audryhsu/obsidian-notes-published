---
creation date: 2022-02-21 11:38
aliases: 
tags: 🖥️
---

# [AJAX](AJAX.md)
---
**AJAX** stands for Asynchronous [JavaScript](./JavaScript.md) And XML, popular for ability to fetch data ([HTML](./HTML.md) or XML) and update *parts of a page*. This solves for ==creating more interactive web interfaces without having to perform full page reloads==.

**Benefits of AJAX:**
1. Instead of browser initiating a HTTP request for entirely updated HTML page, JavaScript code initiates a HTTP request (using a browser API like [XMLHttpRequest](./XMLHttpRequest.md)) asynchronously
2. Our JavaScript defined event listener listens for the AJAX response 'loaded' event
3. When the browser receives a response, the JavaScript code *optionally* takes the response's body (piece of HTML or JSON) and updates the section of the page as needed

🔑 Server returns a *chunk of HTML code* that we can use to dynamically manipulate the page 

Single page applications are increasingly common, working with just one HTML page and use JS to fetch and pass [JSON](./JSON.md) data to and from server to update page elements.

>The examples below  focus on making AJAX requests with [XMLHttpRequest](./XMLHttpRequest.md), however there are other APIs and libraries that can make AJAX requests, such as [Fetch](./Fetch.md) and [[jQuery#AJAX|| jQuery's `.ajax()` method]].

## [XMLHttpRequest](./XMLHttpRequest.md) object
![XMLHttpRequest](./XMLHttpRequest.md#^9bd8e7)
**Using JS to send a HTTP GET request using XHR**
```js
let request = new XMLHttpRequest(); // Instantiate new XMLHttpRequest object
request.open('GET', '/path');  // Set HTTP method and URL on request
request.send();   // Send request asynchronously...

request.addEventListener('load', event => {
  let request = event.target;  // the XMLHttpRequest object

  request.responseText;   // body of response
  request.status; // status code
  request.statusText; // status text from response
  request.getResponseHeader('Content-Type');//response header
});
```
##### Basic Workflow
- [Instantiate](./XMLHttpRequest.md#Constructor) a XHR object as `request` to send a HTTP request with JavaScript.
- open a connection to URL based on request method
- attach a `load` event listener on the XHR object that listens for when the response is loaded
- Send the request with optional data (if a POST or PUT request) 
- ⚡`loadstart` event fires when the request sent to server
- ⚡`load` event is fired after the XHR transaction completes and complete response is loaded 
	- If response is unsuccessful (as in, request-response cycle did not complete), a different type of event may fire (e.g. `abort`, `error`, `timeout`).
- [event] triggers the listener callback that takes the event object (the `XMLHttpRequest` object)
- Handle the response (e.g. inspect the response properties)

>[🔍XHR object methods](./XMLHttpRequest.md#Methods) and [properties](./XMLHttpRequest.md#Properties)
>[🔍XHR Event Types](./XMLHttpRequest.md#Types%20of%20XMLHttpRequest%20events%20https%20developer%20mozilla%20org%20en-US%20docs%20Web%20API%20XMLHttpRequestEventTarget)

 ### Sending Requests with XHR 
- We can load [|form data](FormData.md) and JSON data
	- To load multipart form data, encapsulate `<form>` element in a [|form data](./FormData.md) object
	- To load JSON data, use `JSON.parse()` 
- We can send POST requests using JSON
	- To send JSON, we can use the JSON API to stringify JavaScript object
- We can send Cross-Domain XHR requests by leveraging CORS ([Cross-Origin Resource Sharing](./Cross-Origin%20Resource%20Sharing.md))
	- The [Cross-Origin Resource Sharing](./Cross-Origin%20Resource%20Sharing.md) specification fulfills the need for legitimate cross-origin requests. It gives us a standard way to access resources from different origins without the security problems associated with cross-origin requests.
	- Client browser automatically includes the request origin URI in the `Origin` request header
	- The server response *must* include the matching URI (or allow all `*`) in the `Access-Control-Allow-Origin` response header or else the browser will not accept the response

### Optimizing AJAX Requests
It's common to use the  [Throttling](./Throttling.md) technique to minimize the number of asynchronous requests sent to the server by setting a specified delay. 

### Which API /library should I use?
There are many different options for making Ajax requests: XMLHttpRequest, fetch(), and [jQuery](./jQuery.md)'s ajax().

Each method has pros and cons.
- XHR requests may be sufficient for simple Ajax that do not need to handle lots of conditionals 
- Promise-based syntax used by [Fetch](./Fetch.md) simplifies the interface and is considered relatively new, so support for older browsers may be a concern 
- If the codebase already uses a library like [jQuery](./jQuery.md), then using [jQuery](./jQuery.md) Ajax for Ajax requests could make sense. For newer projects, there may not be a need to introduce [jQuery](./jQuery.md). 
# Examples
###### [Loading Form Data via XHR](./Loading%20Form%20Data%20via%20XHR.md)
![Loading Form Data via XHR](./Loading%20Form%20Data%20via%20XHR.md#^2bba46)

###### [Loading JSON via XHR](./Loading%20JSON%20via%20XHR.md)
![Loading JSON via XHR](./Loading%20JSON%20via%20XHR.md#^12a5bf)
###### [Sending JSON via XHR](./Sending%20JSON%20via%20XHR.md)
![Sending JSON via XHR](./Sending%20JSON%20via%20XHR.md#^444709)

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference: [XMLHttpRequest API](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)

Related: 


