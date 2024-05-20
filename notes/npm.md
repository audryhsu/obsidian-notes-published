---
creation date: 2021-12-07 17:24
aliases: 
tags:
  - 🖥️
publish: true
---
Tags: [JavaScript](../JavaScript.md) - [NodeJS](./NodeJS.md)

## # TLDR
- we use `npm` to manage project dependencies (packages/modules needed)
- packages can be used as command-line tools (eslint and jest) or as a programming interface that needs to be imported with `require` (e.g. readline-sync)
	- best to install dependencies locally in project node_modules folder to keep version integreity.
- upload package-lock.json so other developers can download the precise versions of depedencies needed to run your code properly
- Babel is a transpiler that complies code written with newer syntax inter older code. We can use Babel's CLI with the Babel core library and required presents to transpile into older version of JS.

# Packaging a project
#### node project directory layout
most common standard for Node projects is npm standard.
```
├── lib
│   ├── todo.js
│   └── todolist.js
└── test
    └── todolist.test.js
```

When we import modules with `require`, we can provide a relative path because Node looks in the `node_modules` directory for a folder with the same name as the argument. 

When you `npm install <package> --save`, `npm` will look up the directory hiearchy until it finds a `node_modules` folder or create one where you ran `npm install` if it doesn't find one.

```ad-note
Make sure that the super folders do not have their own `node_modules`, package.json/jackage-lock.json or else the module may get installed above your project folder

```


#### The `package.json` and `package-lock.json`
Preferred way to install depedencies for projects is to list them in the `package.json`
```ad-summary

 `package.json` : is a configuration file for your project; all the packages that your project needs, together with versions of each package. include this and package.json in git repo -- this is the file that `npm install` looks at to get the right versions when others want to clone your project

```

- use `npm init` to initialize a package.json file
- add key value paris of dependencies and major versions to file
- `npm install` in the CLI to automatically install the packages listed in `depdencies` in `node_modules` folder
- `package-lock.json` - automatically created after installing packages and shows the precise versions of pacakges npm installed, and their dependencies. This contains the minor versions.

**Adding dependencies**
1. Add names of packages and versions to the package.json.dependnecies property 
2. Instead of editing the package.json file, you can run `npm install` with the package name and `--save` (or `-S`) option to install the package and save it to package.json and package-lock.json.

Install and save production dependencies
```js
npm install lodash --save
```

Install and save development dependencies:
```js
npm install eslint --save-dev
```
- these are just for debugging or dev environment only
- can be added to package.json under `devDependnecies` property

**Deleting Dependencies**
`npm uninstall lodash`
- removes package from `node_modules`, but NOT from the package.json. 
	- add the `--save` or `--save-dev` option to commit to package.js
Alternative way:
- remove a package from package.json dependencies, then run `npm prune` to auto remove from` node_modules`

```ad-note

Install packages locally in project's `node_modules` directory to ensure projects specific versioning requirements are met.

```

#### Running local executable packages
`npx eslint lib/todolist.js` 
npx uses the local version of eslint to scan the todolist.js file. If it can't find it locally or globally, it downloads and uses a temporary version. 
#❓

Read: `npx` can search for and install packages for one-time execution, versus requiring pre-installed packages

#### Global Packages
Packages that provide cmd line executables like Heroku (where you don't need different versions for different projects) can be installed globally:

```js
npm install heroku -g
```


# Automating tasks with npm scripts
- define in package.json file with the `scripts` object, which takes key/value pairs where name of the script is key and value is the script to run
	- essentially a way to run preset terminal commands
	- example: transpiling code with Babel before every test run

# Packaging projects for distribution
- create an index.js file in root directory. this is the default file specified by the `main` property in the package.json that JavaScript will load when you import the package
- require and export the files in the `dist` folder into an object; Other apps that require our package will import this object!

```
├── dist
│   ├── todo.js
│   └── todolist.js
├── lib
│   ├── todo.js
│   └── todolist.js
├── index.js // require and export all the main js files
├── node_modules
├── package-lock.json
├── package.json
└── test
    └── todolist.test.js
```
---

# Examples
Importing modules from [lodash](lodash.md) module after installing locally
```js
// NOTE: saving the lodash object to _ variable, just by convention
// import 1 : all modules
const _ = require('lodash');

// import 2: only chunk module (only if packages have multiple independent files, determined by documentation)
const chunk = require('lodash/chunk');

//import 3: import all modules, chunk module available to code, and extraneous modules eligible for garbage collection
const chunk = require('lodash').chunk;

console.log(_.chunk([1,2,3,4,5,6,7,8], 2));

```

# useful npm
##### [npm link](https://docs.npmjs.com/cli/v8/commands/npm-link?v=true)
Provides a way to use a local version of a package for local developmenet, testing, and iteration.
In the local project that another project will consume like common-connect-db:
```bash
# First, `npm link` in a package folder with no arguments will create a symlink in the global folder `{prefix}/lib/node_modules/<package>` that links to the package where the `npm link` command was executed.

npm link

/Users/audryhsu/.nvm/versions/node/v18.19.0/lib
└── @ookla/common-connect-db@6.9.5 -> ./../../../../../repos/common-connect-db

```

In the consuming project:
```bash
#`npm link package-name` will create a symbolic link from globally-installed `package-name` to `node_modules/` of the current folder.

npm link @ookla/common-connect-db

# link successful 
$ /Users/audryhsu/repos/connect-admin/node_modules/@ookla/common-connect-db -> /Users/audryhsu/.nvm/versions/node/v10.24.1/lib/node_modules/@ookla/common-connect-db

# list out all symlinks
npm ls -g --link

# to unlink
```