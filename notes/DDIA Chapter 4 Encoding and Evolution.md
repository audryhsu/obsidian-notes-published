---
tags:
  - engineering
  - designing-data-intensive-applications
  - books
aliases: 
publish: true
---
# Evolvability is a key component to long-lived code
![DDIA chapter 4 evolvability and encoding.excalidraw](../images/DDIA%20chapter%204%20evolvability%20and%20encoding.svg)
%%[🖋 Edit in Excalidraw](../images/DDIA%20chapter%204%20evolvability%20and%20encoding.svg), and the [dark exported image](DDIA%20chapter%204%20evolvability%20and%20encoding.excalidraw.dark.svg.md)%%

We need to encode (marshal, serialize) data to translate it from in-memory representation to a byte sequence to be transmitted over the wire. 
# Encoding Formats
What do we care about in encoding formats?
- space efficiency
- ==forward/backward compatibility== 
- lowest-common denominator format to pragmatically  transmit encoded data between organizations

> [!Hint]
>  **Spoiler: Encoding formats that use schemas that can evolve are very powerful**
> Schema evolution allows the same kind of flexibility as schemaless/schema-on-read JSON databases, but with better guarantees about your data!
## 1. Language specific formats  

Every programming language has built-in support for encoding in-memory objects into byte sequences 
	- not portable between applications that use different languages
## 2. Textual formats - JSON, XML, CSV
- widely known & supported
- JSON has built-in support in web browsers

Downsides:
- schemas are optional, and quite powerful (lots of validation like `min` `max` )
- CSV doesn't have any schema, so changes to schema require manual handling
- ambiguity in encoding numbers
	- JSON doesn't distinguish between integers and floats
	 - XML and CSV doesn't distinguish between number and string that happens to be digits

###  Binary encoding variants
- BSON, BJOSN, MessagePack - generally more compact and faster to parse compared to textual formats, but not by much
- make up for some shortcomings by extending datatypes by supporting floats, binary strings etc. 
- not as widely adopted as JSON and XML
- **schemas are still optional, and must include all object field names with encoded data**
## 3. Thrift (Facebook) and Protocol Buffers (Google)
- binary encoding libraries that **require a schema** for encoded data
- come with code generation tools that take a schema definition and produce classes that implement the schema in any language
![Pasted image 20231219165219.png](../images/Pasted%20image%2020231219165219.png)
![Pasted image 20231219170053.png](../images/Pasted%20image%2020231219170053.png)
Advantages:
- use of **integer field tags** (think of Go's struct tags) instead of field names to map schema fields to data fields
- extra compact: can pack the field type and field tag number into a single byte and use variable length integers
- forward compatible: new fields can be ignored by old code
- backwards compatible: new code can read old data as long as each field has a unique tag number. New fields must be optional or have a default value 
- code generation for statically typed languages for compile-time type checking

## 4. Avro
- binary encoding format that also uses defined schemas, created for Hadoop
- does *not* use field tag numbers or any identifier for fields or their data types, just the length and UTF-8 bytes
- uses a *writer's schema* and *reader's schema* and they do not have to be the same, just compatilble
- instead, Avros resolves the differences between the reader & writers schema and translates the data from the writer's schema into the reader's schema
	- if a field appears in writer's schema, but not reader's, it's ignored
	- if reader schema expects a field but writer's schema doesn't include it, it's filled with a default value declared in the reader's schema
 - writer's schema is either included (in case of large file with many records) or each record references a schema version that can be mapped to schemas saved in a database

![Pasted image 20231219170037.png](../images/Pasted%20image%2020231219170037.png)![Pasted image 20231219170646.png](../images/Pasted%20image%2020231219170646.png)
Advantages:
- friendly to *dynamically generated* schemas
- example: exporting data from relational database schemas that change over time
	- generate Avro schema based on database schema, encode data, dump it to a container file. Each table has a record schema, and each column is a field in that record.
	- after a period of time, generate a new Avro schema for the updated db schema and export data in the new schema.
	- Data export process doesn't care about schemas, since Avros will resolve the reader/writer schemas
- For Thrift or Protocol Buffers, field tags would have be updated for column changes

# Modes of dataflow
Who is doing the encoding and decoding? 
Common ways that will be discussed:
- via databases
- via service calls (APIs and RPC)
- via asynchronous messaging systems (queues)

## Dataflow through databases
![DDIA Chapter 4 Encoding and Evolution 2023-12-19 17.22.19.excalidraw](../images/DDIA%20Chapter%204%20Encoding%20and%20Evolution%202023-12-19%2017.22.19.svg)
%%[🖋 Edit in Excalidraw](../images/DDIA%20Chapter%204%20Encoding%20and%20Evolution%202023-12-19%2017.22.19.svg), and the [dark exported image](DDIA%20Chapter%204%20Encoding%20and%20Evolution%202023-12-19%2017.22.19.excalidraw.dark.svg.md)%%

> [!important] Data outlives code
>   - data in database might be there from five years ago, untouched in it's original encoding unless it's been overwritten, whereas code deploys replace all code files
>   - implication is that parts of database will be written by/read by different historical schemas

  - most relational dbs build in schema evolution so that migrations can be incremental and do not require an entire database schema re-write (e.g. when a new column is added, but an old row with old schema is read, the db default fill it with `null` value)
## Dataflow through services: REST and RPC

*service oriented architecture (SOA)* aka *microservice* architecture --> decomposing a large application into smaller services by area of functionality **to make application easier to change and maintain via independently deployable and evolvable services**

client-server is relative
- web browser -> api server
- api server -> database

Web services -> anything that uses HTTP as underlying protocol

### REST and SOAP
Designing APIs
- REST - design philosophy that builds upon HTTP
	- emphasizes simple data formats, URLs for resource identification, and using HTTP features for cache control, authentication, and content-type negotiation.
	- OpenAPI/Swagger describe RESTful APIs
- SOAP - XML-based protocol *independent* from HTTP and avoids using HTTP features. Uses the *web service framework* standards.
	- relies heavily on code generation in Web Services Description Language
	- not human readable, relies heavily on tool support like IDEs 
	- not great for dynamically typed languages 

### Remote procedure calls (RPC)
RPC model attempts to make a request to a remote network service look the same as calling a function/method in local programming language.

**RPC is fundamentally flawed because a local function call is too different from a network call:**
- network requests are unpredictable due to unreliability
- local function calls are deterministic (result, exception, or infinite loop) vs remote calls can be indeterministic (timeout) and retries could be duplicative
- network calls vary in latency
- network calls require encoding data which can be complicated for large objects vs local references in memory

Still, RPC frameworks exist and have been built on top of encodings: Thrift and Avro come with RPC support, [gRPC](https://www.youtube.com/watch?v=gnchfOojMk4) uses protocol buffers, etc.
- best use case seems to be intra-organization within data centers

## Message-passing data flow
Using a message broker/queue, or *message-oriented middleware* is great for one-way, asynchronous communication

Advantages of using message broker over direct RPC:
- reliability - buffer if recipient is unavailable or overloaded
- auto-redelivery of messages
- decoupling of sender to recipient means sender doesn't need to know IP address and port of recipient
- one message to many recipients possible'

