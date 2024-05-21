---
creation date: 2022-02-28 11:06
aliases: 
tags: 🖥️
---

# [Fetch](Fetch.md)
---
Fetch API is a [Web API](Web%20API.md) like [XMLHttpRequest](./XMLHttpRequest.md) to make [AJAX](./AJAX.md) network requests, but uses the [Promise](./Promises.md) syntax to for a simpler interface. 


Key differences between [jQuery](./jQuery.md) Ajax and Fetch:
- The Promise that `fetch()` returns won't reject if the response status is an HTTP error status code (i.e. response codes in the 4xx and 5xx ranges). 
	- Use the `Response.ok` or `Response.status` properties in handler to check for these. 
- By default, `fetch() `won't send cookies. In order to send cookies with the request, the credentials parameter must be set to either `include`, or `same-origin`. The latter option sends credentials only if the request URL is on the same origin as the calling script.

## Using Fetch
Fetch ==returns a promise that resolves to a *Response object*,== which represents the ==entire HTTP response==, not the response body. 

`Response.json()` method reads the `Response` stream and *returns a second promise* that resolves with the result of parsing the response body text as JSON, e.g. data as a JavaScript object. 
```js
fetch(
    '/my-blog-post',
    { method: 'GET' }
  ).then((response) => response.json()) // promise resolves to Response object that needs to be read and parsed
   . then(data => {
	// data is a JS object (POJO, array, string, number etc.)
    // do something with the data 
  });
```

### Request options
- Required: `resource` that is either a string URL or a `Request` object.
- Optional: `init` object that contains custom settings for the request. Common settings:
	- `method`
	- `headers` - passed in as a object literal with string values or as a `Headers` object
	- `body` if sending a POST or PUT request
	- `mode` - `cors`, `no-cors`, `same-origin`
	- `credentials` controls what browsers do with credentials (e.g. cookies, HTTP auth entries, TLS client certificates)

🚨 [Check out common reasons for `TypeError`](https://developer.mozilla.org/en-US/docs/Web/API/fetch#exceptions)

Example POST method implementation with [Async Await](./Async%20Await.md):
```js
// Default options are marked with *
async function postData(url = '', data = {}) {
  const response = await fetch(url, {
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    credentials: 'same-origin', // include, *same-origin, omit
    headers: {
      'Content-Type': 'application/json'
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
    redirect: 'follow', // manual, *follow, error
    body: JSON.stringify(data) // body data type must match "Content-Type" header
  });
  return response.json(); // parses JSON response into native JavaScript objects
}

postData('https://example.com/answer', { answer: 42 })
  .then(data => {
    console.log(data); // JSON data parsed by `data.json()` call
  });
```
### Response objects
Reponses objects are returned when fetch promises resolve. Remember, fetch promise only rejects when there is a network error (usually a permissions issue) and does not reject on HTTP errors (404 etc). 

Useful properties:
- `Response.status` - integer value of response status code
- `Response.statusText`
- `Response.ok` - boolean value if response status is in range 200-299 inclusive
- `Response.json`
- `Response.text()` - reads a Response stream and returns a promise that resolves with a String, decoded using UTF-8

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference: [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

Related: [Data Serialization](./Data%20Serialization.md)