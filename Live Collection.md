---
creation date: 2022-02-10 18:45
aliases: 
tags: 🖥️
---

# [Live Collection](Live%20Collection.md)
---

In the HTML DOM, a live collection is automatically updated when the underlying document is changed. Some DOM-querying methods such as `document.getElementsByClassName` and `document.getElementsByTagName` return collection objects (array-like) such as [HTMLCollection](HTMLCollection.md) and [NodeList](NodeList.md)s that are live collections.

Live collections can lead to unexpected behavior when you iterate over it or use the return value when it's `length` property is updated when elements are added or removed from the DOM.

- `document.getElementsByClassName()` returns an `HTMLCollection`, and is live.
-`document.getElementsByTagName()` returns  an `HTMLCollection`, and is live.
- `document.getElementsByName()` returns  a `NodeList` and is live.
- `document.querySelectorAll()` returns  a `NodeList` and is **not** live.

`HTMLCollection`s appear to always be live.

## HTMLCollections & NodeLists

`NodeList` objects are a collection of nodes. These are **not** arrays but it is possible to iterate over with `forEach` and convert to real array using `Array.from()`. 

The **`HTMLCollection`** interface represents a generic collection (array-like object similar to [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)) of elements (in document order) and offers methods and properties for selecting from the list.

Both :
-  a `length` property
- are live collections. (Some `NodeLists` are static, like the one returned from `document.querySelectorAll()`)
- Have their own [methods](https://developer.mozilla.org/en-US/docs/Web/API/NodeList) to access items in the collection


---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) - [Web Development](Web%20Development.md) - [Document Object Model](./Document%20Object%20Model.md) 

Reference:

Related: 