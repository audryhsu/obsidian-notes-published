---
creation date: 2022-03-23 14:14
aliases: 
tags: 🖥️
---

# [nodemon](nodemon.md)
---
nodemon is a tool that helps develop [NodeJS](NodeJS.md) based applications by automatically restarting the node application when file changes in the directory are detected.

nodemon is a replacement wrapper for node. To use nodemon, replace the word `node` on the command line when executing your script.

### Usage Notes
Nodemon has issues restarting the server when running on WSL:
*"In some networked environments (such as a container running nodemon reading across a mounted drive), you will need to use the legacyWatch: true which enables Chokidar's polling."*

Via the CLI, use either --legacy-watch or -L for short:
```js
nodemon -L
```

Add `nodemon` as a "dev" script in `package.json`
```js
"scripts": {
	"start": "node index.js",
    "dev": "nodemon -L index.js", //...
}
// start server in development mode
$ npm run dev 
```

---
Tags: [Programming](Programming.md) - [NodeJS](NodeJS.md)

Reference:

Related: [npm](npm.md) - [Networked Applications](Networked%20Applications.md)