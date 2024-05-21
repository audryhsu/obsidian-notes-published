---
creation date: 2022-02-09 17:26
aliases:
  - DOM
tags: 🖥️
---

# [Document Object Model](Document%20Object%20Model.md)
---
The [Document Object Model](Document%20Object%20Model.md)  is an *application programming interface* ([_API_](https://www.w3.org/TR/DOM-Level-2-Core/glossary.html#dt-API)) for valid [_HTML_](https://www.w3.org/TR/DOM-Level-2-Core/glossary.html#dt-HTML), or an in-memory object representation of an HTML document.

The DOM provides a standard programming interface (it can also be used with other languages besides JavaScript!) and represents a document (web page) as nodes and objects in a way that programming languages can interact with it by changing the structure, style, and content.

**DOM & HTML**
DOM is a hierarchal tree structure of nodes. DOM nodes are not 1:1 with HTML tags.  The DOM includes HTML and then some that is inserted by the browser or by JavaScript. **the DOM represents current page content** while HTML represent initial page content. 
- The DOM also includes HTML element as nodes,  the parent HTML node itself, text nodes, whitespace (empty) nodes.

**DOM & JavaScript**
JavaScript uses the DOM to access the document and it's elements. Scripting languages like JavaScript are used to manipulate and access the DOM parts. `<script>` tags are used to use the DOM API for the document or windows object. 

## Nodes and their properties
**All** DOM objects are Nodes (think of nodes as the Object.prototype of all nodes). DOM Objects such as **Element** and **Text** all inherit from Node (share Node properties).
- Elements include specialized element types, like `HTMLParagraphElement`.

![Pasted image 20220209180801.png](./images/Pasted%20image%2020220209180801.png)
### **DOM Inheritance**
-   `EventTarget` - provides event handling behavior that supports interactive web applications
    -   `Node` - "prototype" of all nodes, they inherit from this guy
	    -   `Text` - a common subtype of `Node` that holds text
	    -   `Comment`
	    -   `Document`
	        -   `HTMLDocument`
	    -   `Element` - a common subtype of `Node` that represents HTML tags. Almost all HTML tags have their own Element subtype...
	        -   HTMLElement
	        -   HTMLAnchorElement
	        -   HTMLBodyElement
	        -   HTMLButtonElement
	        -   HTMLCanvasElement
	        -   HTMLDivElement
	        -   HTMLFormElement
	        -   HTMLHeadingElement
	        -   HTMLHtmlElement
	        -   HTMLImageElement
	        -   HTMLInputElement
	        -   HTMLLIElement
	        -   HTMLLabelElement
	        -   etc, etc.
	        -   SVGElement
	        -   SVGColorElement
	        -   SVGRectElement
	        -   etc, etc.

```ad-important
title: Knowing a Node's type

A node's type determines what properties and methods are available. Developers need to know the type to understand what you can do with the node. It's common to primarily interact with `Element` nodes, but remember that they inherit from the Node interface and have access to methods like `Node.contains()`. 
```

We can check a node's type via either the `node.toString()` method or `node.constructor` or `instance of HTMLElement`.

**Node properties**
- `Node.nodeName` returns the string representation of the node type
	- `#text`, `element` (HTML tag), `#comment`, `document`
	- - `Node.nodeType` returns a number that matches a node type constant
- `Node.nodeValue` - we can access the textual content of a text node (element nodes return null, but text nodes have text)
- `Node.textContent` - returns/sets textual content of a node *and all of its descendants* inside the Element node (including their children nodes) concatenated into a single string.
**Node methods**
-  `Node.contains(otherNode)` - returns a boolean if Node contains other node *or IS other node*
- `Node.cloneNode([true])` - clone a node and optionally all of its contents


### Traversing Nodes
Nodes are hierarchal and have relationships relative to one another. *Walking the tree* refers to visiting every node that has a child, grandchild, etc relationship within a given node (the family tree) usually with a recursive function.

The DOM doesn't use arrays to walk the tree, it uses DOM nodes. We can define a recursive function `walk` to walk the tree for each node, and then pass a callback function to do something with those nodes. 

> Because we are traversing DOM nodes (`typeof NodeList`) and not arrays, we cannot use array methods like `map` & `forEach`. We should use simple `for` loops.

 [✂️ Recursively walking the DOM](./DOM%20Manipulation%20Snippets.md#Walk%20DOM%20forEach)

We can use node properties to check their relationships to other nodes:
- `parent.firstChild` == `childNodes[0] or null`
- `parent.lastChild` --> `childNodes[childNodes.lengh - 1] or null`
- `parent.childNodes` ==> returns a [Live Collection](./Live%20Collection.md)  (`NodeList` object) of all direct child nodes
- `child.nextSibling` --> `parentNode.childNodes[n+1]` or `null`
- `child.previousSibling` --> `parentNode.childNodes[n - 1]` or `null`
- `child.parentNode` --> immediate parent of this node

![Pasted image 20220304150003.png](./images/Pasted%20image%2020220304150003.png)
![Pasted image 20220210092656.png](./images/Pasted%20image%2020220210092656.png)


### Element attributes
we can set element *all* attributes using getter and setter Element methods 
- `element.hasAttribute('class')` - returns boolean
- - `element.getAttribute('id')` - returns value as a string
 - `element.setAttribute('id', 'complex')` - return undefined

An easier way to access special attributes (`id`, `name`, `title`, `value`) are fetching the value of these exposed properties:
- `element.id`
- `element.name`
- `element.title`
- `element.value`
**Accessing `class` attribute**
- `element.className` - note class is a reserved keyword. This returns a space delimited string of classes; Parsing string is hard to work with, so use:
	- `classList` property return array-like `DOMTokenList` object with add'l properties:
		- `add(name)` - add class `name` to element
		- `remove(name)`
		- `toggle(name, [force])` - add class `name` to element if it doesn't exist, remove if it does
			- optional force parameter turns the toggle into a one-way only operation. 
			- e.g. if force evaluates to `false`, then the token will *only* be removed. 
			- we can pass in an expression as the  `force` parameter to be used as a conditional to determine when to toggle the token;
		- `contains(name)` - return `true` or `false` depending on whether element has `name` of class
		- `length` - number of classes to which element belongs

**Accessing `style` attribute**
`style` attribute references a `CSSStyleDeclaration` Object of an element. 
- use this to alter [CSS](./CSS.md) properties, like changing colors
- use camelCase version of hyphen separated CSS properties (ex. line-height -> lineHeight property)
- used less frequently, as its easier to use classes in stylesheet to alter element stylings

```js
> let h1 = document.querySelector('h1');
> h1.style;
= CSSStyleDeclaration {alignContent: "", alignItems: "", alignSelf: "", alignmentBaseline: "", all: "", ...}

> h1.style.color = 'red';
> h1.style.color = null; // removes a CSS property
```

### Finding DOM Nodes
- Use the `document.getElementById(id)` to return the element based on a specific id 
To get a collection of nodes:
- `document.getElementsByTagName(tagName)` - returns `HTMLCollection` or `NodeList` of matching elements
- `document.getElementsByClassName(className)` - `HTMLCollection` or `NodeList` of matching elements

✂️**Understanding** [ getElementsByTagName](./DOM%20Manipulation%20Snippets.md#Walk%20and%20getElementsByTagName)

### Using CSS Selectors
The process for finding certain elements by tag and class becomes more complex as the DOM grows in complexity. More popularized ways involve using `querySelector` to search for comma separated list of CSS selectors as strings  arguments:
- `document.querySelector(selectors)` - returns first matching element or null
- `document.querySelectorAll(selectors)` - returns a `nodelist` of matching elements or an empty nodeList

```ad-note
title: CSS Selector Methods

These methods are available on *all elements* not just the `document` and search for *CSS Selectors*, not just element tags. You can search for everything with the same `.className`

Note: You must unicode escape numeric #ids when passing into querySelector 
`document.getElementById('1') ==> document.querySelector('#\\31 ');`

```


```js
> document.querySelector('#divTwo, #divOne');
// <div id="divOne"></div>    // returns the first matching element;
                             // div with an id of `divOne` matched first
 document.querySelectorAll('#divTwo, #divOne');
//NodeList(2) [div#divOne, div#divTwo]

```
### Traversing Element Nodes
In most cases, you want to get a list of element nodes only and cut out the excess text nodes (some empty, others not). These methods let us get a list of just our element nodes:
![Pasted image 20220211094256.png](./images/Pasted%20image%2020220211094256.png)
*Parent element properties*
- `children` - live collection of all child elements; returns an empty collection if no children
- `firstElementChild` - returns `children[0]` or null
- `lastElementChild` -` children[chidren.length - 1]` or `null`
- `childElementCount` - returns `children.length`
*Child element properties*
- `nextElementSibling` - returns `parentNode.children[n+1]` or `null`
- `previousElementSibling` - returns `parentNode.children[n-1]` or `null`

To access the `Text` nodes when using element selectors, use the `textContent` property of the element nodes.

**Using querySelector** to update text nodes
```html
<!doctype html>
<html lang="en-US">
  <head>
    <title>Go Back or Continue</title>
  </head>
  <body>
    <p>
      You can <a href="?page=2">go back</a> or <a href="/page/3">continue</a>.
    </p>
  </body>
</html>

```
**Update the anchor tag** text node
```js
> document.querySelector('a').textContent = 'step backward';
// "go back" is replaced with "step backward"
```


```ad-warning
title: Replacing textContent

Reassigning the `textContent` property of an element will remove *all child nodes* from the element and replace it with the text node with the value you provide. Be careful not to unintentionally overwrite children nodes.

```
**This will overwrite children nodes**
```js
> document.querySelector('p').textContent = "You can't go anywhere.";
// "You can't go anywhere."
```
- This replaces everything nested underneath the `<p>` tag and removes all of the anchor tags.

Best practice is to place text content that you want to update with JS inside an element tag, such as a  `span` or `div` tag to be able to use pass in the element's CSS selector into  `querySelector`

### Creating new nodes
Two ways: create new empty nodes or clone an existing node hierarchy:
- `document.createElement(tagName)` - returns a new Element node
- `document.createTextNode(text)` - returns a new text node
- `node.cloneNode(deepClone)` - returns a copy of `node`
	- `deepClone` specify if `true` or `false` to tell the method if you want to include all children nodes 
	- after a `deepClone` node is made, the nodes are independent of each other

### Adding nodes to the DOM
- `parent.appendChild(node)` - appends a node to the end of parent.childNodes
- `parent.insertBefore(node, targetNode)` - insert a node before targetNode
- `parent.replaceChild(node, targetNode)` - remove targetNode from parent.childNodes and insert node in it's place.
- `element.insertAdjacentElement(position, newElement)` - inserts a new element at `position` relative to element
- `element.insertAdjacentText(position,text)` inserts text node that contains `text` at position relative to `element`

`position` must be a string value:
- `"beforebegin"` - before element
- `"afterbegin"`
- `"beforeend"` - after the last child of element
- `"afterend"`

Note: Use `document.body.appendChild`, and not `document.appendChild`
```ad-important
title: Nodes must be unique

A node **cannot appear twice in the DOM**. If you try to add a node that is a dupe, the original one gets removed from original location. 

```

### Removing nodes

^4542b7

- `node.remove()` - removes `node` from DOM
- `parent.removeChild(node)`



---
Tags: [Programming](Programming.md) - [Web Development](Web%20Development.md) - [HTML](./HTML.md) - [CSS](./CSS.md)

Reference: [DevTools](https://developer.chrome.com/docs/devtools/dom/#scroll2)

Related: 

