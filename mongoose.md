---
creation date: 2022-03-26 14:15
aliases: 
tags: 🖥️
---

# [Mongoose](mongoose.md)
---
The Mongoose library API, a object document mapper (ODM), which saves JavaScript objects as Mongo "documents". Mongoose provides built-in type casting, validation, query building, business logic hooks to more easily work with [mongoDB databases](./MongoDB.md). It's essentially a front end to MongoDB via [JavaScript](./JavaScript.md)!

### Basics
Mongoose uses **Schemas**, **Models**, and **Documents**.

**Schemas** map a MongoDB collections and define the shape of documents within that collection (e.g. field/key names, types). Schemas are the configuration object to  construct a Model. 
- Each schema key has a schema type, which acts as the configuration object for an individual property (e.g. getters/setters, values stores). Schema types can be String, Number, Boolean, Date, etc.

**Models** are essentially class constructors compiled from Schema definitions. Models are responsible for performing CRUD operations on Documents on the underlying MongoDB.  Models support MongoDB query syntax, which again are abstractions or aliases for mongoDB queries. 

A **Document** is an instance of a model. Documents represent 1:1 mapping of documents stored in MongoDB. Documents inherit from the model class that it was created from. We can use the `Document.save()` method to save a document mm ,to the DB, and Mongoose will automatically perform some validation middleware before saving (e.g. casting doc properties to their Schema types)
- Subdocuments are documents embedded in other documents and have their own Schema and middleware. This creates a parent/child relationship between top-level documents and their children. 

### Data validation
There are many built-in validators that let you easily set validations in schema (e.g. minimum length, field types, required). You can also set custom validators, too. This is nice because Mongoose and the model (MVC) part of the app are encapsulated and extracted separately from the app logic in [ExpressJS](./notes/ExpressJS.md), as we did in the [Fullstack Open Project (FSO)](./Fullstack%20Open%20Project%20(FSO).md) .

##### Tips using mongoose with Express
Mongoose has [validation functionality](https://mongoosejs.com/docs/validation.html) that we can define on an object's schema, so we can validate POST data before it is stored in the database and Express doesn't have to worry about this. 

Don't forget to update the Express error handling middleware to handle mongoose `ValidationErrors` [objects](https://mongoosejs.com/docs/validation.html#validation-errors) to provide more useful error messages. 
- Each `ValidatorError` has `kind`, `path`, `value`, and `message` properties. Typical to send the `error.message` in the HTTP response.


### Queries
We cannot perform joins like a relational database, but mongoose has a `populate` method that makes several queries under the hood for us. 

With join queries in Mongoose, nothing can guarantee that the state between the collections being joined is consistent, meaning that if we make a query that joins the user and notes collections, the state of the collections may change during the query.


---
Tags: [Programming](Programming.md) - [Databases](./Databases.md) - [Backend Development](Backend%20Development.md) 

Reference:

Related: [mongoDB databases](./MongoDB.md)