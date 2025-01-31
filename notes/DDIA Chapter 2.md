---
tags:
  - books
  - designing-data-intensive-applications
  - engineering
aliases: 
publish: true
---
[Designing Data-Intensive Applications - Martin Kleppmann highlights.pdf](../images/Designing%20Data-Intensive%20Applications%20-%20Martin%20Kleppmann%20highlights.pdf)
# Data Models and Query Languages
Choosing the appropriate data model to model an application's data is paramount --  the data model's data structures should model data being stored as closely as possible to make it more intuitive for developers working with the data because it affects how we *think about the problem* we are solving.

Rule of thumb: Pick the data model that leaders to simpler application code.
## Why NoSQL/document model could be a good choice
NoSQL (document model databases) - think JSON :
👍more flexible schema (compared to RMDBs)
- schemas enforced *on read* instead of *on write*
	- schema on write checks that the data conforms to the schema before writing anything to the DB
- 🧠 type-checking at runtime vs static compile time checking
👍 better performance due to locality (don't need to JOIN to get attributes of tree and it's children nodes)
- benefit only realized if tree's attributes are frequently read all together
- tradeoff is that writes or updates will require re-writing entire document
👍more similar to data structures used by application code
- natural for hierarchal or tree-like data with nested data structures (one:many)
👎 no support for joins
- not good for many:many or many:one
	- basically, any highly-interconnected data
- cannot refer directly to a nested item within a document (imperatively traverse the tree for access)

> [!NOTE] 
> **Why are document object data models bad for many to one relationships if they are just the inverse of one to many?**
> Document databases are designed to store related data together in a single document. This makes one-to-many relationships (where a single document may contain nested subdocuments) straightforward since the many "child" items are embedded within the one "parent" document.
> For many-to-one relationships, if many documents need to refer back to a single piece of data (ie many resumes refer back to the same college entity), this isn't as efficient in a document database. Document databases do not typically support the kind of joins that SQL databases do, which are needed to efficiently query many-to-one relationships.
>  As a result, when you have many-to-one relationships in a document database, you may end up with either *duplication of data* or the need to perform multiple queries and combine the results in application code, which can be more complex and less performant than a join performed by a relational database.
>  In a relational db model, it's very easy for rows to reference a foreign key in another table because data is denormalized
> 

Relational DBs have be dominant, however there is a time and place for NoSQL databases (e.g. graph, document object,  ). Main criticism of SQL databases stem from the *object-relational mismatch*, where object-oriented application code requires an ORM translation layer to store data object into tables, columns, and rows in a denormalized format.

ORMs were created to help translate app code (usually written OO style) and relational dbs (tables, schemas, etc).
- Using JSON model reduces the mismatch between app code and storage layer
- better **locality**, since document stored as a single continuous string encoded as JSON or XML 
- good for one :: many relationships
- end up looking tree-like data structure) 
	- EX: Bill Gate's resume data is stored in on JSON object instead of needing JOIN queries 

## Document and relational databases are becoming more similar over time
In reality, modern databases have added support for features that provide some flexibility which really lands them somewhere in the middle between pure relational and pure document.
- Many SQL dbs support the `JSON` data type in a column with some schema checking features or ability to index and query inside `XML` documents
- MongoDB drivers automatically resolve database references (effectively performing a client-side join), but it may be slower/less optimized than a JOIN.

# Notes
![DDIA Chapter 2 scratch notes.svg](../images/DDIA%20Chapter%202%20scratch%20notes.svg)
%%[🖋 Edit in Excalidraw](../images/DDIA%20Chapter%202%20scratch%20notes.excalidraw), and the [dark exported image](DDIA%20Chapter%202%20scratch%20notes.dark.svg)%%
