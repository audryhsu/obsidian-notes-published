---
creation date: 2022-03-24 11:26
aliases: 
tags: 🖥️
---

# [MongoDB](MongoDB.md)
---
Mongo is an open source [NoSQL](NoSQL.md) database that uses document-oriented data model. Document dbs are *schema-less*, meaning the db does not care about the structure of the data that is stored in the db like a relational db does. We can store documents with completely different fields in the same collection.

Data stored in the db is given a schema at the *application-level* that defines the shape of documents stored. An Object Data Model or Object Relational Model (ODM/ORM) represents data as JavaScript objects, which are then mapped to the underlying database.

#### ODM vs Relational
- 👍ODMs can be data-base agnostic
- 👍ODMs are beneficial for developers, as we can continue to think of our data as objects rather than thinking about database semantics
- 👍ODMs usually cheaper to develop and maintain!
- 👎ODMs are slower because use translation code to map between objects and the database format (less efficient queries)
- 👎ODMs traditionally do not support join queries to aggregate data from multiple tables. you'd have to make multiple queries to achieve the desired result.
	- We can define references to other collections in a Schema, or even nest the entire collection within a schema (e.g. each user has a *notes* property that contains an array of all of their note objects) in a tightly coupled fashion
	- Because of this, ODM requires more design decisions upfront than relational DBs

[MDN has a tutorial on Mongoose and other ODMs](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/mongoose)

![mongo cheatsheet.pdf](./images/mongo%20cheatsheet.pdf)

### Utilities
```
sudo mongod # start local mongodb instance on default port
```

`mongod` starts your local database server and runs it on the default port (27017), and `mongo` runs the MongoDB shell-- allowing you to connect with the database and interact with it manually.

---
Tags: [Programming](Programming.md) - [Databases](./Databases.md) - [Backend Development](Backend%20Development.md)

Reference:

Related: [mongoose](./mongoose.md)