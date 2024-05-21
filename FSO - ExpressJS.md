---
creation date: 2022-03-22 22:12
aliases: 
tags: 🖥️
---

# [FSO - ExpressJS](FSO%20-%20ExpressJS.md)
---
### Basics of Express
- define routes on our express `app` based http request methods according to [RESTful API principles](./APIs.md#REST%20and%20CRUD) or the resource-oriented API principles.
- Each route takes a callback that always receives a request and response  object
	- We can access the HTTP request parameters via `request.params`
	- To access data sent with a HTTP POST request, there are a few more steps:
		- use the `express.json()` built-in middleware to parse JSON data of a request and transform to JS object that is attached to body property of the property of the request object *before* the route handler is called.
		- In the route callback, we can  access the deserialized data in `request.body` and do something with it

### Middleware
##### Refresher - what is middleware
In Node.js and Express, middleware is a function that has access to the request and response lifecycle methods, and the next() method to continue logic in your Express server.

Middleware functions are called in order that they're used via `express.use()` method and should generally be used before routes. 
- The exception to this is an error handling middleware, that gets called if none of the other middleware/routes handle the HTTP request.
- Middleware takes  three parameters - request, response, next
- `next()` yields control to the next middleware 
- Usage: `app.use(middlewareFn)`
##### Express middleware used
1. `express.json()` parses json from HTTP requests and makes it available in `express.request.body` object
2. use morgan for logging 
###### Steps for combining frontend build with backend express into one repo
4. use `cors()` to allow cross origin resource sharing between different ports (front end running on 3000 and back end running 3001)
5. use built-in `express.static('build')` to tell express to serve static files (images, CSS, JS, our  React `index.html` and components) from the `build` folder copied over 
	1. After building React production build, copied over the `build` folder from React project root to the express backend project root.
	2. Impact: whenever express gets an HTTP GET request, it will first check if the `build` folder contains a file corresponding to the request's address 
	3. HTTP GET requests to index.html will show the React frontend! 
	4. HTTP GET requests to `/api/notes` will be handled by backend code as usual
	5. Update the baseURL in React AJAX server requests to refer relative url since React and backend are now part of same project root 
	6. We can now see the React frontend and run express backend from ONE server, localhost 3001, as a single page app!

New app design:
![Pasted image 20220323220920.png](./images/Pasted%20image%2020220323220920.png)

### Deploying to [Heroku](./Heroku.md)
After copying frontend production build to express repo:
1. Create a *.Procfile* to backend root folder to tell Heroku how to start the application: `web: npm start`
2. Change express port in the index.js file to use EITHER port 3001 (for dev) or the port defined by envrionment variable (how Heroku configured app port)
```js
// index.js of express
const PORT = process.env.PORT || 3001;
app.listen(PORT, () => {
  console.log('Listening on port' + PORT);
})
```
1. add node_modules to .gitignore
2. Push changes to local/remote repos to save changes
3.  `heroku login` to log in from CLI
4. `heroku create`
5. `git push heroku main`
6. Open the app in Heroku browser. Monitor the app logs using `heroku logs -t`
	1. debug for things like missing dependencies or forgetting to configure express to use the port set to environment variable PORT
### Streamlining deploying of frontend
Update `package.json` of express backend with npm scripts to automate the React build and copy process:

*Note: make sure to change the relative folder paths*
```js
{
  "scripts": {
    //...
    "build:ui": "rm -rf build && cd ../part2-notes/ && npm run build && cp -r build ../notes-backend",
    "deploy": "git push heroku main",
    "deploy:full": "npm run build:ui && git add . && git commit -m uibuild && git push && npm run deploy",    
    "logs:prod": "heroku logs --tail"
  }
}
```
- `build:ui` - builds React production frontend and copies version under backend repo
- `run deploy` releases current backend to heroku
- `deploy:full` updates git repo
- `logs:prod` shows heroku logs

##### Refactoring dev environments with a [proxy server](./Proxy%20Server.md)
- Since we changed the baseURL for AJAX requests when we moved the production build to the express repo, running React in *development* mode (on it's own server) breaks because it is requesting json data from a relative url on the same server (localhost:3000). 
	- To solve this, we specify a [proxy](./Proxy%20Server.md) server to the React `package.json` and direct it towards to where the express dev server would be running (ie. localhost:3001)
	- Now, when React dev makes a HTTP request to outside of React (i.e. to express routes), it will direct the requests to localhost:3001 instead of localhost:3000 

Still with the npm scripts, the frontend deployment pipeline is complex. The course dives into creating an automated deployment pipeline in part 11.


### Development Utilities
Helper Packages
- install  [nodemon](./nodemon.md)  as a dev dependency to watch files in the project directory and automatically restart the node application 
- install [Morgan Package](./Morgan%20Package.md) to log messages to the console based on "tiny" configuration
- install node's `cors` middleware package to be able to configure [Cross-Origin Resource Sharing](./Cross-Origin%20Resource%20Sharing.md) rules in express
- 
# Examples
Starter Code
- [ExpressJS](./notes/ExpressJS.md) handles setting the Content-Type header with the appropriate values (text/plain and application/json) for both of the `get` routes
- `response.json` method serializes the data before sending
```js
const express = require('express')
const app = express()

let notes = [
  {
    id: 1,
    content: "HTML is easy",
    date: "2022-05-30T17:30:31.098Z",
    important: true
  },
//...
app.get('/', (request, response) => {
  response.send('<h1>Hello World!</h1>')
})

app.get('/api/notes', (request, response) => {
  response.json(notes)
})

const PORT = 3001
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`)
})
```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: 