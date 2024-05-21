---
creation date: 2022-03-24 14:01
aliases: 
tags: 🖥️
---

# [Environment Variables](Environment%20Variables.md)
---


### How to define environment variables
1. Define in the CLI when running a command like starting an express application
```bash
$ MONGODB_URI=address_here npm run dev
```
- In a similar vein, you could integrate it into the "start" script for a node project in the package.json. This example starts a React app on port 3006 instead of the default 3000 and sets two different environment variables to enable hot refresh:
```
  "scripts": {
    "start": "PORT=3006 CHOKIDAR_USEPOLLING=true FAST_REFRESH=false react-scripts start",
```

2. Using the [dotenv](dotenv.md) library
Dotenv is a zero-dependency module that loads environment variables from a `.env` file into p`rocess.env`.

- create an `.env` file and set your variables in KEY=VALUE format
- import and configure dotenv in application *at the very top of the application* to ensure other modules can access environment variables
```js
require('dotenv').config() // import
console.log(proces.env) // confirm access
```

```ad-important

REMEMBER to add `.env` file to your `.gitignore`!!!

```
We need to configure heroku with the database url since we hid the url in the .env file from git.
- this can be done in the heroku web app Configure Vars

# Examples
```js

```



---
Tags: [Programming](Programming.md) - [Web Development](Web%20Development.md) 

Reference:

Related: [Fullstack Open Project (FSO)](./Fullstack%20Open%20Project%20(FSO).md)