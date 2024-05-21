---
creation date: 2022-03-03 08:31
aliases: 
tags: 🖥️
---

# [FormData](FormData.md)
---
FormData is a [Web API](Web%20API.md) interface that provides an easy way to construct key/value pairs that represent form fields and their values. It uses "multipart/form-data" encoding. 

Once created, the FormData object can be used to send [AJAX](./AJAX.md) request via [XMLHttpRequest](./XMLHttpRequest.md) or [jQuery](./jQuery.md), or JSONified. 

 ## Syntax 
 The constructor function takes a HTML form element. The FormData object will be populated with the form's current keys/values using the `name` property of each form input element and encode 
```js
new FormData(form)
```

>Only successful form controls are included in a FormData object, i.e. those with a name, not disabled and checked (radio buttons and checkboxes) or selected (one or more options within a select).


We can iterate over a FormData object using `for...of FormData object` which will loop over `[inputName, value]` pairs. 

```js
let formData = new FormData(form)

for (let pair of formData) {
	pair // [name, value]
}
// equivalent to
for (let pair of formData.entries())
//...
```
##### Convert to JSON
```js
let formData = new FormData(form)
let data = {}

for (let pair of formData) {
	data[pair[0]] = pair[1]
}

let json = JSON.stringify(data)

//send xhr request
xhr.open('POST', form.action);
    xhr.setRequestHeader('Content-Type', 'application/json');

xhr.send(json);
```

# Examples
```js

```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: [HTML](./HTML.md) - [XMLHttpRequest](./XMLHttpRequest.md) - [AJAX](./AJAX.md) 