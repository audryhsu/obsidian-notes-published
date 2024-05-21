---
creation date: 2022-02-21 18:58
aliases: 
tags: 🖥️
---

###### [Loading JSON via XHR](Loading%20JSON%20via%20XHR.md)
```js
let request = new XMLHttpRequest();
request.open('GET', 'https://api.github.com/repos/rails/rails');
request.responseType = 'json';

request.addEventListener('load', event => {
  // request.response will be the result of parsing the JSON response body
  // or null if the body couldn't be parsed or another error
  // occurred.

  let data = request.response; // returns a JavaScript object!
});

request.send();
```

^f26e6e

- By specifying the `responseType` in the request header, the browser will parse the JSON for us and return a JavaScript object
- If the server returns data that is not compatible with what the `responseType` was et, the value of `response` will be `null`, something we can simply check for and can avoid extra error-handling code (see below)
- Otherwise, we need code to parse the response ourselves using `JSON.parse(request.response)`

Boiler plate code avoided:
```js
let request = new XMLHttpRequest();
request.open('GET', 'http://ls-230-book-catalog.herokuapp.com/invalid_book');

request.addEventListener('load', event => {
  try {
    let data = JSON.parse(request.response); // extra code to parse JSON string into JavaScript object
    // do something with the data
  } catch(e) {
    console.log('Cannot parse the received response as JSON.')
  }
});

request.send();
```


**Add an error handling event listener** - See XHR types of [ events](XMLHttpRequest.md#Types%20of%20XMLHttpRequest%20events%20https%20developer%20mozilla%20org%20en-US%20docs%20Web%20API%20XMLHttpRequestEventTarget%20)
```js
let request = new XMLHttpRequest();
request.open('GET', 'htps://api.github.com/repos/rails/rails');

request.addEventListener('load', event => {
  let data = JSON.parse(request.response);
  console.log(request.status);
  console.log(data.open_issues);
});

request.addEventListener('error', event => {
  console.log('The request could not be completed!');
});

request.send();
```

^12a5bf

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: [AJAX](./AJAX.md) -[XMLHttpRequest](./XMLHttpRequest.md)