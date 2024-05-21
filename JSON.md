---
creation date: 2022-02-22 08:57
aliases: 
tags: 🖥️
---

# [JSON](JSON.md)
---
JavaScript Object Notation, or JSON, is a text-based data format popularized by [Douglas Crockford](Douglas%20Crockford.md)  that closely resembles JavaScript object syntax, however many programming languages support ability to read (parse) and generate JSON. 

Converting JSON to a native object is called *deserialization*, while converting a native object to JSON is called *[serialization](./Data%20Serialization.md)*.

### Converting data
We serialize data to be able to send it across the network. We can do this using the built-in JSON object that is available in browsers via Web APIs:

- `JSON.parse(jsonString)` - returns a JavaScript object
- `JSON.stringify(nativeObject)` - returns equivalent JSON string

### Notes about JSON
- JSON is purely a string with a specified data format, containing only properties (no methods)
- *Must use double quotes* around strings and property names

---
Tags: [Programming](Programming.md)

Reference:

Related: 