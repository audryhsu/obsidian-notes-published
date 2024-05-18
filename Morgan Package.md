---
creation date: 2022-03-23 16:15
aliases: 
tags: 🖥️
---

# [Morgan](Morgan.md) Package
---
morgan is a HTTP request logger middleware for [NodeJS](./NodeJS.md). morgan is named after Dexter Morgan, the fictious anti-hero protagonist and serial killer.

### Configuration
We must specify a logging `format` (predefined custom) and can optionally customize additional settings.   
`morgan(format, [options])`
- `format` argument is a string reference to predefined formats in the API
	- `tiny` format is the minimal output. 
	- `dev` format is concise and color codes response status. I like this one! 

Using morgan as [ExpressJS](./ExpressJS.md) middleware
```js
const express = require('express');
const app = express()
const morgan = require('morgan');

//middleware
app.use(express.json())
app.use(morgan('dev'))
```

#### Custom Token and format
Create a custom token (must be a string) and pass in a string of logger settings
```js
// create custom token named 'body' to add request body in logger
morgan.token('body', (req, res) => {
  return JSON.stringify(req.body)
})
// pass custom morgan format as logger middleware 
app.use(morgan(':method :url :status :response-time ms - :body'))

```
---
Tags: [Programming](Programming.md) - [Web Development](Web%20Development.md) - [Backend Development](Backend%20Development.md)

Reference: [morgan API](https://www.npmjs.com/package/morgan)

Related: [ExpressJS](./ExpressJS.md)