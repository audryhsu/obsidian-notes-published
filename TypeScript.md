---
creation date: 2022-04-22 21:34
aliases: 
tags: 🖥️
---

# [TypeScript](TypeScript.md)
---
TypeScript extends JavaScript as a structurally typed language created by Microsoft to support building large-scale applications. **TypeScript is eventually compiled into plain JavaScript**, which means you can use it anywhere, frontend or backend.

The *compiler* is responsible for type information removal and code transformation from TypeScript to JavaScript (everything related to types is removed at compile-time) and  static code analysis to raise warnings/errors

### Why use TypeScript
1. Type checking and static code analysis reduces runtime errors and catch errors faster/earlier
2. Type annotations in code is like a code-level documentation where other devs can easily tell what kind types of inputs and outputs are expected for functions
3. IDEs can provide more specific and better suggestions when it knows what types you are processing

### Notable Features
#### Types

#### Interfaces
- create a contract that classes must follow

### Setup
TypeScript files (.ts) need to be compiled into JavaScript
```bash
// install TypeScript compiler
$ npm install typescript
$ tsc <filename> # compile a file
$ tsc --watch <filename> # watch for live changes and automatically recompile after save
$ tsc --init # create a config file
```
Config options
- `target` - ES version
- `outDir` - where to put JS files
- `rootDir` - where TS source files are


---
Tags: [Programming](Programming.md) - 

Reference:

Related: 