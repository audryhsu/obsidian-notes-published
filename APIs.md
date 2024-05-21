---
creation date: <% tp.file.creation_date() %>
aliases:
  - API
  - Application Programming Interface
tags: 🖥️
---
# Basics
*Notes are derive from LS Web API book*

**Application Programming Interface** (API) provide functionality for use by another computer program.

APIs exist at many different levels:
- Web services have APIs for other services to consume their services of HTTP
- Operating systems have APIs for programs to access memory and files etc.
- Mobile devices have APIs to provide access to location or sensor data

An API **provider** is the system that provides an API for other parties to use. The API **consumer** uses the API to accomplish work e.g. consume weather forecast data 

APIs help *share data* and allow developers to *build their applications on top of a variety of other specialized systems* instead of rebuilding everything on their own. 

- **Public APIs** for consumption outside the organization (e.g. third party programs to interact with Facebook)
- **Private APIs** are for internal use for the provider, e.g. Google's search page uses a private API to get the search suggestions

Web APIs are based on HTTP request-responses, but instead of a human making requests, API requests are made from a program written by a developer. 

## Common uses for APIs
- Sharing data - for example, providing data to a mobile application (Netflix app on your phone) that needs to access data from a web-based service (your Netflix account)
- Enable automation: We can automate data entry from one application (customer orders from a web store) into a sweepstakes system as long as the web store's API grants acess to customers and order information, then the sweepstakes API can consume that information to programatically add or remove entries into the system.

In the end, APIs give developers access to specialized data or services that they don't have to build themselves; We can focus on building an app that uses those services in the way we want without having to build from scratch. 

### Parts of HTTP Response 
- **Headers**: metadata about the response, the name of the server handling it, etc.
- **Media Type**:  refers to set of shared markup languages and data formats for transferring information between computers. Tells the browser how to interpret the body content
	- HTML
	- plain
	- text/css
	- application/javascript
	- zips, pdfs, sound files, videos, etc...
 - `Content-Type: text/html` describes the format of a response's body and optionally the `charset`, which for text is usually `Content-Type: text/html; charset=UTF-8`. 
- Body: The actual [JSON](./JSON.md) or html transmitted


### [Data Serialization](./Data%20Serialization.md)
a **[Data Serialization](./Data%20Serialization.md) format** describes a way for programs to convert data into a form that is more efficiently stored or transferred and can be revert back to original data any time.
Examples of serialization formats;
- XML (extensible markup language), shares common heritage with HTML
- [JSON](./JSON.md) *most popular now*

# REST and CRUD
**REST** (representational state transfer) is a set of conventions (not rules) for how to build APIs, defined in 2000 by Roy Fielding based on formalized patterns about kind of interactions that commonly take place on the web (loading pages, submitting forms) and API design.

BLUF: convention simplifies API design for developers and reduces complexity for consumers to use. REST is ***proven way to handle common solutions*** rather than a workable solution for all possible problems.

RESTful API design will consider:
- What resource is being acted upon? (Resource (e.g. data), resource path)
- How we are interacting with the resource? (CRUD & HTTP method)

**CRUD** is an acronym that refers to Create Read Update Delete. RESTful APIs model most functionality by matching one of these operations to the appropriate resource.

We can combine CRUD operations with an HTTP method (GET, POST, PUT, DELETE) and map it to the *what* resource at some path to operate on to chart out API design. This is *Resource Oriented Thinking*.

# Working with Resources
- a **resource** represents a grouping of data, and is anything an API user needs to interact with (e.g. post, tag, comment, transaction, etc.)
	- every resource in a web API must have a unique URL to identify and access the resource
	- 
RESTful APIs interact with elements and collections:
- **elements** are representations of a single resource (e.g. one blog post with a unique id)
- **collections** represent a grouping of elements of the same type, and may have a parent-child relationship (e.g. collection of blog posts) 

#### Fetching resources
We can fetch resources by making HTTP requests to an API by providing a valid resource path, the request method, and the HTTP protocol version being used
- GET  request method usually
	- `Accept` request header: specifics what media types the client will accept in the response

#### Creating resources
We can create resources using POST requests by sending a HTTP POST request to the API path that accepts key/value pairs of required parameters to create the resource
- If successful, the response header will be a 2XX code and send back the serialized data in the response body

#### Updating resources
We can use PUT method requests and specify the resource's path and provide the parameters required by the API to update it.

#### Deleting resources
We can use DELETE method requests and specify the resource's path
- `204 No Content` is in the format _2xx_, which means the request was processed successfully. `204 No Content` is commonly used when it doesn't make sense to return anything in the response body, and deleting a resource is one such case. If there is no longer a resource at the path being accessed, there isn't anything to send back.

#### Handling Common Errors
- Missing or invalid parameters - 422 unprocessable entity
- Attempting to access a resource that doesn't exist - 404
	- Could have been deleted
	- URL could be incorrect 
	- Resource could require authentication
- Missing authentication credentials - usually these requests will receive `401`, `403`, or `404` errors, and can be resolved by sending valid credentials.
- Incorrect media type - 415 unsupported media type 
	- the content-type specified in the request header doesn't match what the API expects (usually JSON)
- Rate limiting - 403 forbidden
- Server errors - 5XX - not the API user's problem
	- bug in server impelmentation even if API usuage is correct
	- hardware or infrastructure problem

How to troubleshoot:
- Look at the response status

---
Tags: [Programming](Programming.md) - [Web Development](Web%20Development.md) 

Reference:

Related: [HTTP](./HTTP.md) - [Networked Applications](./Networked%20Applications.md)