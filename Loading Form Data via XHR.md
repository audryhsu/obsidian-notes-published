---
creation date: 2022-02-21 18:31
aliases: 
tags: 🖥️
---

###### [Loading Form Data via XHR](Loading%20Form%20Data%20via%20XHR.md)
```js
document.addEventListener("DOMContentLoaded", () => {
  let store = document.getElementById("store");
//...
  store.addEventListener('submit', (e) => {
    event.preventDefault(); // prevent the form from submitting
    let form = e.target; // form is what triggered the event
	
// dynamically get path for product resource
    let productid = form.getAttribute('action'); 
    let data = new FormData(form); // serialize form data

    request = new XMLHttpRequest();
    request.open(
      "POST",
      "https://ls-230-web-store-demo.herokuapp.com" + productid
    );

	// add token to Authorization request header
    request.setRequestHeader('Authorization', 'token AUTH_TOKEN'); 
	// update store div with response
    request.addEventListener("load", e => store.innerHTML = request.response); 

    request.send(data);
  });
});
```

^2bba46

 https://codepen.io/launchschool/pen/ybzadx/587b47206111d4def64bb419de8c47b3?editors=0011>)

###### Send POST request parameters with URL encoding
- URL-encode POST parameters must include `Content-Type: application/x-www-form-urlencoded`
```js
let request = new XMLHttpRequest();
request.open('POST', 'https://ls-230-book-catalog.herokuapp.com/books');

request.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

let data = 'title=Effective%20JavaScript&author=David%20Herman';

request.addEventListener('load', () => {
  if (request.status === 201) {
    console.log(`This book was added to the catalog: ${request.responseText}`);
  }
});

request.send(data);
```

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: [AJAX](./AJAX.md) - [XMLHttpRequest](./XMLHttpRequest.md)