---
creation date: 2022-02-22 15:39
aliases: 
tags: 🖥️
---

# [Throttling](Throttling.md)
---
Throttling allows developers to change network speed to emulate different network speed conditions. 

We can use throttling to set short periods of delays to reduce the number of unnecessary  requests that are sent to the server. This technique waits for some specified time before sending a request to the server. If in the interim, the request becomes irrelevant due to a newer request, the original request is discarded and the delay period is reset. 

Many JS libraries provide this capability using a `debounce` function that will invoke a callback after a delay, which optimizes [AJAX](./AJAX.md) requests. 

Other use cases include optimizing the calling frequency of any event handler that gets called too often, like scrolling and mouse movements. 


# Examples
Custom debounce function
```js
// takes a callback func and a delay in ms as arguments
// returns a new function that calls the callback fn after delay elapses.
debounce (callback, delay) => {
  let timeout;
  return function (...args) {
    if (timeout) clearTimeout(timeout)

    timeout = setTimeout(() => callback.apply(null, args), delay);
  };
};
```

Implementing debounce from LS 230 Autocompletion walk through
```js
// return a new version of valueChanged method that delays the fetchMatches request only after 300 ms from when the user last changed the input value 
this.valueChanged = debounce(this.valueChanged.bind(this), 300)

// either resets Autocomplete object or fetches matches
  valueChanged() {
    let value = this.input.value;
    this.previousValue = value;

    if (value.length > 0) {
      this.fetchMatches(value, matches => {
        this.visible = true;
        this.matches = matches;
        this.bestMatchIndex = 0;
        this.selectedIndex = null;
        this.draw();
      });
    } else {
      this.reset();
    }
  }
  // sends AJAX request for matching countries based on query text
  fetchMatches(query, callback) {
    let request = new XMLHttpRequest();

    request.addEventListener('load', () => {
      callback(request.response);
    });

    request.open('GET', `${this.url}${encodeURIComponent(query)}`);
    request.responseType = 'json';
    request.send();
  }
```


---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: 