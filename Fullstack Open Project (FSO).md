---
creation date: 2022-03-17 11:55
aliases: 
tags: 🖥️
---

# [Fullstack Open Project (FSO)](Fullstack%20Open%20Project%20(FSO).md)
---
*Note: [Capstone Prep](./Capstone%20Prep.md) required parts 1 - 7*

**Goal:** Building a single page applications with [React](./React.md) that use [REST APIs](./APIs.md#REST%20and%20CRUD) built with [NodeJS](./notes/NodeJS.md). The course also contains a section on [GraphQL](./GraphQL.md), a modern alternative to REST APIs.

Description:
- Introduction to [React](./React.md)
- use of [MongoDB for](MongoDB%20for.md) storing the application’s data.
- Programming a server with [NodeJS](./notes/NodeJS.md) and [ExpressJS](./notes/ExpressJS.md) 
- testing ExpressJS servers and user administration
- testing React apps
- state management with [Redux](./Redux.md)
- React router, custom hooks, and styling with [CSS](./CSS.md) and [webpack](webpack.md)

### Key learnings 
#### Part 1 - Intro to React, Component State, Event handlers
[Full React Notes](./React.md)
- hooks are a recent addition to React and allow us to avoid using class syntax to maintain state
- state is managed by the [useState hook](./React.md#useState%20hook), which returns a pair of variables (some *state* variable, and a  *setState* function by convention)
- state values should *never* be mutated, only copied or replaced using the *setState* functions
- *lifting up state* philosophy dictates that we should keep state in the highest common ancestor and pass state to sub-components as needed as props
#### Part 2 - Modules, Forms, and Making XHR Requests
- Rendering collections, modules
	- By convention, component modules should reside in the `src/components` folder
		- we can import components like JS modules
	- Components are Uppercased and return JSX
	- Components are the functional units of React and control the way the HTML should look, while React handles how it is rendered
	- JSX is powerful in that we can embed JavaScript expressions to it, making it very easy to use iteration methods to programmatically create JSX elements from collections
		- React requires a `key` attribute for elements in an array to uniquely identify which elements need to be updated (avoid using indexes)
- Forms - [ReactJS Forms](./ReactJS%20Forms.md) are controlled components that need state synched to the form input
	- input value = state variable
	- we need event listeners for `onChange` and any other user interactions (e.g. onClick or onSubmit) 
	- use setState functions to reset the form input elements after
- Getting data from server
	- Used the the promise-based [axios](./Axios.md) package to make XHR requests to the local server, `json-server` to serve JSON data
	- Side effects like fetching data should be called from inside the [useEffect hook](./React.md#useEffect%20hooks)
- Altering data in server
	- Factored out `[[axios]]` AJAX requests into a modules housed in the `src/services` folder 
	- Added error handling catch statements
	- Added custom message banners in the Notification component that disappear after a few seconds using `setTimeout`
- Adding styles to React
	- Added inline style to Notification component, by passing a CSS-like object to style attribute
	- We can use the `className` attribute and CSS style sheet for styling, however React's philosophy is to keep styling coupled with the component

##### Developing locally
Javascript code that makes up our React application is run in the browser. The browser gets the JavaScript from the React dev server (running on localhost:3000 after `npm start`). 
- dev-server transforms JS into a format understood by the browser and stiches together JS from different files (our components, App.js, index.js) into one file
- Our running React app in the browser fetches JSON data from `json-server` running on localhost:3001, which queries our `db.json` file.
	- later, we move the database to mongoDB
![Pasted image 20220321095218.png](./images/Pasted%20image%2020220321095218.png)

##### Setting up json-server
1. install json-server package as a dev dependency
	`npm install json-server --save-dev`
1. Create a `db.json` file in the root directory of the React app, optionally with some data to test with
2. Add the below to package.json under "scripts":
`    "server": "json-server -p3001 --watch db.json"`
1. Run `npm run server` to start the server on port 3001 in a different terminal than your React app
	1. Note that 3001 is specified because React apps start on 3000 by default.


#### Part 3 Backend - REST API, Using Database, and Deployment 
- [NodeJS](./notes/NodeJS.md) and [ExpressJS](./notes/ExpressJS.md)
- Deploying to internet using [Heroku](./Heroku.md)
	- Walkthrough is [FSO - ExpressJS](./FSO%20-%20ExpressJS.md)
#todonotes 
==**SUMMARIZE THIS PART**==

We started with two separate projects: React front end running on one server, and the express backend, running on another server
![Pasted image 20220323221042.png](./images/Pasted%20image%2020220323221042.png)

##### Production Build and Hosting on Heroku
- We were able to deploy the backend to Heroku. 
- We then merged the backend and front end by creating a production build of React and moving the static and build files to express project and using middleware to tell express where to fetch and serve static files certain GET requests (index.html, since it's a single page app!)
- All other routes to the backend api for getting data/update/delete/post are handled by express still in our index.js.
- After confirming we can run the entire app one on local server, we pushed changes to the heroku git repository

![Pasted image 20220323221920.png](./images/Pasted%20image%2020220323221920.png)

##### Saving data to MongoDB
- Course uses [MongoDB](./MongoDB.md) Atlas service instead of installing and running mongoDB on computer. 
- Install [mongoose](./mongoose.md)
	- ⚠️ could not get mongoDB to connect on Ubuntu, had to run dev express from windows CLI
		- tried whitelisting the linux VM IP address
		- this also broke the ability to run React in dev: `Proxy error: Could not proxy request /api/notes from localhost:3000 to http://localhost:3001.` Can only get backend `api/notes/` end points : 😔
- defined a schema for a Note object (content string, date Date, important Boolean) as a "document"
	- exported to it's own node module under the `/models` folder, handles connecting to database, transforming Note document, and defining Note schema
	- factored out the mongodb url (generated by Atlas) and password to [Environment Variables](./Environment%20Variables.md) using the [dotenv](dotenv.md) package
	- 🥳 now we can leverage mongoose built-in methods to find `Note.findbyId`, `Note.findByIdAndRemove`, etc. 

###### Refactoring mongo object fields
- mongo automatically creates `._id` and `.__v` fields on all instances of models. we modified the `toJSON` method of the schema to return a string `id` field instead for ease of testing and deleted the `._id` and `.__v` so those aren't returned to the frontend
- After mongoose fetches data objects from the database, express sends the collection of  mongoose objects to the client in the HTTP response via the `response.json()` [ express method](./notes/ExpressJS.md#Response%20methods) 
	- express's `response.json()` method sends an HTTP response with the correct content-type header and converts the parameter using JavaScript's `JSON.stringify`
		- `JSON.stringify` takes an optional replacer function that alters the behavior of the stringification process (e.g. you can pick how what values to stringify and included in output)
	- This is the point in the code at which the mongoose `toJSON` method is called on each object in the collection, as the replacer function to `JSON.stringify`

```json
    {
        "_id": "623da9f7a536829158d28e8e",
        "title": "my first blog post",
        "author": "audry hsu",
        "likes": 0,
        "__v": 0
    } // becomes...
        {
        "title": "my first blog post",
        "author": "audry hsu",
        "likes": 0,
        "id": "623da9f7a536829158d28e8e"
    },
```


##### Validation and ESLint
- define validation rules for each field in Notes schema, now mongoose takes care of this instead of express
- deployed the database backend to production in Heroku and monitored requests to the [MongoDB atlas online](https://cloud.mongodb.com/)
- Installed `eslint` as a local dev dependency 
	- created a `.eslintrc.js` configuration file that specifies rules that we want to include
	- 
#### Part 4 Backend - Testing and Authentication
##### - Structure of backend application as part of *best practices*, although it is not strictly  enforced
- extract out modules to `/utils` folder to simplify the `index.js`
	- create a `logger` module that handles `console log/errors` which makes it easier to write logs to a file or send them externally to logging tools
	- create `config` module handling of environment variables
	- create `middleware` module that houses custom middleware functions, like unknown end points and error handler
- extract out route handlers to `/controllers/notes.js`, where an express Router object (a middleware app) handles the middleware and routing functions if a request is made to our backend URI (e.g. `/api/notes`)
	- 

Now, our main express `app.js` is responsible for:
1. connecting to the db
2. taking middleware into use
3. delegates route handling to the controller Router

`index.js` is responsible for:
1. creating a node HTTP server
2. configure server to listen on specific port
```
├── index.js
├── app.js
├── build
│   └── ...
├── controllers
│   └── notes.js
├── models
│   └── note.js
├── package-lock.json
├── package.json
├── utils
│   ├── config.js
│   ├── logger.js
│   └── middleware.js 
```

##### Unit testing in Node
- install [Jest](./Jest.md) as a dev testing tool and our npm script test to run jest
	- specify node as the execution environment in package.json or in a `jest.config.js` file
- update eslint configuration to add `'jest': true` under the `'env'` object
- folder `test.js` files in `/test` directory
	- create testing units for each helper function in `utils/list_helper.js`
##### Testing backend
Implement testing that tests backend and database, also called *integration testing* when testing multiple components together
- option to mock up a database instead of using a real one using libraries like mongodb-memory-server 
In FSO, we will test entire app through REST API, including the database

```ad-note
title: mongoDB locally

💬 For Blogs app, I chose to [download mongodb locally on WSL](https://github.com/michaeltreat/Windows-Subsystem-For-Linux-Setup-Guide/blob/master/readmes/installs/MongoDB.md) since I was having issues connecting to MongoDB Atlas from WSL.
	- the database files are saved under `home/ahsu/data/db`
	- To run server: `sudo mongod --dbpath ~/data/db`
	- To run mongo shell: `mongo`

```

###### Differentiate development vs production mode
To be able to modify the way that our application runs in different modes,  use the `NODE_ENV` environment variable to indicate whether we are running in *production* or *development* mode. We can set this environment variable using npm scripts in `package.json.`
- npm start --> `NODE_ENV=production `
- npm run dev --> `NODE_ENV=development `
- npm test --> `NODE_ENV=development `
	- we add the `--runInBand` flag to prevent [Jest](./Jest.md) from running tests in parallel
- Note: install the `cross-env` package to to make setting environment variables cross OS platform compatible (different ways for Windows of Linux)
- Update app's configuration file to use a different mongoDB URI if the NODE_ENV is test 
- Update .env file to include a testing db location

🔑 Running different modes lets us do things like use a different test DB than the production DB. 

Ideally, you want to run tests using a db that is running on the developer's local machine AND to have very test execution use it's own separate DB. You could do this using mongo in-memory or using Docker containers.  

###### Testing Helpers
- install `supertest` package to test the API
	- while we still run the tests using jest, `supertest` has built-in methods to make AJAX calls to api routes and methods to compare the http response with what we expect (e.g. status codes, content-type headers, response body)
	- supertest takes care that the application being tested is started at the port that it uses internally (e.g. don't need to run the app in another terminal)
- use afterAll jest methods to close database connection after all tests complete.
- beforeEach to clear out test database and create consistent test data before each test unit

High level approach to API testing
1. Perform an initial fetch of data
2. Run the test CRUD operation using supertest on the db
	1. Test the response status code
	2. Test the response headers, if applicable
	3. Test the response body (e.g. contents were added/updated)
3. Inspect database post-operation with another fetch
	1. Test the length of the resulting data
	2. Test contents of resulting data (e.g. deleted data does not remain)

###### Refactoring async functions
In this part, we updated test code and our controller code to using [Async Await](./Async%20Await.md) syntax.
- 😍 install `express-async-errors` package to reduce code clutter from try/except blocks
	- it allows us to eliminate try-catch blocks; calling `next(error)` is handled under the hood 
	- import it into the main `app.js` file before creating express app

Refactored testing `beforeEach` to use [Promise.all](./Promises.md#Orchestrating%20Promises) to wait for async functions (saving testing objects) to be completed before starting test suite.
- Remember, we should NOT use loops like forEach to try and orchestrate promises! 

##### User administration

Create a Schema for users to construct the User model.
	- username, name, passwordHash, and notes
		- notes property is an array of objects that *reference* the Note document they created
		- passwordHash is the output of a one-way hash function applied to user's password
		- install [bcrypt](./bcrypt.md) to generate password hashes
- Goal is to create a new collection of Users that are creators of Notes
While there are different ways to relate Users to their Notes, we made the design decision to store the ids of notes created by users in the User document AND store the user ID in the note schema.
- this is a sort of pseudo foreign key
- note that ODMs don't natively support join queries. Mongoose API has some abstractions to perform aggregation queries, but under the hood it is still multiple queries

 references are now stored in both mongoose documents: the user references the user who created it, and the user has an array of references to all of the notes created by them.

###### Create users
follow RESTFUL API convetions via a POST request to the *users* path in a separate express router.
- create a new router under `/controller/users.js` that should handle all user related HTTP requests and `app.use` this middleware in the `app.js`
- use [bcrypt](./bcrypt.md) to hash user passwords with salt of 10
	- save the hashed password ONLY to the db, not the password itself

- Write tests for the user router to be able to create new users and confirm that we can create new notes that are linked with a user 

###### Pseudo Join queries using mongoose
Add functionality so that GET request to users route shows all notes and their contents. 
- each note's user field has a reference to the `._id` of the user that created it
- each object in a user's notes field has a reference to the `._id` of the corresponding note
- mongoose's `Model.populate()` method essentially takes those references and replaces the `._id` with the actual mongoose document. 
```js
// note route 
notesRouter.get('/', async (request, response) => {
  const notes = await Note
    .find({})
    .populate('user', { username: 1, name: 1}) // configure to only populate username and name fields
  response.json(notes)
})
```
```js
// note model
const noteSchema = new mongoose.Schema({
//...
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User'
  }
})
```
- For example, the database doesn't know that the ids stored in a notes' user field references documents in the users collection
- `note.user` will store an `ObjectId`
- the `ref` option tells mongoose which model to use during population
- all `._id`s stored here must be from the User model

##### [Token based Authentication](./Token%20based%20Authentication.md)
- install `jsonwebtoken` package and create a new controller route for logins
- when a new login POST request is validated, the route creates and returns a signed token
- modify the POST notes route to only allow new notes to be created for logged in users
	- this means we need to send the token from the browser to the server for these requests.
	- Tokens will be stored in the Authorization HTTP header of the request
	- We defined a helper function to extract the jsonwebtoken from the request header 
	- in our POST route, we must use the `jwt.verify()` method to decode the token and validate that it is present and valid
	- We use the user id stored in the `id` field inside the token and query mongoDB for the proper user instead of getting the user ID from the request body. 
	-  update the error handling middleware to gracefully handle when a token is invalid or expired

For additional security, we implement a validity period before tokens expire
- pass in a configuration object with the `{expiresIn: time}` before we sign the token in the login route
![Pasted image 20220330151250.png](./images/Pasted%20image%2020220330151250.png)
#### Part 5 - Front-end & tokens
##### Login in frontend
We need to add login functionality to the front end and authenticate a user to be able to send HTTP POST requests to the database.

- add a login form in React
	- manage form state
	- handleLogin event handler makes an AJAX HTTP request via the login module service which returns a user object and token
- add user state management to track when what user is logged in 
	- login form is hidden when a user is logged in

###### Conditionally show the login form and notes form based on user state
- Factored out form to add new notes and the login form to helper functions
- Use embedded JSX expression with logical && operator is a commonly used react trick to render forms conditionally:

```js
      {user === null && loginForm()}
      {user !== null && noteForm()}
```
- If `user===null` is truthy, then `loginForm()` will execute
- if the first statement evaluates to false/falsy, then the second statement is *not* executed

###### Add token of logged in user to authorization header of HTTP request
As a refresher, our express backend creates,  signs, and sends back a JSON web token in the HTTP response body when a log-in is successful after comparing the username and password against the password hash with [bcrypt](./bcrypt.md). 

We modify the React notes service helper module to contain a private `token` variable that is set based on the returned token after log-in. The private `token` is sent with every POST request to create new notes in the HTTP Authorization header via a configuration object in the axios AJAX request. 

###### Saving token to browser's local storage
We can save data using the [Web Storage API](./Web%20Storage%20API.md), which allows browsers to store key/value pairs (rather than using cookies).

We store the user credentials in the `window.localStorage` so that every page refresh does not require a login. 
- Values in local storage are persisted even when the page is rerendered, and the storage is origin specific (each web app has its own)
- local storage saves key/value pairs as *strings*
- We can inspect `window.localStorage` object in the browser console or under Applications > local storage in Chrome dev tools!

Use React's effect hook to check user details of logged-in user from browser local storage once after user logs in. Now the user is perpetually logged in, since the credentials are saved to React's state.

##### props.children and prop types
We can designate child components of a parent component. When we use a component with opening and closing brackets, it signals that it contains children components within.

```js
<Parent Component 
//...any elements go here
	<ChildComponent />
</ParentComponent>
```

We create a reusable Togglable component as a parent to the LoginForm. Togglable will have it's own state separate from App. React automatically provides a `props.children` property that is available to all components. If a component is defined with an automatically closing tag `/>` then `props.children` is set to an empty array.  

###### References to components with ref
The primary way to for parent components to interact iwth child components is via re-rendering with new props. 

To access variables inside of another child component, React  has the ref mechanism that lets you reference a component. We can use Refs for managing animations, such as hiding a form. 

==REVIEW REFS, USEIMPERATIVEHANDLE, FORWARDREF==

###### Prop type checking
- Install `prop-types` package, which will perform runtime type checking for React props based on what we document as the intended types of properties passed to components. This is super helpful during development.
	- Use this package to define which props are required what their expected type is
	- console will now display an error message if you forget to pass a required prop to a component or the wrong type.
	- Note that the app won't crash, we just get a dev error message

###### ESlint frontend
- create-react-app automatically comes with ESlint
	- configure the `.eslintrc.js` file
	- configure `.eslintignore` file to ignore node_modules, build, and eslintrc
	- add npm script to run the lint
- in order to avoid undesired and irrelevant linter errors for testing,  install the eslint-plugin-jest package

##### Testing React apps
🔬 Note that react's standard is to defined tests in the *same directory* as the component app source code.

###### Set up Unit Tests
Use [Jest](./Jest.md) ,  `react-testing-library`, and `jest-dom`, which has gotten rapidly popular recently
- Jest is configured by default to apps created with create-react-app! yay! We don't need to install or configure it
- `create-react-app` configures tests to be run in watch mode by default (won't exit after tests are finished and will wait for code to be changed). 
	- Run `CI=true npm test` to override this 

###### React-testing-library
`react-testing-library` tests for what is rendered to the screen rather than testing state or props
- we can use the `screen` object and the `getbyText` method to search for an element that has specific text content
	- `render()` method will create a div and append that div to the document.body and this is where your React component will be rendered.
		- returns an object with a  `container` property, which is the Dom node of the rendered React element
			- Since this just a regular DOM node, we can use `querySelector` method to find specific elements via CSS selectors
	- `screen.debug()` prints out the HTML created by the `render` to the console for inspection. You can also pass in an element to inspect, too. Handy!
- Use `user-event` package to simulate user input easier for testing event handlers.
	- in OOP, mock objects are simulated that mimic behavior of real objects in controlled ways for testing. Great for when it's too complex to test the real object (e.g. difficult to recreate state or reproduce an error, involves a database. return results that are non-determinisitc)

```js
 import '@testing-library/jest-dom/extend-expect'
 import { render, screen } from '@testing-library/react'
 import userEvent from '@testing-library/user-event'
 
test('clicking the button calls event handler once', async () => {
   const note = {
     content: 'Button was clicked',
     important: true
   }

   const mockHandler = jest.fn() // mock jest handler fn

   render(
     <Note note={note} toggleImportance={mockHandler} />
   )

	// find button element based on button text
   const button = screen.getByText('make not important')

	// simulate button click
   userEvent.click(button)
	// verifies mock function has been called once
   expect(mockHandler.mock.calls).toHaveLength(1)
 })
```

🔍[Cheat sheet of Testing Library queries for React](https://testing-library.com/docs/react-testing-library/cheatsheet/#queries)

###### Jest Coverage
We can check the coverage of our front end tests
```bash
$ CI=true npm test -- --coverage
```

###### Jest snapshot testing
🅾️ Snapshot testing do not have any tests, but just flags when something changes between snapshots.

Compare HTML code defined by the component after it has changed to the HTML code that existed before it was changed. If there is a diff in the snapshot, then the diff is either new functionality or a bug, and the developer must tell Jest which one it is.  

When a test is run for the first time, Jest stores a snapshot file, which is committed along with code changes. On subsequent test runs. Jest compares the rendered output to the previous snapshot. If something changed, theres a bug, or the snapshot needs to be updated.

##### End to end Testing
End to end tests the system as a whole and are most useful category of tests because they test the system through the same interface as real users (e.g. via a web application browser).  However, these are challenging and slow and flaky.

###### Cypress
Install cypress, runs completely within the browser (unlike Selenium, which runs outside the browser and uses remote commands across network). 

Cypress tests can be in the frontend or backend, and requires the tested system to be already running when tests start. 
- Cypress tests uses Mocha under the hood for testing syntax and jQuery selectors
	- Uses promises under the hood, so chaining is easy since methods *yield* their subjects
	- Cypress commands are like promises, so if we want to access their return values, we have to do it using the `then command`.
- we can simulate "visiting" a website and validate that the page "contains" some content
- Anything a browser can access, so can Cypress (e.g. `window`, `document`, and DOM elements

🤩 So cool, the GUI will show each test step and visualize the result in the browser!

Cypress recommend testing the full log in flow once, and then for the rest of the tests to bypass the UI and use HTTP request backend login for optimization.
- Cypress principle: do not use UI to build up state as it's slow. 
- we can use `cy.request()` to make HTTP requests
- factor out helpers into `/cypress/support/commands.js`, such as code to log in that will be used as utility

We were able to test:
- valid user log in
- invalid user log in
- notification banner was correct for either case above with proper text and CSS formatting
- creating a valid note
- changing importance of note 

###### Cypress internals
- `/cypress/integration` folder automatically created to house our tests
- cypress GUI should open after running `npm cypress open`
- Install  `eslint-plugin-cypress` as dev dependency
	-  adding `"cypress/globals": true` and adding `cypress` to `plugins` list to `.eslintrc.js` file to get rid of eslint complaints in our spec.js files. 
	- 

```ad-tip

Whether unit testing or E2E testing, best practice is to "reset" the database before each test to start with same state everytime.

```

For tests to be able to modify the server's database, we need to create an express/node API endpoint to the backend for the test.
- endpoint will be able to empty the database and "reset" db state for each test by deleting all notes and users from mongoDB
- The  `testingRouter` middleware will only be put into use by express if the app is run in test mode 
- we can use `cy.request()` method to make HTTP requests to the backend.

###### Configuring vcXsrv and Cypress
This is a pain because WSL2 does not natively allow linux GUI applications, so we must use vcXsrv to launch. 

Did not solve:
- Installed intel gpu driver for WSL
- updated WSL2 and restarted - ubuntu 20.04
	- https://docs.microsoft.com/en-us/windows/wsl/tutorials/gui-apps

Solved:
- configured firewall inbound for vcXsrv
- Cypress unable to open X server. Version 9.5.3
	- issue was previous xlaunch attempts before firewall was configured kept blocking and overriding the manual access allow
- xLaunch with ac flag
- running express backend in test on port 3001, connects to mongodb atlas fine
- https://nickymeuleman.netlify.app/blog/gui-on-wsl2-cypress

Run mongod server local
```bash
$ sudo mongod --dbpath ~/data/db.
```


#### Part 6 - State management with Redux
##### Flux and Redux
[Flux](./Flux.md) & [Redux](./Redux.md)

###### Test driven development
- Install the [deep-freeze](https://github.com/substack/deep-freeze) testing library as a dev dependency that ensures that the reducer has been defined correctly as an immutable function.
	- call `deepFreeze(state)` to freeze our state object to any changes before calling the reducer methods. The test will fail if deepFreeze senses we are trying to mutate state object

###### Forwarding Redux store to various components
To share the redux store with an App's components, we can use the hooks api from the `react-redux` library. We need this binding to separate out `App.js` and `index.js ` to separate module files. This library is just an abstraction layer for implementing redux stores.

React Redux lets React components read data from a Redux store, and dispatch actions to the store to update state. It is a "UI binding library" handles the store interaction logic between the Redux store and the UI.

In index.js:
- App component becomes a child of the `Provider` component provided by react redux library
	- App component must pass the `store` as props to all components that need access to it
- application store is given to `Provider` as an attribute
- move action creators to `/reducers/noteReducer.js` where reducer is defined

In App.js:
- import the `useDispatch` hook that provides any React component access to the dispatch function of the redux-store defined in index.js.
	- This allows components to make changes to state of redux-store
- import `useSelector` hook, which receives a function as a parameter to either search for or select data from the redux-store.
	- In the snippet below, we return all of the notes from the redux-store
```js
import {useSelector, useDispatch} from 'react-redux'

const App = () => {
  const dispatch = useDispatch()
  const notes = useSelector(state => state)
  //...
```

Further refactor:
- separate creating a new note to it's own component
	- be able to dispatch actions to create notes
	- addNote event handler 

End result:
- `App.js` is greatly simplified since state is extracted out (this was the only reason why we needed all the components and their event handlers in once place was to access/set state)
	- `App.js` simplified to only the declarative JSX describing what components go where 
- Components are modularized *outside* of App.js and give them access to state via the `useSelector` hook, if they need it
- Any component that has user interactions (buttons, forms) that would trigger state change can  `useDispatch` to send actions to the reducer.
- Reducer and the business logic of *how* to update state based on the action modularized (in it's own file) away from components and App.js
	- We can define action creator helper functions as "factory" functions to return action objects with their specified `type` and data payload structure.
	- Action creator helpers can be used in component modules that trigger actions
- Reducer returns new or copy of previous state to the `store` that was created in the `index.js`

##### Many Reducers
Managed store with complex state by adding ability to filter notes displayed based on importance using radio buttons.
- implement this by adding a `filter` property to our global state
- a Visibility Filter component has radio buttons that indicate which notes to show based on importance
	- on change event handler calls the `filterChange` action creator to create a `SET_FILTER` action
	- `dispatch` method sends the action to the `filterReducer`  

Now that we have three distinct "paths" for our reducer (create a new note, toggle importance, and filter), it makes sense to modularize our reducer into several reducers and separate concerns.
- Now each reducer module is only responsible for the logic related to their `action.type`
- Our filter reducer  

We use `combineReducers()` and can still pass one single reducer to `createStore()`. 
```js
import noteReducer from './reducers/noteReducer'
import filterReducer from './reducers/filterReducer'
// different reducers manage different domains of app state
const reducer = combineReducers({
  notes: noteReducer,
  filter: filterReducer
})
const store = createStore(reducer)
```

##### Redux Toolkit
[Redux toolkit](https://redux-toolkit.js.org/tutorials/quick-start#add-slice-reducers-to-the-store) is a library to help with Redux configuration and state management implementation and abstract away some boiler plate code (e.g. combining reducers, creating store). 

➕ Using configureStore also means that there's no additional configuration for Redux DevTools in chrome to work.
➕ This all works well with React, Redux, and `react-redux`
➕ uses [Immer](https://immerjs.github.io/immer/) API under the hood, which simplifies handling immutable data structures in React and Redux
- allows mutations to a "draft" object, which are recorded and then used to produce the next state (takes care of copying under the hood)
- prevents accidental modifications by freezing data

The terminology of a *slice reducer* refers to a reducer that is being used to handle updates to one *specific slice of the state tree* 
- *case reducers* are functions intended to handle a specific *action type* -- (hint: equivalent to a single case statement in switch statement where we check the action type)

###### Abstraction Features 
1. Use `configureStore()` to create store from multiple reducers with less boilerplate (uses Redux's `combineReducers` under the hood)

1. `createSlice()` - Couples reducers and action creator helper functions together  [createSlice function](https://redux-toolkit.js.org/api/createSlice#reducers)
	- in it's configuration object, we define following properties:
		- `initialState` - the initial state for this "slice" of the state tree
		-  `reducers` - an object containing Redux "case" reducer functions
		- `name` -  string for this slice. This will be the prefix for generated action types
	- no more switch statements to find the action type!
	- action type is mapped to the case reducer function name

```bash
$ npm install @reduxjs/toolkit
```

**Using createSlice**
```js
// noteReducer.js
import {createSlice} from '@reduxjs/toolkit'

const noteSlice = createSlice({
  name: 'notes', // prefix to be use in action's `type` values
  initialState, // object representing initial state
  // An object of "reducers". Key names will be used to generate actions.
  reducers: {
    createNote(state, action) {
      const content = action.payload
      state.push({
        content,
        important: false
        id: generateId(),
      })
    },
    toggleImportanceOf(state, action) {
    //... rest of reducer logic
  }
})

// createSlice returns an OBJECT that looks like:
{
    name : string,
    reducer : ReducerFunction, // gets passed to combinedReducers as a "slice reducer"
    actions : Record<string, ActionCreator>, // each fn defined in 'reducers' argument will have corresponding action creator generated using createAction
    caseReducers: Record<string, CaseReducer>.
    getInitialState: () => State
}
```

Redux Dev Tools:
- can dispatch actions to the store
- impsect how dispatching action changes state by clicking on the action2


##### Communicating with a server in Redux application
This part, we are using `json-server` as the dev database and `axios` to make AJAX HTTP calls
1. Initialize application with data from the server
- Remove hard coded variables with initial state from reducers and use backend data (json file)
- in App.js -`useEffect` hook to initialize state with data returned from axios AJAX call to fetch db data
	- invoke dispatch method to send db data to a reducer that sets the store state 
2. Refactor component event handler to save new notes to the database
	1. once saved to the database, use returned response data as action payload, create action, and dispatch to reducer function.

To abstract away the communication with the server (AJAX calls with our `/services` modules) from the component event handlers, we use the Redux Thunk library. This will keep the component code pure to only calling the action creator *after* the database operation has completed.

###### Redux Thunk
"Thunk" is a programming term that means a piece of code does some delayed work. [Redux thunks](https://redux.js.org/usage/writing-logic-thunks) are a pattern of writing functions with logic inside that can interact with a Redux store's `dispatch` and `getState` methods. Thunks are the standard approach for writing async logic in Redux apps (e.g. use for data fetching) to separate logic or code that causes side effects from the UI layer. This keeps React "unaware" of Redux. 

Redux Thunk is middleware, and the functions implement action creators *which return a function instead of an object*. 
- function receives Redux store's `dispatch` and `getState` methods as parameters.
- **async action creators wait for an async operation first before dispatching some action**, which is exactly our use case. 

The use of the library doesn't need any additional configuration when the Redux store is created using the Redux Toolkit's configureStore function (aka already installed)

There are other APIs such as [RTK Query](https://redux-toolkit.js.org/rtk-query/overview) that aim to further abstract and simplify data fetching and caching, since they can be considered a separate concern from "state management". 

###### connect from react-redux
*Note: it is recommended to use react-redux hooks as the default for new apps, is the predecessor `connect`*

Thus far, we have been using the [hooks API](https://react-redux.js.org/api/hooks) , namely `useSelector` and `useDispatch` hooks to give components ability to use local component state. Another, more complicated way is to use the `connect` function. 

The `connect` function can be used for transforming "regular" React components so that the state of the Redux store can be "mapped" into the component's props. 
- This creates slightly more *separation of concerns* as the functional components themselves are more "purely presentational"
- the *connected component* that is created with the `connect` function aligns closer to a *container component*.

This pattern for separating components (by Dan Abramov) is *one* way of structuring React applications for better separation of concerns and reusability. 

🎨 **Presentational components**: 
-  concerned with how things *look
-  usually have DOM markup styles
- don't specify how data is loaded or mutated
- rarely have their own state (when they do, its UI state rather than data)
⚙️ **Container components:**
- concerned with how things work
- may have presentational and container components inside but usually don't have DOM markup or styles
- provide the data and behavior to presentational or other container components
- call Redux actions 
- often stateful, tend to serve as data sources

###### connect Pattern
In this pattern, we *connect* components access to the state of the store directly through `props.notes` that contains the list of notes and `props.filter` that references value of the filter. 

![Pasted image 20220412101014.png](./images/Pasted%20image%2020220412101014.png)
Components can reference a function that can be used for dispatching `notes/toggleImportanceOf`-type actions via the prop.
![Pasted image 20220412101345.png](./images/Pasted%20image%2020220412101345.png)

###### You might not need Redux
As with any technology, you should understand your use case and the tradeoffs. It's not an absolute to always use redux with react. In Dan Abromov's [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367), he goes into this.

It's possible to implement redux-like state management without redux using React's Context-api and the useReducer hook.


#### Part 7 - React router, custom hooks, styling app with CSS and webpack
We've used `create-react-app` to generate vody of our applications. Webpack is what configures the app under the hood. 

##### React Router to handle routing in SPA
**Problem: Routing using React State isn't efficient or ideal.**
- Using components + React state to render "pages" won't change the URL address for each view (to be able to bookmark)
- the back button won't work as expected.
- This also doesn't scale well, as we'd have to create a new component for every new page or "route".

###### Why React router
[React Router](https://reactrouter.com/docs/en/v6/api#matchpath) Helps divide application into different views that are shown based on URL routes in the browser address bar. This is handy for SPAs to simulate different "pages" when there is a navigation bar.

Routing means conditional rendering of components based on the URL in the browser is implementing by placing the components as children inside the `BrowserRouter` component.

`BrowserRouter` is a router that uses [HTML5 history API](https://developer.mozilla.org/en-US/docs/web/api/history_api) to keep UI in sync with the URL by manipulating the URL in the address bar (and the history stack) for internal "routing" in React applications without reloading or navigating away from the page, since our AJAX is what changes the content on the page. 
- back and forward buttons on browser history work the way we expect it to since `BrowserRouter` can pushState to the history stack
- *Links* are defined inside the router and are elements that let users navigate to another "page", like an anchor tag
	- the `to` attribute is the value the address URL 
- *Routes* looks through all of its children *Route* elements and renders first component whose `path` matches the url in browser address bar.
	- *Route* elements have a `path` attribute and `element` attribute.
		- If the browser's address matches path in  `path` attribute, then render the element in `element.`
		- `element`s usually refer to a defined Component

###### react router hooks
- `useNavigate` - lets us modify the current "location" and navigate to another route. Typically used in an event handler
	- In react-router, location refers to `Location` interface from history library. A `location` is an entry in the history stack, usually a "page" or "screen" in the app. 
- `useParams` - returns an object of key/value pairs of the params from current URL that were matched by Route path.
- `useMatch` - takes in a string route and determines if the current URL address matches the route. If url matches given route, it returns an object from which we can access the parameterized part of the path
	- example: `useMatch('/notes/:id')` would return an object containing `{params: {id: 1}` if the current url matches.

##### Custom Hooks
We can build our own [hooks](./React.md#Hooks) in React to reuse component logic.

- regular JavaScript functions, but still need to follow [rules of hooks](./React.md#Rules%20of%20using%20Hooks%20⚠️)
- great for extracting repetitive *stateful logic*, like state to manage form input
	- nice because you can encapsulate `useEffect` hook inside the custom hook away from your main App and reduce clutter!

Common use cases:
- handling form input state `useField`
- handling AJAX requests responsible for communicating with the backend and updating "resources" state (`useResource`)
- [🔗 Part 7b](https://fullstackopen.com/en/part7/custom_hooks)


##### React Styles
[React Bootstrap](https://react-bootstrap.github.io/) and MaterialUI are two UI frameworks. There are many others like this that integrate well with React and provide you pre-styled components that function like React components that you can import.

*[Styled components](https://styled-components.com/docs/basics)* is another approach for defining styles through [tagged template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates) (an [JavaScript ES6 Features](./JavaScript%20ES6%20Features.md) feature). It uses straight up CSS syntax that you apply directly to a component without having to use classes. 

##### Webpack
Before `create-react-app`, developers had to set up bundling configurations themselves. Thankfully, CRA uses webpack behind the scenes. 

Webpack is a static module bundler for JS applications and builds a *dependency graph* (e.g. libraries, images, fonts, etc.) of all of the modules your app needs and bundles them together for the browser. 

Browsers used to not know how to handle code that is divided into modules, thus source code must be *bundled* together in a single application code file ([like when we ran a production build of React app](Fullstack%20Open%20Project%20(FSO).md#Part%203%20Backend%20-%20REST%20API%20Using%20Database%20and%20Deployment)) or a few files. 

Benefits of bundling:
- minimizes production code file size via minifying
- easier debugging using source mapping
- configuring a transpiler transform code from ES7 JS to older ES5 syntax (we used babel to transpile JSX into JS)

Concepts of Webpack
- Webpack starts at an *entry point*, or a file where it should begin building the dependency graph (like an `index.html`)
	- defaults to `src/index.js` but can be configured
- During bundling, webpack includes all of the code that the entry point imports, the code that its imports import, etc.
- Output is where the bundled code will be stored
###### Loaders
Webpack can only bundle plain javascript. Without the proper loaders installed, bundling ("build") process will fail
- To bundle React code that uses JSX, configure a loader in the config file and install the appropriate loader
- same for CSS files
###### Source map
We can configure webpack to generate a *source map* for the bundle to make mapping errors from execution of bundle to original source code (e.g. our editor files) easier

###### Minifying
The bundled application code generated by webpack is relatively large compared to the number of lines of code in our source code because it's including *the source code for the entire React library*.

❓Size of bundled code matters since browser has to load the code when the app is first used. This can be cumbersome for mobile devices.

We can optimize our bundled production code *minifying* it using library UglifyJs.
- We can specify `--mode=production` when we run our webpack build and it will also minify our code

###### Polyfill
Polyfill is code that adds missing functionality to older browsers, and can be added with help of webpack and babel or using another polyfill library. 

Example: IE doesn't support Promises. You can use `promise-polyfill` library that provides a global `PromisePolyfill` variable if it detects that the browser doesn't have a `Promise` object. 
```js
import PromisePolyfill from 'promise-polyfill'

if (!window.Promise) {
  window.Promise = PromisePolyfill
}
```

###### Ejecting
If the default configuration isn't enough, we can always `eject` the project, which gets rid of all of CRA's configuration and then you have to manually maintain. 

#### Miscellaneous Part 7

Organizing Code
- We did front end and backend in separate repos
- Could also do a [single directory](https://github.com/fullstack-hy2020/create-app) with separate directories for front end (client) and backend (server) 

Changes on the Server
How do we push changes from the server (e.g. new blogs from other users) to the React front end without a reload? 
1. Use polling on the frontend, e.g. repeated requests to backend API to check for updates using setInterval
2. Websockets API (web API) - 2 way open communication channel between client and server that lets server send data and client has a callback to handle incoming data
3. Socket.io library - provides fallback options in case a browser isn't compatible with WebSockets

#### Trends
1. Statically typed languages like [Typescript](./TypeScript.md)
2. [Microservices](./Microservices.md)
3. Serverless - serverless offerings like AWS [Lambda](./Lambda.md) allow the execution of functions in the cloud (functions as a service)
	1. Serverless applications mean requests to HTTP API get responses from cloud functions.
	2. The impact of serverless isn't about server or no server -- developers can work at higher abstraction level and not worry about routing HTTP requests, database relations 
	3. [ ] #tolearn Read: [Benefits of Serverless](https://www.splunk.com/en_us/data-insider/what-are-serverless-functions.html)
4. [Progressive Web Applications](./Progressive%20Web%20Applications.md)


### Development Notes
- store environment variables in a `.env` file in the root project folder
	- you can access these variables in App.js like: 
```js
const API_KEY= process.env.REACT_APP_API_KEY
```
##### Third party libraries used
- json-sever: run a local server with json data for making ajax requests
- [axios](./Axios.md): an *isomorphic* package that uses [NodeJS](./notes/NodeJS.md) http module for server side HTTP requests and [XMLHttpRequest](./XMLHttpRequest.md) on the browser.
- mongoose - 


Structuring React project
- src folder is essentially your workspace
	- App.js is the main front-end logic
	- components  folder-> extract out helper components that are imported by App.js
	- services folder -> extract out into separate JavaScript modules logic pertaining to backend, such as making ajax requests to server 
		- this might be temporary

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference: [Fullstack Open Course](https://fullstackopen.com/en/part0/fundamentals_of_web_apps)

Related: 

---
tags: 📥️ 🌱
publish:
aliases: 
cssclass: 
---

# [<%tp.file.title%>](%3C%25tp.file.title%25%3E.md)

---





---
Tags: 

Reference:

Related: 