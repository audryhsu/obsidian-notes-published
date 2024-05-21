---
creation date: <% tp.file.creation_date() %>
aliases: 
tags: 🖥️
---
Source: Notes from LS's introduction to HTML book.
# How the internet works

HTTP, hyper transfer protocol, is a protocol that links between applications and transfer of hypertext. Protocols are agreements, or message formats that dictate how computers communicate.

A client (browser) makes a **request** to a server and waits for a **response**.
- A browser can use a URL address (friendly address for an IP address)
- IP addresses can have **port numbers** (think phone extensions) for more detail on how to communicate (e.g. email ports)
- If the IP address is not cached already in your device's DNS cache, the client request is sent to the [Domain Name Service](./Domain%20Name%20Service.md) to map the URL to IP Address. ^f731f6

- The HTTP request goes over the internet to the server with the matching IP Address
- The remote server accepts the request and sends the respond back over the internet to your browser
- Browser renders the response in a web page

![Pasted image 20211202094302.png](./images/Pasted%20image%2020211202094302.png)

### HTTP is stateless
Protocols are stateless when it's designed in such a way that a request/response pair are **independent of the previous one**. The servers do not need to remember information (aka retain state) between requests, so if a request breaks en route, there is no clean up, the request can be retried.

While great for internet architecture (ease of resources) this is where developers must create applications that appear stateful for UX.

### URL
Uniform Resource locator is the most common part of the Uniform Resource Identifier (URI) that specifies how resources are located.
![Pasted image 20211202095813.png](./images/Pasted%20image%2020211202095813.png)

```ad-tip

Scheme, host, and path are required elements of a URL. Port and query parameters are optional. Port 80 is default for HTTP requests unless otherwise specified.

```


- **scheme** tells the web client how to access the resource, or what protocol to use
- port numbers may be included which the host uses to listen to HTTP requests. Default HTTP port is 80, which will be used unless otherwise specified.
- **query strings** contain paramter name/value pairs and start at `?` and pass add'l information to the server but have a max length
- `&` is a reserved character to add more parameters to query string

```
http://www.phoneshop.com?product=iphone&size=32gb&color=white
```
![Pasted image 20211202100002.png](./images/Pasted%20image%2020211202100002.png)



> URLs are design to only accept certain characters in standard 128-character ASCII character set. Reserved or unsafe characters must be **encoded** to replace the non-conforming characters with `%` followed by hexadecimal digits that represent ASCII code.

- unsafe characters may be misinterpreted by some systems (spaces, quotation marks, the `#` character, `<` and `>`, `{` and `}`, `[` and `]`, and `~`, among others)
- reserved characters are used for special purposes in URL scheme (e.g. `/`, `?`, `:`, `@`, and `&`)

# Requests and Responses
### Types of Requests
- HTTP `GET` requests are most common for non-sensitive information (since parameters are visible in URL)
- `POST` requests are used when we want to send or submit data to the server, usually to a form.
	- can send larger and sensitive data, like images or videos, or usernames and passwords for authentication
	- Data is being sent through the HTTP **body**, aka the letter enclosed in the envelope rather than the URL.

Additional info is sent through the HTTP response/request **headers** that provide more details about how the response/request should be sent and more info about the resources being sent back

Common response header info to understand:
- **response status codes** : 200 OK, 302: Found, 404: Not Found, 500: Internal Server Error

# Stateful Web Apps
If HTTP protocol is stateless (does not retain information between request/response cycles and are completely independent of each other), how can we build **stateful** web apps?

Facebook seems to remember that we are logged in when we click around. Techniques to simulate stateful experience and making content dynamic:
- sessions
- cookies
- asynchronous JavaScript calls, or [AJAX](./AJAX.md)

```ad-summary
title: Cookies & Sessions

Cookies are used by the server to implement sessions to preserve app state between request/response cycles. Cookie contains the session id. 

```

### Sessions
The server sends a unique token to each client browser, so that every time the client makes a request, it can flash it's unique token, or **session identifier** (think keycard badge) as part of the request so that the server can identify the client.

Benefit: This `session id` creates a sense of persistent connect between requests, or faux statefulness from the UX side

Tradeoffs: Every request needs to check for the `session id` and ensure it's still valid based on some rules (expired or not).
- In addition, the server needs to store these `session id`s somewhere for reference and the session data based on the session id.
- server needs to recreate the application state (e.g. the HTML) from the session data associated with the validated session id and send it back to the client (e.g. validate the bookmark and return to the right page/paragraph of the book)

**Cookies** are pieces of data that are sent from the server and stored in the client/browser during the request/response cycle that contain session info. Client side cookie (local) is compared with server side session data on each request to identify current session.
- small bits of data (4kb) which hold key-value pairs are stored in client browser.

**[AJAX](./AJAX.md)** is Asynchronous JavaScript and XML - allows browsers to issue requests and process responses *without a full page refresh*.
- [AJAX](./AJAX.md) requests are like regular requests and are sent to the server with all normal HTTP request elements and handles them as normal
- Key difference: instead of browser refreshing and processing response, the response is processed by a callback function (some JS code)


## HTTP History
HTTP/1.1 WAS RELEASED IN 1997 and first standard version of HTTP; 
- allowed teh re-use of a TCP connection to make multiple requests instead of one request per connection, reducing latency overhead
- re-use of connection also allowed pipelining of requests, where multiple requests can be sent at once
- helped scale the number of requests needed as CSS and JS scripts were being linked into web pages
- limitations: HOL blocking, since messages must be responded to in order they are sent
HTTP/2 standardized in 2015
- provided multiplexing, where multiple requests can eb sent at the same time **in parallel** and do not rely on message order
- headers are more compressed

HTTP/3
- developing QUIC protocol that provides reliability and security features traditionally provided by TCP and TLS

---
Tags: [Programming](Programming.md) - [Web Apps](Web%20Apps.md)

Reference:

Related:  - [Networking](./Networking.md) - [Application Layer](./Application%20Layer.md)