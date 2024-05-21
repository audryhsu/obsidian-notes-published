---
creation date: 2021-12-20 17:20
aliases: 
tags: 🖥️
---

# [Application Layer](Application%20Layer.md)
---
Application layer protocols focus on the structure of messages (syntax) and the data it should contain. There are many different application protocols, as different types of applications have different requirements (HTTP, FTP, SMTP, etc.).

## [HTTP](./HTTP.md)
HTTP is the primary protocol used for communication on the Web, a **service** that can be accessed via the internet (network of networks). The web is a information system comprised of resources which are navigable means of a URL.

Web was conceived as a solution to accessing resources across the world to promote easier information sharing by standardizing how resources were structured for rendering (HTML), how resources were addressed so they could be easily located (URI), and how requrests for resources were made and responded to (HTTP). 
- HTML 
- URI - **uniform resource identifier** sequence of characters that identifies an abstract or physical resource
	- URLs, the subset of URIs that, in addition to identifying a resource, provide a means of locating the resource by describing its primary access mechanism (e.g., its network "location") are often used interchangably with URIs.
	- we can just say "URL".
- HTTP - set of rules which provide uniformity to how resources on the web are transferred between applications.

**Server-side infrastructure** in web development comprises of a web server, an application servier, and a data store. 
- web server: responds to requests for static assets: files, images, css, javascript.
- application server: app or business logic resides and where server-side code lives when deployed. application servers interact with the data store. 

HTTP operates at the application layer and structures messages exchanged between applications, [TCP](./TCP.md)/IP ensures that the request/response cycle gets completed between browser and server.

### URLs and Filepaths
URL schema (https) indicates the type of protocol that should be used to request the resource. In the early days of the web, the URL path represented a physical file location on teh web server and might have represented a directory hiearchy. 

In modern web use, content consumed on the web is generated dynamically (either server side or client side) by combining templates with stored data to produce HTML 'pages', forming the body of an HTTP response. The URL path is used by application logic rather than an underlying file strcuture, and involves URL pattern-matching to match the path to a pre-defined 'route' which then executes logic.  
 
## Request Response Cycle
Client - browser
Server - the applications running on the server, like a Ruby or Java  program.

Components of a HTTP request:
- Method (required)
- Path (required)
- Parameters
- Message body

Components of an HTTP Response:
- Status (required)
- Headers
- Body

**GET** requrests only retrieve content from the server.
**POST** requests are used to change values on server

## Summary of Working with HTTP
-   HTTP is a _text-based_ protocol. HTTP Request and Responses involve sending text between the client and server
-   In order for the protocol to work, the Request and Response must be structured in such a way that both the client and the server can understand them.
-   With HTTP/1.1, the end of the headers is indicated by an _empty line_.
-   The `Content-Length` header can be used to indicate the _size of the body_. This can help determine where the HTTP message should end.

---
Tags: [Programming](Programming.md) - [Networking](./Networking.md) 

Reference:

Related: 