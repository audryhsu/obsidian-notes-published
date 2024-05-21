## Workflow
1. serialize data into JSON
2. send request using XHR object with the correct content type header
3. handle the response

# Example1
```js
let request = new XMLHttpRequest();
let url = 'https://ls-230-book-catalog.herokuapp.com/books'
request.open('POST', url)

// set content-type header
request.setRequestHeader('Content-Type', 'application/json; charset=utf-8')

// convert an object into json string
let data = {title: 'Eloquent JavaScript', author: 'Marijn Haverbeke'}
let json = JSON.stringify(data)
```
Illustrative HTTP Request based on above code:
```http
POST /books HTTP/1.1
Host: https://ls-230-book-catalog.herokuapp.com
Content-Type: application/json; charset=utf-8
Accept: *

{"title": "Eloquent JavaScript", "author": "Marijn Haverbeke"}

```

Creating a new product to the web store
```js
let product = {name: 'visible ink', sku: 'abc56', price: 15}

createProduct(product);

function createProduct(productObject) {
  let url = 'https://ls-230-web-store-demo.herokuapp.com/v1/products'
  let json = JSON.stringify(productObject);

  let request = new XMLHttpRequest()

  request.open('POST', url)
  request.setRequestHeader('Content-Type', 'application/json; charset=utf-8')
  request.setRequestHeader('Authorization', 'token AUTH_TOKEN')

  request.addEventListener('load', (e) => {
    console.log(request.status);
    console.log(request.responseText);
  });

  request.addEventListener('error', (e) => {
    console.log("Error occurred, couldn't load response");
  });

  request.send(json)
}
```

^444709

