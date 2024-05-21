---
aliases: 
tags: book
---
Tags: [Go Lang](./Go%20Lang.md)
Reference:

---
Status: 
- skipped [integration testing](Book%20Let's%20Go.md#integration%20testing) chapter
- stopped at 17.4 guided exercise:  Add Account page to app

Takeaways
* learned a alot of neat [Go Lang Testing](./Go%20Lang%20Testing.md) tooling! 

## Chapter 2: Foundations
#### Creating a module
- Choose a Module path for a project is a identifier for your project that should be unique to avoid conflicts with other people's projects or the standard library
If it's going to be an [ external package](./Go%20Lang%20Packages.md#Importing%20External%20Packages), the go path should be the github URL where it will be hosted.
- `go mod init <module path>` to turn our project directory into a module
- `go get <url path to github module>` to download 
Adding a module


#### [Go HTTP Server](./Go%20HTTP%20Server.md) Web Server
##### Router & Handler
- Go's `servemux`  (from `net/http` package) is a **router** to route HTTP requests to registered **handler functions** based on a URL pattern
	- super lightweight - look to 3rd party package for routing based on request method, URLs with variables in it, or regex patterns
- A handler is *an object that satifies the `http.Handler` interface*, aka object *must* have a `ServeHTTP()` with the below signature.
	- When servemux receives a HTTP request for `/` it will call the `ServeHTTP()` method, which will write the HTTP response.
```go
type Handler interface {
	ServeHTTP(ResponseWriter, *Request)
}

type home struct{} // object satisfies the Handler interface! 
function (h *home) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("this is homep age"))
}

mux:= http.NewServeMux()
mux.Handle("/", &home{})
```

Note that this is 2 step process:
1. Create the struct that implements `Handler` interface
2. Register handler to servemux router

Common to use the `mux.HandlerFunc` adapter (syntatic sugar 🍬) that transforms a regular function into a handler in one step
- convenient way to automatically add `ServeHTTP()` method to a regular function to satisfy Handler interface
	- `ServeHTTP()` called first, then calls the original unfction.

```go
// regular function (not a struct that implements Handler interface)
func home(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("this is home page"))
}

mux:= http.NewServeMux()
// convenience method that transforms func into handler
mux.Handler("/", http.HandlerFunc(home))

// also equivalent
mux.HandlerFunc("/", home)

```

###### Servemux is a special kind of handler
We passed servemux as a handler to `http.ListenAndServe()`, because it also implements the `serveHTTP()` method! 
```go
err := http.ListenAndServe(":4000", mux)
```
🧠 Go web appllications are a *chain of `ServeHTTP()`* methods being called one after another.
- instead of returning a resposne, servemux passes the request onto a second handler.
- Similar in Express, where we can pass unlimited middleware functions.

##### Customizing HTTP Responses & Headers 
- `w.Header.Set()` to add headers
- `w.Write()` automatically sends a `200` response, so make sure to set the status code with `w.WriteHeader()` first.
	- only possible to write to response header `w.WriteHeader()` **once per response**, so set all headers first.
- `http.Error()` shortcut that calls write header and write methods for us.
- Go tries to guess the right `Content-Type` by *sniffing* the response body, but it's best to explicitly set it since it's hard to tell JSON from plain text apart
```go
w.Header().Set("Content-Type", "application/json")
w.Write([]byte(`{"name": "Audry"}`))
```

> [!tip]
> Good practice to use `net/http` [constants](https://pkg.go.dev/net/http#pkg-constants) instead of strings like POST or typing integer status codes to prevent typos and make code self-documenting
> ```go
> w.Header().Set("Allow", http.MethodPost) // instead of passing str "POST"
> http.Error(w, "Method not allowed.", http.StatusMethodNotAllowed) // instead of passing int 405
> ```
> 

##### Project structure & organization
- no right or wrong, but this is popular approach
```
├── cmd
│  └── web
│     ├── handlers.go
│     └── main.go
├── go.mod
├── internal
├── swap.go
└── ui
   ├── html
   └── static

```
- `cmd` - application specific code for executable applications in the project
- `internal` - has special meaning in Go -- only the project can access packages  these even if the project is an external module. 
	- good place for non-app specific code that may be reused 

Benefits:
- Go code is separated from non-go code
- easy place to add other executables, like a CLI `cmd/cli`

#### File Server
Features:
- automatically sanitizes request paths (runs `path.Clean()` before searching for a file) to remove `.` or `..` from URL path and prevents directory traversal attacks. 
- supports [range requests](https://benramsey.com/blog/2008/05/206-partial-content-and-range-requests/)(download chunks of bytes)
- `Content-Type` automtically set from the file extension
##### Serving HTML and Static files
- created html base template and partials under the `ui/html` folder
- import the `html/template` package to generate HTML by parsing a slice of html files (our templates) and then executing the base template.
- `net/http` package has built-in `http.FileServer` handler to serve static files over HTTP from a specific directory.
	- we put this before all of the other handler functions
- use `http.ServeFile()` to serve a single file from within a handler

#### Concurrency in Go Applications
All incoming HTTP requests are served in their own goroutine, aka code called by handlers are running concurrently. We like that each goroutine is separate from the Go HTTP server running, because any panics won't bring down the entire app server 😄

⚠ Be aware of race conditions when handlers are accessing shared resources 

## Chapter 3: Configuration and error handling
Goals:
1. set configuration settings at run time using cli flags
2. app log messages to be more descriptive
3. make dependencies available to handlers in extensible, fype-safe, test-friendly way
4. centralize error handling for DRYness

#### Configuration settings
###### Command line flags
- Import the `flag` package to configure command line flags and default values
- also comes built-in with `-help` cmd line flag to list all flags

Methods that automatically converts cmd line flag value to proper type and returns a pointer to %v type 
- `flag.String(flagName, defaultValue, helpfulMessage)`
- `flag.Int(...)`
- `flag.Bool(...)`
- `flag.Float64(...)`

```go
addr:= flag.String("addr", ":4000", "HTTP network address")
flag.Parse() // parse flags after all defined and save into variable
log.Printf("starting server on %s", *addr)
```

To parse flags into the memory address of pre-existing variables like a config struct:
```go
type config struct {
	addr string
	staticDir string
}
flag.StringVar(&config.addr, "addr", ":4000", "HTTP network address")
flag.StringVar(&config.addr, "static-dir", "./ui/static", "Path to static assets")

flag.Parse()
```

###### Environment variables
- use `os.Getenv("VAR_NAME")`
- returns an empty string if env var doesn't exist, so you don't get ot set a default
- *always* returns a string, so you'll have to manually convert

#### Leveled Logging
- separate informational logging vs error logging by creating two custom loggers using the `log` package

Create a new instance of Logger
```go
// create a new logger with custom prefix that writes to stdout
// includes local date and time
infoLog := log.New(os.Stdout, "INFO\t", log.Ldate|log.Ltime)

// use log.Lshortfile to incl relevant file name and line number
errorLog := log.New(os.Stderr, "ERROR\t", log.Ldate|log.Ltime|log.Lshortfile)

mux := http.NewServeMux()
```

by default, `http` server uses standard logger. To use new custom errorLog, we need initialize a new `http.Server` struct containing the configuration settings for our server
```go

// initialize new http.Server struct and pass in our custom error logger
srv := &http.Server{
	Addr: *addr,
	ErrorLog: errorLog,
	Handler: mux,
}

infoLog.Printf("Starting server on %s", *addr)
// call ListenAndServe() method on new http.Server struct
err := srv.ListenAndServe()
```
``

> [!Tip] [Logger](https://pkg.go.dev/log#Logger) methods
> Rule of 👍: Avoid using `Panic()` and `Fatal()` variations outside of `main.go`. Good practice is to return errors.

#### Dependency injection & centralize error handling
Problem: handler functions in `handlers.go` need access to dependencies like our custom error logger functions

Possible Solutions:
~~1. Make dependencies a global variable~~
1. Create a custom `application` struct and [|inject the dependencies](./Dependency%20Injection.md), then define handler functions as methods against `application`
Now, the `app` will always have access to the error loggers, and since handler functions are now methods of app, they too will have access to error loggers!

Refactor error handling code into a `helpers.go` as methods against `application`
- `app.serveError()` - output the stack trace error and return HTTP 500 error response
- `app.clientError()` - takes a http status code return a given status code to the user 
- `app.notFound()` - wrapper around `clientError` that sends 404 response code

Refactor routes to `routes.go` as a method against `application`
- `app.routes()` - creates and returns the http server multiplexer with registered URL paths
## Ch 4 Database-driven responses
1. Setup [MySQL](./MySQL.md) & download the Go driver by [external package](./Go%20Lang%20Packages.md#Importing%20External%20Packages)
2. [Create a database connection pool](Book%20Let's%20Go.md#Create%20a%20database%20connection%20pool)
3. [Design database model](Book%20Let's%20Go.md#Design%20database%20model)
4. [Execute SQL statements](Book%20Let's%20Go.md#Execute%20SQL%20statements) using Go's `database/sql` package
	1. single record queries
	2. multiple record queries

#### Create a database connection pool
```go
// openDB() function wraps sql.Open() and returns a sql.DB connection pool for a given DSN
func openDB(dsn string) (*sql.DB, error) {  
   db, err := sql.Open("mysql", dsn)  
   if err != nil {  
      return nil, err  
   }  
   // create a connection and check for any errors  
   if err = db.Ping(); err != nil {  
      return nil, err  
   }  
   return db, nil  
}
```
`sql.Open(driverName, dsn)` does *not* create any connections, it initializes a **pool** for future use.
- dsn = data source name (aka *connection string*) which describes how to connect to your db
	- note: format will depend on database type and driver, so check driver specific docs
	- `parseTime=true` is driver-specific parameter that tells driver to convert SQL TIME and DATE fields to Go `time.Time` objects


> [!Hint] Go apps create long-lived connection pools, not connections
> Typical to initialize connection pool in `main()` and pass the pool to handlers.
> - Go manages the connections in this pool as needed, auto opening/closing connections to the database via the driver

#### Design database model
Create a data access layer to encapsulate code for working with MySQL in a separate package from the rest of the app


Steps:
1. Create a `Snippet` struct that will hold data for an individual snippet (e.g. SQL row), like a Snippet. Each struct field corresponds to a SQL column.
2. Create a `SnippetModel` struct, which will have methods for us to access and manipulate the snippets in the database. 
	1. create skeleton of SQL-related methods on the model (e.g. insert, get, etc.)

To use the `SnippetModel`, it must be injected as dependency into `application` struct so it's available to handlers. 

➕  separation of concerns - database logic is not tied to handlers.
➕ Handlers do HTTP stuff and don't need to know about how db calls are made  

#### Execute SQL statements
Three different ways to execute SQL statements:
- [DB.Query()](https://pkg.go.dev/database/sql/#DB.Query) is used for `SELECT` queries which return multiple rows.  
- [DB.QueryRow()](https://pkg.go.dev/database/sql/#DB.QueryRow) is used for `SELECT` queries which return a single row. 
	- takes a sql statement and primary id to look up and returns a `row`
	- must call `row.Scan()` to copy the row into a destination struct
		- will return a `sql.ErrNoRows` if no matching record found. 
- [DB.Exec()](https://pkg.go.dev/database/sql/#DB.Exec) is used for statement which don’t return rows (like INSERT and DELETE).
	- creates a [prepared statement](https://en.wikipedia.org/wiki/Prepared_statement) (ready to take parameters) based on the plain-text SQL statement you pass in with parameter placeholders
		- `INSERT INTO snippets (title, content) VALUES (? ?)`
	- Exec() then passes parameters values to db & executes. Prevents SQL injection because *intent* of SQL statement can't change (it's been precompiled!)
	- deallocates the prepared statement


> [!NOTE] Prepare Statements
> Exec(), Query(), and QueryRow() methods set up prepared statements behind the scenes on database connection, run it with parameters provided, then close the prepared statement. 
> To avoid recreating same prepared statements everytime, consider using [DB.Prepare()](https://pkg.go.dev/database/sql/#DB.Prepare) to create complex or repeated statements for re-use to reduce effect on run time.
> *See the end of chapter 4 for an example.*


#### What's the relationship between HTTP server, app, and model?

![Drawing 2022-12-31 19.00.39.excalidraw](./images/Drawing%202022-12-31%2019.00.39.svg)

## Chapter 5: Dynamic HTML templates
Chapter goals:
- pass to HTML templates
- use `html/template` package to control display of dynamic data
	- automatically escapes data yielded between `{{}}` to prevent [Web Security > Cross site scripting (XSS)](./Web%20Security.md#Cross%20site%20scripting%20(XSS))
- create template cache so templates aren't read from disk each HTTP request
- handle template rendering errors at runtime
- DRY pattern for passing common dynamic data
- create custom functions to format and display data in HTML templates

  ### Display dynamic data
  - update handler functions to use the dynamic data fetched from the database and inject it into HTML templates

> [!hint]
>   Go's `html/template` package only allows to pass ine one item of dynamic data into a template, thus we wrap all of our pieces of data inside a new struct. Now we can pass as many pieces of dynamic data as we want to HTML template. 

  
### Template actions and functions
- Go has template actions that add logic to rendering template data, similar to Pug or Mustache
### Caching templates
- Cache parsed templates to in-memory map so we only have to do it once instead of inside every handler function that wants to generate a page with dynamic data, PLUS the base template and any partial templates
- add helper function DRY render templates from cache 

### Handling runtime errors
- we need to handle runtime errors that may occur in our template actions and functions (since these *will not* cause compile-time error)

In book example, error caused by `{{ len nil }}`  returned half finished HTTP response with 200 ok status, bad!
```bash
$ curl -i "http://localhost:4000/snippet/view?id=1"

HTTP/1.1 200 OK  
Date: Tue, 01 Feb 2022 09:20:49 GMT Content-Length: 762  
Content-Type: text/html; charset=utf-8

<!doctype html> <html lang='en'>

<head>  
<meta charset='utf-8'>  
<title>Snippet #1 - Snippetbox</title>

<link rel='stylesheet' href='/static/css/main.css'>  
<link rel='shortcut icon' href='/static/img/favicon.ico' type='image/x-icon'>

<link rel='stylesheet' href='https://fonts.googleapis.com/css?family=Ubuntu+Mono:400,700'> </head>

<body> <header>

<h1><a href='/'>Snippetbox</a></h1> </header>

<nav>  
<a href='/'>Home</a>

</nav>  
<main>

<div class='snippet'>  
<div class='metadata'>

<strong>An old silent pond</strong>

<span>#1</span> </div>

Internal Server Error
```

Solution: make the template rendering a two-stage process.
1. "trial" render by writing template into a buffer. If this fails, we can respond with error message 
2. If success, *then* write to http.Response
### Common dynamic data
- Use case: data you want on every page, like name and profile picture or CSRF token

Solution:
1. Add the fields  to the  `templateData` data struct
2. Create an `App.NewTemplateData()` init method returns a new `templateData` instance with the default values set

```go
type templateData struct {
	CurrentYear Int
	...
}

func (app *application) NewTemplateData() *templateData {
	return &templateData{
		CurrentYear : time.Now().Year()
	}
}
```
### Custom template functions
Create a custom function to use in Go templates, like `humanDate()` which outputs datetimes in pretty format. Registered template functions can now be invoked like built-in template functions in the html files.
```go
 // --templates.go 

humanDate(t time.Time) string {  
   return t.Format("02 Jan 2023 at 15:04")  
}  
  
// Initialize template.FuncMap object and store it in a global variable. This is a lookup between names of custom template funcs and funcs themselves.  
var functions = template.FuncMap{  
   "humanDate": humanDate,  
}

func NewTemplateCache() (map[string]*template.Template, error) {  
	...
	
   for _, page := range pages {  
      // Create a new blank template, then register template.FuncMap before parsing base template into template set  
	  ts, err := template.New(name).Funcs(functions).ParseFiles("./ui/html/base.html")  
      if err != nil {  
         return nil, err  
      } 
	...  
}
```

1. create `template.FuncMap` object containing the custom function
2. use `template.Funcs()` method to register custom funcs to template in the `template.FuncMap` *before* calling ParseFiles().

> [!info]
> 
> Custom template functions can *only return one value* (or two if you want to return an error).

## Chapter 6: Middleware
Shared functionality between many or all HTTP requests like logging, compressing every response, or checking a cache before passing a request to handlers. 

Learning goals:
- idiomatic pattern for building using custom middleware compatiable with `net/http` and many third-party packages
- create middleware to:
	-  set security headers for http responses
	- log requests received
	- recovers panics for graceful handling
- create composable middleware chains to organize middleware functions 

### How middleware works 

> [!Summary] Go web apps are a chain of `ServeHttp()` methods being called one after another
> When server receives a new HTTP request, it calls servemux's `ServeHTTP()` method, which looks up relevant handler based on URL path, and then calls *that* handler's `ServeHttp()` method. Middleware is just another handler function in this chain. 

```go
func myMiddleware(next http.Handler) http.Handler {  
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		// TODO: Execute our middleware logic here...
		next.ServeHTTP(w, r) // pass control to next

		// Optional: execute logic on the way back up the chain
	})
}
```
`myMiddleware()` is a function that accepts the next handler in a chain as a parameter and *returns a handler* that does some logic, then calls the next handler. 

##### Positioning matters & control flow
- Positioning middleware before the servemux will invoke it on *every request*
- positioning middleware *after* the servemux by wrapping a specific app handler to only execute for specific routes

> secureHeaders--> servemux --> app handler --> servemux --> secureHeaders

- After last handler in chain returns, control is passed back up the chain in reverse direction. 
- Returning early from a middleware func (before calling `next.ServeHTTP()`) stops the chain from being executed and flow control goes back upstream. 
	- Useful if a call to authorization middleware fails 

#### Set security headers
- add middleware *before* routing (i.e. for every http request) by wrapping the instance of our servemux router
#### Request logging
Middleware can also be defined as methods on a struct, like `app`, to get access to app dependencies (e.g. our custom `infoLog` function)
- should have the same signature as standalone middleware functions

> app.logRequest --> secureHeaders--> servemux --> app handler --> servemux --> secureHeaders --> app.logRequest


#### Panic recovery
- Go's HTTP server assumes a panic in a handler is isolated to the goroutine serving the active HTTP request because of [how Go uses concurrency](Book%20Let's%20Go.md#Concurrency%20in%20Go%20Applications)
 
Solution:
1. create middleware which *recovers* panics and calls `app.serverError()` helper method to return a `500` status instead of an empty HTTP response. 
2. Since we want this middleware functionality for all requests, wrap the existing middleware chain in `routes.go`

> app.recoverPanic --> app.logRequest --> secureHeaders--> servemux --> app handler --> servemux --> secureHeaders --> app.logRequest --> app.recoverPanic

Notes:
- `app.recoverPanic()` middleware only recovers panics that happen in the same goroutine as the middleware (aka http request passed through our app)
- Any new goroutines spun up separately (say, by a handler function) would need to implement its own panic recovery

### Composable middleware
Using the minimalist `justinas/alice` third party package to make it easier to compose middleware chains
[justinas/alice](https://github.com/justinas/alice#why)

Alice provides a convenient way to chain your HTTP middleware functions and the app handler.

In short, it transforms
```go
Middleware1(Middleware2(Middleware3(App)))
```
to
```go
alice.New(Middleware1, Middleware2, Middleware3).Then(App)
```

## Chapter 7: Advanced routing

Goals:
- update routes to properly differentiate between GET vs POST `/snippets/create` 
- restrict info-only routes to only support GET requests (via method-based routing)
- use clean URLs e.g. RESTful resource representation

Since we will want to use method-based routing, we are going to use a third party package beyond http/net.

Author recommends the below three third party routers
- **julienschmidt/httprouter**
- go-chi/chi
- gorilla/mux.

### Using httprouter
Now using httprouter, we can utilize url pattern matching and method-based routing, so that we don't have to check for either of these two things inside the handler function itself.

Before:
```go
// router.go
mux.HandleFunc("/", app.home)
mux.HandleFunc("/snippet/view", app.snippetView)
mux.HandleFunc("/snippet/create", app.snippetCreate)

// handler.go
func(app *application) snippetCreate(w, r) {
	if r.Method === 'POST' {
		// create a new snippet in database
	} else if r.Method === 'GET' {
		// render HTML form to create a snippet
	}
}
```
After:
```go
// router.go
router.HandlerFunc(http.MethodGet, "/", app.home) // matches EXACT "/"
router.HandlerFunc(http.MethodGet, "/snippet/view/:id", app.snippetView)
// Two distinct handlers depending on method
router.HandlerFunc(http.MethodGet, "/snippet/create", app.snippetCreate)  
router.HandlerFunc(http.MethodPost, "/snippet/create", app.snippetCreatePost)
```


## Chapter 8: Processing forms
Workflow for processing form = Post-Redirect-Get pattern

Learning goals:
- how to parse and access form data sent in POST request
- techniques for doing common validation checks on form data
- patterns for alerting users to validation failures and repopulating form fields with previously submitted data
- keep handlers clean by using helpers for form processing and validation

### Updating snippetCreatePost handler

1. Add field for `Form` in `templateData` struct so we can pass a Form back to template in the `app.render()` method
2. use a `snippetCreateForm` struct to encapsulate the form data submitted and any validation errors in a `fieldErrors` map.
	1. Field names must be capitalized in order to access them from a template
3. Validate form data
	1. If errors, render the create form HTML, passing the `snippetCreateForm` object as part of response body to repopulate the HTML template with previously submitted data and info about errors
4. Parse form data
5. Use form data for db insert
6. Redirect client to snippet/view for resource just created

### Create validation helpers
Refactor form validation out of handlers into a package
- create `internal/validator` package to abstract validation behavior and reduce boilerplate in handlers 
- `Validator` struct holds the map of `FieldErrors`, plus define methods to check against the map and add to it.
```go
// validator.go
 type Validator struct {  
   FieldErrors map[string]string  
}
// ...methods
```

Embed a `Validator` instance in our `snippetCreateForm` struct, then use it to perform checks on form data.

[embedding struct in structs](./Embedding%20in%20Go%20Lang.md#Struct%20in%20Go%20Lang%20Structures%20&%20Interfaces%20Structs%20structs)
Embedding a `Validator` struct inside the `snippetCreateForm` struct will give the `snippetCreateForm` access to fields and methods defined on `Validator`!   

### Automatic form parsing
- use third party package go-playground/form to automatically decode forms to save 
- decoding the form = telling how to map HTML form values into different struct fields (using struct tags) without manually having to make the mapping
	- helpful when you have many forms, or have very large forms with many fields

Solution:
- create a new instance of a decoder and inject it as dependency into app
- refactor form parsing, form decoding, and invalid decoder error checking into a helper method 

Without decoding package, must map each form field to the request form value
```go
type snippetCreateForm struct {  
   Title               string  
   Content             string  
   Expires             int  
   validator.Validator
}

func (app application) snippetCreatePost(w, r) {
	//...
	expires, err := strconv.Atoi(r.PostFormValue("expires"))  
	if err != nil {  
	   app.serverError(w, err)  
	   return  
	}
	form := snippetCreateForm{  
	   Title:   r.PostFormValue("title"),  
	   Content: r.PostFormValue("content"),  
	   Expires: expires,  
	}
	//...
}
```

With decoding package:
```go
  type snippetCreateForm struct {  
   Title               string `form:"title"`  
   Content             string `form:"content"`  
   Expires             int    `form:"expires"`  
   validator.Validator `form:"-"` // anonymous Validator type; "-" means ignore field during decoding  
}  
  
func (app *application) snippetCreatePost(w, r) {  
   // ...
   var form snippetCreateForm  
	// decode form, takes care of type matching too
   if err := app.formDecoder.Decode(&form, r.PostForm); err != nil {  
      app.clientError(w, http.StatusBadRequest)  
      return  
   }
   //...
```
## Chapter 9: Sessions
- add sessions to share state between http requests
- add sessions table in mySQL database as the session store
- use `alexedwards/scs` HTTP session management [package](https://pkg.go.dev/github.com/alexedwards/scs/v2#section-readme)

Solution:
- inject session manager as dependency to app so our handlers can access it
- specifically, we want to use the [LoadAndSave()](https://pkg.go.dev/github.com/alexedwards/scs/v2#SessionManager.LoadAndSave) middleware on stateful API routes to automatically loads and saves session data for the current request, and communicates the session token to and from the client in a 🍪

### Working with session data
Goal: share a message that says a user successfuly created a new snippet between the `snippetCreatePost` handler and redirecting to viewing created snippet. 

Session data store is a key/value store.
- To write data to session store, use methods like `Put()` to add/update a key. 
- To read and remove data from session store, use `PopString()` method.


req w/ cookie (token)--> app middleware looks up token in sql session --> returns session data if token not expired --> adds session data to req context so handlers can access it

if handler updates session data, request context is also updated. LoadAndSave() middleware updates the sql database with changes before it returns 

## Chapter 10: Security improvements
TODO - skipped

## Chapter 11 : User authentication
Goals:
- add routes for only registered, logged in users can create new snippets
- non-logged in users can view snippets or sign up for an account

Workflow
1.  A user will register by visiting a form at /user/signup and entering their name, email address and password. We’ll store this information in a new users database table (which we’ll create in a moment).
2.  A user will log in by visiting a form at /user/login and entering their email address and password.
3.  We will then check the database to see if the email and password they entered match one of the users in the users table. If there’s a match, the user has authenticated successfully and we add the relevant id value for the user to their session data, using the key "authenticatedUserID" .
4.  When we receive any subsequent requests, we can check the user’s session data for a "authenticatedUserID" value. If it exists, we know that the user has already successfully logged in. We can keep checking this until the session expires, when the user will need to log in again. If there’s no "authenticatedUserID" in the session, we know that the user is not logged in.

### User signup & password hashing
Using [bcrypt](./bcrypt.md) go library to generate a hashed password. The `12` is the cost, e.g. `2^12 = 4096` operations performed to generate the password.
- Higher the cost, the more computational expensive for hackers to guess, but also more resource intensive to generate
- balancing act

```go
// generate hash
hash, err := bcrypt.GenerateFromPassword([]byte("my plain text password"), 12)
// check if plaintext and hash match
bcrypt.CompareHashAndPassword(hash, byte[]"my plain text password")
```

- Add form validation for email and password
- Do some Error handling if user tries to sign up with a duplicate email (violates SQL `UNIQUE`)

### user login
- create a login template and render the template with an empty `userLoginForm` struct on GET route
- add standard form validation for empty fields etc. 
- add an authentication method to the `UserModel` which validates email and password or returns `ErrInvalidCredentials`
- POST route calls `UserModel.Authenticate()`, reroutes to create snippet view if successful
- Renew session data and save the authenticated userID in session store

### user authorization
- dynamically change the nav bar depending on whether user is authenticated or not --> need to pass auth data to templates
- only certain routes allowed to authenticated users 

Solution:
- `isAuthenticated(http request)` helper method on `app` that checks the session store for presence of `authenticatedUserID`
	- since the session data is passed between http req/response cycles, we need to pass the request context  
- add a boolean field in templateData struct that indicates whether or not the user is authenticated (therefore should the "create snippet" or "login" appear in nav)
- 
create a middleware that redirects to the login page if not authenticated
- reorganize the routes into unprotected and proctected routes

### csrf protection
cross site request forgery occurs when a malicious website steals your session data and can perform actions like an authenticated user.

Defensive measures:
1. use attribute "SameSite=Lax" means session cookie won't be sent by users browser on cross-site POST/PUT/DELETE requests. This is relatively new attribute.
2. Use CSRF token check method with a third party package like `gorilla/csrf` or `justinas/nosurf`, aka double-submit cookie pattern
	1. Package create a cryptographically secure token and stored in a CSRF cookie that is included in a hidden field on every HTML form. 
	2. The package then checks that the midden field value and cookie match on form submit.

- add CSRF token check as a middleware function
- to successfully submit a form, use `nosurf.Token()` to get the CSRF token and add it to hidden `csrf_token` field in each form by adding as a field on `TemplateData` struct
- update HTML forms to include this field 

## Ch 12: using request context
[Golang Context](./Golang%20Context.md) is broader than just http requests. 

What is request context:
- object to store information during lifetime of http request-response cycle
- share info between handlers/middleware, like auth status

Add data to request context --> create a new *copy* with new context info
```go
ctx := r.Context() // get existing context from a request

// create a new copy of existing context with new key and value
ctx = context.WithValue(r.Context(), "isAuthenticated", true)

// create a copy of the request containing *new* context
r = r.WithContext(ctx)
```

Retrieving values from request context --> make sure to assert to original type
```go
isAuthenticated, ok := r.Context().Value("isAuthenticated").(bool)
if !ok {
	return errors.New("could not convert value to bool")
}
```

### Use request context for authentication for more robust auth checking

Solution: add check of logged in user id to database user id once in the middleware and then pass it down to remaining middleware 
- create a custom `contextKey` string type named `isAuthenticatedContextKey` (to avoid key collisions stored in context by using regular string keys)
- create authenticate middleware method that:
	- retrieves user ID from seession data (from login handler)
	- checks the database if user ID is valid in users db by calling `UserModel.Exists()`
	- Update request context to include `isAuthenticatedContextKey` = true if user id is validating in the user db
	- refactor `app.isAuthenticated()` helper to check the request context instead of session data

Recap: 
1. user logs in --> DB validates email and password. Generate session ID and add the validated userID to session store 
2. for protected routes to create snippets, `requireAuthentication` middleware calls `isAuthenticated` which checks request context if auth is true or false 
3. `authenticated` middleware validates the authenticateduser ID in session store to a user id in database for each request and response cycle and writes to context to feel login "stateful" 
	1. is part of the dynamic middleware chain (basically all routes)
	2. if user isn't authenticated, pass control to next middleware without updating auth key in request context

## Chatper 13: Embedded files & generics
The `embed` package allows you to embed external files into the Go program itself so that it's self contained in the binary executable. 

Goal of this chapter:
- configure app to serve static CSS/JS and image files from the embedded filesystem instead of reading them from disk at runtime
- embed HTML templates

## Chapter 14: Testing

### Unit tests
- use "table-driven testing" by running through a slice of test case, where the input and expected values are defined
- use `t.Run()` to run each sub-test case in the slice of cases
- 
### Testing handlers
Use the built in `net/http/httptest` package for testing handlers

Pattern:
1. create a `http.ResponseRecorder` to pass to a handler function  instead of `http.ResponseWriter`
	1. records response headers, body, and status codes to inspect during tests 
2. create a dummy `http.Request` 
3. pass the dummy request and response recorder to the handler function we're testing
4. call `http.ResponseRecorder.Result()` method to access what was written to the response object by the handler
5. Make assertions on the expected response headers, body, and status code.


Follow a similar pattern to test middleware functions.
- May need extra step in creating dummy "next" middleware functions to satisfy the middleware function signature we're testing

#### Example of testing middleware function
```go
func TestSecureHeaders(t *testing.T) {
	rr := httptest.NewRecorder()
	req, err := http.NewRequest(http.MethodGet, "/", nil)
	if err != nil {
		t.Fatal(err)
	}
	// create and pass a mock HTTP handler to secureHeaders middleware that writes to the response body
	next := http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("next handler was called"))
	})
	
	// Mock the middleware chain
	// Because secureHeaders returns a http.Handler, call the ServeHTTP() method with response recorder and dummy req
	secureHeaders(next).ServeHTTP(rr, req)
	res := rr.Result()

	// Check that middleware correctly set headers on responses
	tests := []struct {
		header   string
		expected string
	}{
		{
			header:   "Content-Security-Policy",
			expected: "default-src 'self'; style-src 'self' fonts.googleapis.com; font-src fonts.gstatic.com",
		},
		{
			header:   "Referer-Policy",
			expected: "origin-when-cross-origin",
		},
		{
			header:   "X-Content-Type-Options",
			expected: "nosniff",
		},
		{
			header:   "X-Frame-Options",
			expected: "deny",
		},
		{
			header:   "X-XSS-Protection",
			expected: "0",
		},
	}
	
	for _, test := range tests {
		t.Run(test.header, func(t *testing.T) {
			assert.Equal(t, res.Header.Get(test.header), test.expected)
		})
	}
	
	// Check that middleware correctly called the dummy `next` handler
	assert.Equal(t, res.StatusCode, http.StatusOK)
	defer res.Body.Close()
	body, err := io.ReadAll(res.Body)
	if err != nil {
		t.Fatal(err)
	}
	bytes.TrimSpace(body)
	assert.Equal(t, string(body), "next handler was called")

}

```
### End to end testing
Goal:
- more confidence that app is working by encompassing routing, middleware, and handlers
- systematically make GET http requests to specific app routes by spinning up a test server and app
	- `httptest.Server` package allows us to create a new test server that accepts any `http.Handler`. From this server, we can create `http.Client` to send requests to test server.
Solution:
- `testutils_test.go` factors out helper funcs that create a test app and test server with methods to make http requests to for each end to end test.
	- 
- configure test server client settings:
	- automatically store cookies sent in a https response to send back in subsequent requests back to test server (e.g. for testing anti-CSRF)
	- stop automatic redirects to return first https response sent by our server for testing purposes

### mocking dependencies
Goal: mock app dependencies such as:
	1. loggers
	2. DB models that make queries

To accomplish #2:
1. create a `mocks` package
2. create a "mock" `SnippetModel` that implements the same methods as the prod version of `models.SnippetModel` but returns fixed dummy data instead
3. Initialie mocks - Update the `newTestApplication()` function in `testutils_test.go` so that the app struct created for testing uses these dependencies instead.

For our `app` to be able to take either our production  `model.SnippetModel` or our `mock.SnippetModel`, we create a `SnippetModelInterface` for both to implement and update our `app`


> [!Tip] Interfaces *are* pointers
> Interfaces are a data structure that hold pointers to the underlying *concrete* type (e.g. `model.SnippetModel`), therefore it's syntatically incorrect to have a pointer to an interface (e.g. `*model.SnippetModelInterface`).
> Under the hood, interfaces also have a pointer to a slice of functions. ChatGPT made me a diagram but i lost it 😔

#### end to end testing handler
- See the `TestUserSignup` function.
	- simulate the GET request to the signup html to get a valid CSRF token
	- simulate the POST request with the form data

### integration testing 
‼️ skipped

