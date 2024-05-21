---
creation date: 2022-02-21 14:17
aliases: 
tags: 🖥️
---

# [Data Serialization](Data%20Serialization.md)
---
JS applications in the browser must serialize data when communicating with remote systems. Data serialization lets client/browser and server transfer data in a way that preserve the information without interfering with communication protocol, as long as both parties know how to read and write the serialization format.

Common media types are HTML, XML, and [JSON](./JSON.md). 

##### Query string / URL encoding
Recall that query strings (name=value pairs) must be URL encoded to be used ina valid URL. 
- we can use the built-in JS function `encodeURIComponent` to encode a name or value using URL encoding, and append the encoded query string as part of a HTTP request path.

Illustration:
```rb
# without encodeURIComponent
title=Do Androids Dream of Electric Sheep?&year=1968

# with encodeURIComponent
title=Do%20Androids%20Dream%20of%20Electric%20Sheep%3F&year=1968
# appended query string to request path
GET /path?title=Do%20Androids%20Dream%20of%20Electric%20Sheep%3F&year=1968 HTTP/1.1
Host: example.test
Accept: */*
```

To serialize [FormData](./FormData.md)  parameters into a query string as part of a POST request, we can leverage the  [URLSearchParams](URLSearchParams.md) interface. 
```js
let form = document.querySelector('form')
let params = new URLSearchParams(new FormData(form))
//params.toString() -->  photo_id=1&name=audry&email=dummmy%40a&body=test+comment

fetch(href, {
  method: method,
  headers: { "Content-Type": 'application/x-www-form-urlencoded'},
  body: params // 
})
//...rest of fetch handlers
```
- the `URLSearchParams` constructor takes a string literal of name-value pairs or any object with an iterator that produces string pairs (such as `FormData` object)
- We *must* set the Content-Type header 
- We can use the `.set(name, value)` to reassign a parameter name's values
- Note: URLSearchParams *will not parse full URLS*, just the query string portion. 
- 

We can also use the [spread syntax](./JavaScript%20ES6%20Features.md#spread%20syntax) to pass in an expanded iterable into `URLSearchParams` constructor
```js
let formData = new FormData(form)

fetch(href, {
  method: method,
  headers: { "Content-Type": 'application/x-www-form-urlencoded'},
  body: new URLSearchParams([...formData])
})
//...rest of fetch handlers

```
[🔗 URLSearchParams MDN](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)

##### Multi-part forms
POST requests use multipart form format for forms that include file uploads or `[[FormData]]` objects to collect data. This doesn't use encoding, but uses boundary delimiters defined in the `Content-Type` request header to separate name-value pairs of form data.
```http
POST /path HTTP/1.1
Host: example.test
Content-Length: 267
Content-Type: multipart/form-data; boundary=----WebKitFormBoundarywDbHM6i57QWyAWro
Accept: */*

------WebKitFormBoundarywDbHM6i57QWyAWro
Content-Disposition: form-data; name="title"

Do Androids Dream of Electric Sheep?
------WebKitFormBoundarywDbHM6i57QWyAWro
Content-Disposition: form-data; name="year"

1968
------WebKitFormBoundarywDbHM6i57QWyAWro--
```

##### [JSON](./JSON.md) serialization
Used to exchange data in arrays, objects, strings, numbers, and booleans over the network. Note that complex data types like Dates aren't supported and must be represented in one of the previous data types.

Be sure to use the `application/json; charset=utf-8` value in `Content-Type` header to ensure server parses the request correctly.
```http
POST /path HTTP/1.1
Host: example.test
Content-Length: 62
Content-Type: application/json; charset=utf-8
Accept: */*

{"title":"Do Androids Dream of Electric Sheep?","year":"1968"}
```


**Serializing a FormData object using Object.fromEntries**
```js
let form = e.target.closest('form')
let action = form.getAttribute('action');
let formData = new FormData(form)

let response = await fetch(action, {
  method: 'POST',
  headers: {'Content-Type': 'application/json'},
  body: JSON.stringify(Object.fromEntries(formData))
})
let data = await response.json()
```

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) - [AJAX](./AJAX.md)

Reference:

Related: [HTTP](./HTTP.md) - [XMLHttpRequest](./XMLHttpRequest.md) - [Fetch](./Fetch.md)
