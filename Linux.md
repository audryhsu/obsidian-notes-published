---
creation date: 2022-01-26 10:36
aliases: 
tags: 🖥️
---

# [Linux](Linux.md)
---
## Background
🔴 linux vs unix

Historically


## Basics
Standard streams are interconnected input and output communication channels between a computer program and its environment. Back in the day, `stdin` was the keyboard, and `stdout` pointed to a monitor.

Three input/output (I/O) connections:
- `stdin` - standard input stream, accept text to read
- `stdout` - text output from command to shell is delivered via standard out, write
- `stderr` - dedicated stream for error messages from command; 
	- usually redirected to a log file for analysis and stops the error messages from contaminating the file that `stdout` has been redirected into.

**Pipeline or piping** refers to a set of processes chained together by their standard streams. The `stdout` is passed to the `stdin` for the next process in a unidirectional manner.

In code, it's represented by the pipe:
```
ls -l | grep key | less
```
Each | tells the shell to connect the `stdout` of command on the left to the `stdin` of the command on the right.


# Utilities

**make a script executable**
```
chmod +x <file_name>
```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference: [Standard Streams](https://en.wikipedia.org/wiki/Standard_streams)

Related: 