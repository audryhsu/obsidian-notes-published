---
creation date: 2022-04-22 17:18
aliases:
  - WebSockets
tags: 🖥️
---

# [WebSocket API](WebSocket%20API.md)
---
 A Web API that enables bidirectional communication, which allows a client and server to establish a persistent [TCP](./TCP.md) connection and both side can independently send messages without needing a request.
 - ==a completely different protocol than HTTP==
-  doesn't concern itself with buffering, parsing, or reconstructing received data = low latency for text and binary data
-  simplicity is tradeoff, as the application must account for missing state management, compressing, caching, etc. other things provided by browser usually
-  not a replacement for HTTP, XHR, SSE 
- Starts as HTTP, then upgraded after handshake

Diagram from WebSocket's documentation:
![Pasted image 20211222103259.png](./images/Pasted%20image%2020211222103259.png)
- WebSockets is the technology, and Socket.io is the library that provides an abstraction layer on top of websockets.


How to use Websockets in a fullstack app with Socket.io:
1. Import socket.io server library
2. Create a new server instance `io`, which manages Socket instances and their rooms
3. Websocket server listens for messages and can send messages 

Front end client code:
1. Import socket.io client library
2. Create a new socket connection and point to backend server location/port
3. Write socket event listener and callback of what to do when client receives a message from server
4. Socket.io has `emit` function that broadcasts a message between all websocket clients.  

- each new client socket connection have a unique id

[With Express:](https://www.npmjs.com/package/socket.io)
- You need to pass the Server to socket.io, and not the express application function. Also make sure to call .listen on the server, not the app.



---
Tags: [Programming](Programming.md) - [Web API](Web%20API.md)

Reference: https://dev.to/bravemaster619/how-to-prevent-multiple-socket-connections-and-events-in-react-531d

Related: 