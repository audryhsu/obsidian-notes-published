---
creation date: 2022-03-24 09:45
aliases: 
tags: 🖥️
---

# [Axios](Axios.md)
---
Promise based HTTP client for the browser and node.js

### Error handling
To access the response detail for error handling with `catch` , `response` is accessed through `error` object. 

To access a custom error message sent from the server,  access `error.response.data` 
```js
axios.get('/user/12345')
  .catch((error) => {
    if (error.response) {
      // The request was made and the server responded with a status code that falls out of the range of 2xx
      console.log(error.response.data);
      console.log(error.response.status);
      console.log(error.response.headers);
    } else if (error.request) {
      // The request was made but no response was received
      // `error.request` is an instance of XMLHttpRequest in the browser and an instance of
      // http.ClientRequest in node.js
      console.log(error.request);
    } else {
      // Something happened in setting up the request that triggered an Error
      console.log('Error', error.message);
    }
    console.log(error.config);
  });
```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference: [Docs](https://github.com/axios/axios#handling-errors)

Related: 