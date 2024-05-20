---
creation date: 2021-12-28 09:39
aliases: 
tags:
  - 🖥️
publish: true
---

# [ExpressJS](ExpressJS.md)
---
Express is the most popular Node web framework. It provides mechanisms to write route handlers for HTTP requests, integrate with "view" rendering engines via templates, set web app settings for connections and ports, and integrate middleware processing at any point in the request handling pipeline.

Express is unopinionated and fairly minimal, which makes it extremely flexible as there's tons of middleware that is compatible with express to solve almost any web development problem (e.g. sessions, cookies, security, etc).
### Application
The [app](https://expressjs.com/en/api.html#app) object has methods for
- Routing HTTP requests; see for example, app.METHOD and app.param.
- Configuring middleware; see `app.route`.
- Rendering HTML views; see `app.render`.
- Registering a template engine; see `app.engine`.
- Mounting middleware functions at a specified path; see `app.use`
- Bind and listen for connections on a specific host and port; `app.listen`

### Request
The [req object](https://expressjs.com/en/api.html#req)  represents the HTTP request.
Properties:
- `req.params` - object containing named parameters
- `req.query` - object containing a property for each query string parameter 
- `req.headers` - inspect headers
- `req.body` - key-value pairs of data submitted in the request body
### Response
The [res object](https://expressjs.com/en/api.html#res) is HTTP response that Express app sends


Use any of the [Response methods](ExpressJS.md#Response%20methods) to send the response object and end the cycle. 

### Router (aka controller)
🔗[Routing Guide](https://expressjs.com/en/guide/routing.html)
The `express.Router` object allows you to group route handlers for a particular part of a site together and access them using a common route-prefix (e.g. /wiki/ or /notes/). Router functions are middleware, so they must handle the request and/or call `next` middleware.

By modularizing a group of routes to a `express.Router`, it makes maintaining code much easier, especially in larger applications

![express-routing 2022-09-29 11.00.30.excalidraw](../images/express-routing%202022-09-29%2011.00.30.svg)

For example, we can create a Wiki route module in `wiki.js` that handles all HTTP requests to `/wiki`. We can add routes to the `express.Router` object just like we can to the express app object 
```js
// wiki.js - Wiki route module
const express = require('express');
const router = express.Router();

// Home page route
router.get('/', function(req, res) {
  res.send('Wiki home page');
});

// About page route
router.get('/about', function(req, res) {
  res.send('About this wiki');
});

module.exports = router;
```
In our main app, we can take the wiki router into use. Now all requests to `/wiki` will be handled by the `express.Router` middleware defined in `wiki.js`
```js
// app.js file
const wiki = require('./wiki.js');
// ...
app.use('/wiki', wiki);

```
-------- below are notes taken from LS curriculum walk through.
## Primer on ExpressJS
1. Instantiate an express application instance, or  `app` object.
- properties include local app variables
- methods including making http `get` and `post` requests
2. middleware - functions that have access to `req` and `res` objects, process the data (app logic), and direct the app to the `next` middleware function, or end the request/response cycle
	1. app-level middleware - bound to the `app` object --> app.use
	2. router-level middleware - bound to instance of ``express.Router``, or router object (not used in LS walk through)
	3. error-handling middleware - requires four arguments to include the err, and is the last middleware
	4. built-in middleware - `express.static` to serve static asset files
		1. `express.json` parses JSON requests
		2. `express.urlencoded` parses URL encoded requests
	5. third-party middleware

- requests `req` and responses `res` are objects passed between middleware functions
	- each `req/res` objects can store information in their properties about the request or response. We can use  
	- `req` object has properties to parse the client request and http headers
	- `res` object facilitates data transfer and has [Response methods](ExpressJS.md#Response%20methods) for detailing the response back, such as setting status codes, appending the response with more information, sending information, redirecting, rendering, or ending

#### app.locals vs res.locals
- l`app.locals` is a property on the `app` object that stores local variables within the ExpressJS application (app) and *persist through the lifetime of the application*
	- these local variables are available in middleware via `req.app.locals`
	- holds **application-level information**
-` res.locals` properties only valid for *lifetime of the request*
	- only available to views rendered during that request/response cycle, if any.
	- useful for exposing **request-level information**

#### Starter code for app.js 
Follow along LS lesson
```js
const express = require("express");
const app = express();

app.set("views", "./views");
app.set("view engine", "pug");

app.get("/", (req, res) => {
  res.render("hello-world-english");
});

app.listen(3000, "localhost", () => {
  console.log("Listening to port 3000.");
});
```
- create a HTTP server named `app`
- define a route for GET requests to the root path
	- `app.METHOD(PATH, HANDLER)`
		- app is an instance of `express`
		- method is an http request method
		- path is a path on the server
		- handler is the function executed when the route is matched (can be one or more)
- set the server to listen on port 3000 for requests from localhost

**Incorporating Pug template engine**
- `app.set` trells express to use the pug engine and where to look for view templates
- `res.render(TEMPLATE_NAME, [DATA_OBJECT])` - renders the view template .pug file by converting it to HTML and sending it to the client
	- optionally takes an object and creates variable in Pug view whose names and values match the property name/value of obj 

**Static files**

app.js
```js
// middleware function to use public directory for static files
app.use(express.static("public"))
```
- `express.static` is a function that returns a middleware that handles requests for static assets in the `public` folder.
	- express will call function **each time** it receives an http request and return requested asset whenver path includes one of the subdirs in public since it is placed above route handlers
	- 

**Redirect routes**
```js
app.get("/", (req, res) => {
  res.redirect("/english");
});
app.get("/english", (req, res) => {
  res.render("hello-world-english");
});
```

#### View variables
```ad-tldr
title: Variables vs Helpers

Use view helpers when there are 2+ views that need the same helper, otherwise keep the logic as part of app and pass the value as a variable.

```

```js
/// view helper functions defined as method on `app.locals` object
app.locals.currentPathClass = (path, currentPath) => {
  return path === currentPath ? "current" : "";
};

app.get("/english", (req, res) => {
  res.render("hello-world-english");
  currentPath: req.path,
});

// ... rest of routes
```
- the `currentPathClass` function will dynamically set class value in layout.pug based on if the URL path matches the route path.

```ad-tip
title: Storing request/response variables

All views can access variables and functions defined as properties of `app.locals` and `res.locals` objects. Add view helpers as properties to `app.locals` (only set once); 
Use `res.locals` properties for passing variables between middleware functions. It's usually better to define variables as second arg passed to `res.render` to store variables during current request/response cycle.

```
#### Route parameters
Routes can match a parameterized pattern (e.g. path pattern can include placeholders) to make DRYer code.

Parameterized route:
```js
// define routes
app.get("/:language", (req, res, next) => {
  const language = req.params.language;
  const languageCode = LANGUAGE_CODES[language];
  if (languageCode) {
    res.render(`hello-world-${language}`, {
      countries: COUNTRY_DATA,
      currentPath: req.path,
      language: languageCode,
    })
  } else {
    next(new Error(`Language not supports: ${language}`))
  }
})
```
- note that the ***first matching*** route for a particular method gets called
- use `req.params` to access a parameter (property name is same as parameter name)
- check to see if there is a code for the language parameter passed in
	- if there is, render the proper view and pass in corresponding `COUNTRY_DATA`
	- if not, raise an error
- pass the error obj to the `next` function
	- `next` represents the next middleware function in the app passed as an argument 

## Middleware
Express middleware functions are callback functions used by Express methods like `app.use` and `app.get` to access and manipulate request and response objects.

**Parts of middleware**
```
(req, res, next) => {
  // body of middleware
}
```
- `req` and `res` parameters are response and request objects
- `next` - callback argument to the middleware function, called "next" by convention

**How to use middleware**
- Middleware must either generate a HTTP response (e.g. call `render`, `send` or `redirect`) or tell Express to execute the next middleware (e.g. call `next`).
- use `app.use` for functions that must run for *every* HTTP request or each request for specified path
	- middleware functions can be passed as separate args, or as part of an array 
- use `app.get/post` for functions that apply to one particular path and method combo
	- - Route callbacks are middleware but we don't have to call `next`. 

> [!NOTE] Middleware order
> 
> The order of middleware loading is important: middleware functions that are loaded first are also executed first. If the current middleware function does not end the request-response cycle, it must call `next()` to pass control to the next middleware function. Otherwise, the request will be left hanging.
> 

**Example of Middleware sequence**
```js
app.use((req, res, next) => {      // Middleware #1 called first
  // do something
  if (somethingIsTrue) {
    res.render("foo");
  } else {
    next();
  }
});

app.use((req, res, next) => {      // Middleware #2 calls next #3
  // do something else
  next();
});

app.get("/stuff", (req, res) => {  // Middleware #3 only called if #1 and #2 call next
  req.render("qux");
});
```

**Error handler Middleware**
```js
// error handler must be last middleware in app.js!!! 
app.use((err, req, res, _next) => {
  console.log(err);
  res.status(404).send(err.message)
})
```
-  error handler accepts four arguments (even if you don't use them all) where `err` is the error object

**Passing data between Middleware**
Use `res.locals` object to define properties to pass data between middleware functions
- `res.locals` lifespan is limited to current req/res cycle
```js
app.get(
  "/foo",
  (req, res, next) => {
    res.locals.id = getNextIdNumber();
    res.locals.name = req.body.name.trim().toUpperCase();
    res.locals.fooData = [1, 2, 3];
    next();
  },
  (req, res) => {
    res.render("bar", {
      id: res.locals.id,
      name: res.locals.name,
    });
  },
);
```

## Sessions and Persistence
- `express-session` module generates unique session id when client browser makes an initial HTTP request to app and sends the session id to the client in a cookie
- client browser sends the cookie back to server for each request for the app to look up stored session data
-  `session-store.db` is created in project dir by `connect-loki` module, a noSQL db

Set up sessions - *express-session middleware calls session function to define cookie property*
```js
app.use(session({
  cookie: {
    httpOnly: true,
    maxAge: 31 * 24 * 60 * 60 * 1000, // 31 days in milliseconds
    path: "/",
    secure: false,
  },
  name: "launch-school-contacts-manager-session-id",
  resave: false,
  saveUninitialized: true,
  secret: "this is not very secure",
  store: new LokiStore({}),
}));
```

Persistence - *Move session data to session store by creating and using properties on `req.session`*
```js
// Middleware checks for contact data in req.session to re-use or initializes contactData with starter contactData object

app.use((req, res, next) => {
  if (!("contactData" in req.session)) {
    req.session.contactData = clone(contactData);
  }

  next();
});
```

### express-sessions
- the `session` middleware defines our session id and cookie ifnromation as well as our session store instance. In our example code, we are using a `LokiStore` object. 
- after we add the session middleware, all requests to the app are now using sessions.
- **the session is attached to the request**, which is why you can access it through `req.session`, which stores session data serialized as JSON 
- Remember, session id is stored in a cookie, but the related session data is stored server-side. the client receives their session id in a cookie and sends it with **every http request** (hence `req.session`!)


******

# Utilities
[nodemon](../nodemon.md) package allows automatic reloading
```js
$ npm install nodemon --save-dev
```
 - add it as a `start` script in package.json to shortcut running your app.js file with nodemon


**Logging using [Morgan Package](../Morgan%20Package.md)**
```js
//app.js
// middleware functions
app.use(morgan("common"))
```
- uses "common" format to output log information standard in Apache web server

**express-validator** simplifies validation and sanitization of user inputs 
```js
const { body, validationResult } = require("express-validator")


app.post("/items",
  // Validation chains
  [
    body("name").trim().isLength({ min: 1, max: 40 }),
    body("age").isInt({ min: 18, max: 120 }).toInt(),
  ],
  // Error handling middleware
  (req, res, next) => {
    let errors = validationResult(req);
    if (!errors.isEmpty()) {
      res.render("...", {
        name: req.body.name,
        age: req.body.age,
        errorMessages: errors.array().map(error => error.msg),
      });
    } else {
      next();
    }
  },
  // Main route callback
  (req, res) => {
    // do something here
    res.redirect("...");
  }
);
```
- use the **validation chain API**, a middleware in route callbacks, to pass validation chains to `app.post` or `app.get` as an array argument
- A combination of independent functions and arrays of functions can handle a route, which is why we can pass an array of validations as the second argument to `app.post` and then two more independent callback functions


##### Response methods
The methods on the response object (`res`) in the following table can send a response to the client, and terminate the request-response cycle. *If none of these methods are called from a route handler, the client request will be left hanging.*

| Method | Description |
| ------ | ----------- |
|[res.download()](https://expressjs.com/en/4x/api.html#res.download) | Prompt a file to be downloaded.|
|[res.end()](https://expressjs.com/en/4x/api.html#res.end)|End the response process.|
|[res.json()](https://expressjs.com/en/4x/api.html#res.json)| Send a JSON response. |
|[res.jsonp()](https://expressjs.com/en/4x/api.html#res.jsonp)|Send a JSON response with JSONP support.|
|[res.redirect()](https://expressjs.com/en/4x/api.html#res.redirect)|Redirect a request.|
|[res.render()](https://expressjs.com/en/4x/api.html#res.render)|Render a view template.|
|[res.send()](https://expressjs.com/en/4x/api.html#res.send)|Send a response of various types.| 
|[res.sendFile()](https://expressjs.com/en/4x/api.html#res.sendFile)|Send a file as an octet stream.|
|[res.sendStatus()](https://expressjs.com/en/4x/api.html#res.sendStatus)|Set the response status code and send its string representation as the response body.|


---
Tags: [Programming](Programming.md) - [JavaScript](../JavaScript.md) - [Web Apps](Web%20Apps.md) - [Backend Development](Backend%20Development.md) 

Reference: [List of popular middleware to use with Express](https://expressjs.com/en/resources/middleware.html)

Related: 