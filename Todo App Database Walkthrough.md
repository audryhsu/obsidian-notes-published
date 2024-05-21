--
creation date: 2022-01-26 17:56
aliases:
tags: 🖥️
---

# [Todo App Database Walkthrough](Todo%20App%20Database%20Walkthrough.md)
---
Activities:
1. Move session handling to a module - separates responsibilities and reduces dependencies of app on a specific data storage scheme
2. Transition session store to using a database
3. Configure app control with authentication
4. deploy to [Heroku](./Heroku.md)

**Focus on:**
- Understanding what schema is needed for the app
- Understanding [Asynchronous Programming](./Asynchronous%20Programming.md) operations
- 

Project setup is secondary. 

## Modularizing session store
Define a class called 

#### Seed data
**Create seed data** for testing purposes and we make a [deep copy](deep%20copy.md) of it as not to mutate the data since we are going to work with multiple users. 


Idiom for making deep copies is well-known
```js
const deepCopy = object => {
  if (typeof object !== "object") return object;
  return JSON.parse(JSON.stringify(object));
};

module.exports = deepCopy;
```
- modules will be manipulating and returning deep copies of list of todo lists since it is a public facing method. we don't want to return any references to store data and forces users to use our API methods. 


### Diagrams
Start of app:

![Pasted image 20220127102427.png](./images/Pasted%20image%2020220127102427.png)

Target architecture:
We'll start by extracting all operations on the session store to a separate class that contains a variety of methods. These methods define an API that the application uses to access and update the data store. Those methods, in turn, use and manipulate the store directly. The resulting architecture looks like this:


![Pasted image 20220127102442.png](./images/Pasted%20image%2020220127102442.png)
why:
- add a layer between app data and the app code with the session persistence module
	- removing the TodoList and Todo classes. the todolists data are stored in the Session-Persistence module. We do not create any TodoList objects or Todo objects.
	- note: we are still using our libs from the prev. project iteration, but we are moving the library method invocations into SP class.  
- we define the SP api to safeguard our data by returning deep copies of session data
- we also move any data logic from views to express.js or api to:
	- separate view logic from data logic
	-  make sure any functions accessing database can be async

## Creating external data store
Next, we create PgPersistence module
- the api is functionally the same, so we really don't need to make changes to the express app except for subbing in the new module
- we do have to rewrite the PgPersistence class to be asynchronous since we are working with a database
- move repetitive code for making sql queries to db-query module and parameterize queries 

```js
module.exports = class PgPersistence {
  // omitted code

  // Returns a promise that resolves to the todo list with the specified ID. The
  // todo list contains the todos for that list. The todos are not sorted. The
  // Promise resolves to `undefined` if the todo list is not found.
  async loadTodoList(todoListId) {
    const FIND_TODOLIST = "SELECT * FROM todolists WHERE id = $1";
    const FIND_TODOS = "SELECT * FROM todos WHERE todolist_id = $1";

    let resultTodoList = dbQuery(FIND_TODOLIST, todoListId);
    let resultTodos = dbQuery(FIND_TODOS, todoListId);
    let resultBoth = await Promise.all([resultTodoList, resultTodos]);

    let todoList = resultBoth[0].rows[0];
    if (!todoList) return undefined;

    todoList.todos = resultBoth[1].rows;
    return todoList;
  }

  // omitted code
};

```
Note that we're making two separate queries here. We could use `await` for each query, but that's wasteful; it's more efficient to make simultaneous queries instead of waiting for one to complete. Thus, we don't use `await` when making these queries. Instead, we use `await Promise.all(...)` to wait for all of the queries to settle. `Promise.all` creates a new Promise that settles when all of the [Promises](./Promises.md) in the argument resolve.

- we need to make sure we add await keyword in todos.js for any async functions in pg-persistence; similarly, any dbQueries in pg-persistence need to also use async/await keywords;

#### Refactor error handling with catch-error module
- usually in asc, we need to use try/catch blocks for every function; usually in express this would happen within the route handlers, but this adds tons of repetitive code
- use a wrapper function around async middleware

### Summary 
- extracted session-specific functionality into SessionPersistance class
- Replaced synchronous SessionPersistence class with asynchronous PgPersistence class to store data in a db
- Parameterized SQL statements to prevent injection attacks
- Log queries made by our application

## Storing User Accounts in an External File
- create a db to store username and hashed passwords (using [bcrypt](./bcrypt.md))
- create add'l routes for sign in 
- store `username` and `signedIn` boolean in `req.session` object and extract it to `res.locals` before every req/response cycle to be used in middleware
- pass session data to `PgPersistence` module constructor, and extract `session.username` to `this.username` property to use in SQL queries
- add a middleware function `requiresAuthentication` and pass as callback to routes to handle checking of `signedIn` in express instead of pug views

### Optimization
**database queries** - **N + 1** query pattern are ripe for optimization; This is when the original query returns N items, then we must make N+1 queries in all
	- sortedTodoList method - we must query to get the todolist, then call the database for each item in the todolist; Avoid making db calls in a loop.
	-  Solution: make only two db queries to retrieve all user todolists and user todos; use add logic in API to associate the todos with the todolist 

### Security
- always use parameterized values for db queries 
- always validate user input to download files -- use a list of choices or use the built in path.basename method to restrict file requests to a single directory

Using path.basename to restrict downloads to .html or .md files 
```js
app.get("/show", (req, res) => {
  let filename = path.basename(req.query.filename);
  if (/\.html$/.test(filename) || /\.markdown$/.test(filename)) {
    res.sendFile(filename);
  } else {
    res.status(401).send("Unauthorized.");
  }
});```

```ad-tip

It's easier and safer to check for valid data than to test for invalid data. Code that checks for correct data is said to **allow-list** input instead of **block-list** the data.

```

#### Secrets
We need to protect sensitive information (API credentials, cryptographic keys, access to passwords, database names and locations) and restrict access from app users by removing from source code, the project files, and repository. 
- solution: use [environment variables](./Environment%20Variables.md)
- always add sensitive files to `.gitignore` - if you've pushed sensitive files, consider it compromise. 

### Summary

To summarize, try to keep the following guidelines in mind while developing web applications:

1.  Avoid displaying parameters in a browser.
2.  Avoid using parameters and user-provided inputs to construct file paths and SQL statements.
3.  Use whitelisting to define what is allowed explicitly.
4.  Put sensitive information in a place that is safe from prying eyes, but still available to the app.
5.  Use a `.gitignore` file!
6.  **Think like a malicious user.** Is it possible to introduce an _unexpected_ value into the system through _expected_ means such as parameters? Is it possible to misuse or abuse your application in a way that someone might exploit?


## Deploying to [Heroku](./Heroku.md)

#### Create Heroku app and add configurations 
- Create heroku app in command line
- create dev and production configs in `.env` file and in heroku app CLI respectively
- install and update source code to use dotenv module in config.js file
- update source code:
	-  todos.js --> use config.HOST, config.PORT, config.SECRET 
	- db-query.js --> use config.DATABASE_URL for the connection and add isProduction variable to check if app is running in production via Heorku's `NODE_ENV`environment variable

- Update package.json to define version of `node` and `npm` under `engines` property
- define `heroku-postbuild` script to run Heroku after building the application with the following command: 
`npm install --production`

#### Initialize database on server 
- create and initialize `hobby-dev` type database on the server (free version) and pass in app name with `-a` flag
```bash
$ ahsu@ahsu:~$ heroku addons:create heroku-postgresql:hobby-dev -a nameless-headland-26422

Creating heroku-postgresql:hobby-dev on ⬢ nameless-headland-26422... free
Database has been created and is available
 ! This database is empty. If upgrading, you can transfer
 ! data from another database with pg:copy
Created postgresql-symmetrical-83031 as DATABASE_URL
Use heroku addons:docs heroku-postgresql to view documentation
```
- launch `psql` with a connection ot the database on Heroku server! We can now create database by loading in our schema.sql and run any other regular SQL commands
```bash
ahsu@ahsu:/mnt/c/Users/audry/source/repos/todos$ heroku pg:psql < schema.sql -a nameless-headland-26422

--> Connecting to postgresql-symmetrical-83031
NOTICE:  relation "users" already exists, skipping
CREATE TABLE
CREATE TABLE
CREATE TABLE

ahsu@ahsu:/mnt/c/Users/audry/source/repos/todos$ heroku pg:psql < lib/users.sql -a nameless-headland-26422
--> Connecting to postgresql-symmetrical-83031
INSERT 0 3
```

#### Deploy 
1. push to github and heroku

Set up Heroku git to point to app's heroku repo:
```bash
# in project folder

$ heroku git:remote -a nameless-headland-26422
set git remote heroku to https://git.heroku.com/nameless-headland-26422.git

$ git push heroku main
```

2. Start server and test local version at `localhost`
```bash
$ heroku ps:scale web1
web=1:Free

# Run and open in browser
$ heroku local 

```

3. Run production version (...herokuapp.com)
```bash
$ heroku open
# this command should open the deployed Heroku app in your browser.
```
---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: 