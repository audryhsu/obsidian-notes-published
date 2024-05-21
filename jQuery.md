---
creation date: 2022-02-25 16:00
aliases: 
tags: 🖥️
---

# [jQuery](jQuery.md)
---

jQuery is a [JavaScript](./JavaScript.md) library (not a separate language) of functions and methods that provides an API to manipulate [DOM](./Document%20Object%20Model.md) elements, handle events, and make [AJAX](./AJAX.md) requests.

jQuery was created to work across browsers and platforms since in the old days, there were a lot of browser compatibility issues that developers were constantly writing hacks for to create a consistent experience. Today, this is less of a concern and we can accomplish most of what we want with vanilla JavaScript. At it's core, jQuery is still JavaScript.

It's biggest advantage is using the jQuery method `$.ajax` to make [AJAX](./AJAX.md) requests. 


###### Import jQuery
🔗 [Cheat Sheet](https://oscarotero.com/jquery/)
🔗 Get [from Google's CDN](https://developers.google.com/speed/libraries/#jquery) or [jQuery's CDN](https://releases.jquery.com/)
```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
```

## Syntax
jQuery is a function that wraps a *collection of DOM elements* and some convenience methods *into an object*, also called a *selection*. 
- if the argument passed into jQuery function is a string or DOM element, it ==returns a collection of jQuery objects==
- if the argument passed is a function, jQuery ==uses that function as a callback== when the document is ready

```js
// wrap an element inside a jQuery function
var $content = jQuery('#content'); // returns a collection of jQuery objects
var $sameContent = $('#content'); // shorthand 
$content.length // => 1;  confirms that jQuery object contains elements

// Selecting only the first <h1> element on the page (in a jQuery object)
var headings = $( "h1" ); // all h1 elements
var firstHeading = headings.eq( 0 ); // returns first jQuery object
var firstHeadingElem = $( "h1" )[ 0 ]; // returns native DOM element
```
Convenience methods include `$.isArray`,  `$.isFunction`,  `$.merge` arrays,  `$.map` new array. 
> jQuery objects are *NOT live* collections.

```ad-tip
title: Note

Save selections to variables with `$` prefixed in the name to indicate that it is a jQuery object for readablity.
```

## Methods
#### Selecting elements
- `$(selector)` - can make a selection or compound CSS selectors, or pseudo-selectors 
- `.get()` - returns the DOM element itself, instead of a j wrapped DOM element
	- Use this method to compare native DOM elements, as jQuery objects are unique even if they wrap the same DOM element
- `.eq(index)` - selects the jQuery object at `index` location of the collection 
-` .attr(attribute, [value])` - acts as both a getter and setter; As a setter, can accept either a key and value or an object containing key/value pairs.
 - `.not(selector)` - returns a new jQuery object from a subset of matching elements
	 - Can also be written as a pseudo selector `$(selector:not(selector))`  
- `$(:contains(text))` - pseudo selector returns collection of elements that contain the text or parent elements of elements that contain text 
 
 jQuery [selectors](https://api.jquery.com/category/selectors/) are pretty powerful. You can select by:
 - attribute selectors that can be paired with regex 
	 - `$("[attribute]")` - elements that have `attribute`
	 - `$("[attribute*='value']")` - selects elements that have  `attribute` that *contains* `value` substring 
		 - Example: `'input[type=text].val()'` - selects value of text input field 
		 - Example: `$("a[href^='#']")` - returns all anchor tags whose href attribute *starts with* $
 - child (`parent > child`)
	 - `(":first-child")` - matches all elements that are the first child of their parent
	- `(":last-child")` 
	- `(":nth-child(n)")` - selects all elements that are the nth child of their parent, where `n` is an index, `even`, `odd`, or equation 
 - filtering by content
	 - `jQuery( ":contains(text)" )` - selects all elements that contain specified text
	 -` jQuery( ":has(selector)" )` - selects elements which contain at least one element that matches the specified selector e.g. `$("div:has(p)")"`
 - form elements
	 -  `:button`
	 - `:input` - all form  controls, such as `input, textarea, select, and button`
 - visibility
	 - `:hidden` or `:visible`

```js
// Refining selections.
$( "div.foo" ).has( "p" );         // div.foo elements that contain <p> tags
$( "h1" ).not( ".bar" );           // h1 elements that don't have a class of bar
$( "ul li" ).filter( ".current" ); // unordered list items with class of current
$( "ul li" ).first();              // just the first unordered list item
$( "ul li" ).eq( 5 );              // the sixth in collection
```
#### Manipulating Elements Attributes (get and set)
- `.html()` – Get or set the HTML contents., or the value of a script element
- `.text()` – Get or set the text contents; HTML will be stripped. Returns a string containing the combined text of all matched elements.
- `.attr()` – Get or set the value of the provided attribute.
- `.removeAttr()`
- `.width() `– Get or set the width in pixels of the first element in the selection as an integer.
- `.height()` – Get or set the height in pixels of the first element in the selection as an integer.
- `.position()` – Get an object with position information for the first element in the selection, relative to its first positioned ancestor. This is a getter only.
- `.val()` – Get or set the value of form elements such as input, select and textarea. When called on an empty collection, it returns undefined.
- `$.addClass( "big" )`
- `$.removeClass( "big" )`
- `$.toggleClass( "big" )`
- `$.hasClass("big")` - returns a boolean if class is assigned to an element (even if it has others)

#### Traversing methods 
Almost all traversal methods take an optional selector argument, which can be compounded (e.g. tag + class + id + pseudoclasses).

🔗[all jQuery traversal methods](https://api.jquery.com/category/traversing/)
###### Ancestors
- The methods for finding the parents, all which take an optional selector argument
- .`parent([selector])` - optionally takes a selector; if omitted, returns the direct parent
- `.parents()`- returns jQuery object collection of ancestors
- `.parentsUntil([selector])` - returns jquery object up until and not including a specified ancestor
- `.closest([selector])` - Selecting the closest parent, note that only one parent will be selected and that the *initial element itself is included in the search*
	- useful for finding the first ancestor element that meets the criteria passed to the method
###### Children
- `children([selector])` - returns only direct children
- `find([selector])` - returns all children, including nested children (traverses recursively) for a given selector
###### Siblings
- `.prev()` - previous sibling, or `[]` if none ^9e09da
- `.next()`
- ` .siblings()` - all siblings in both directions that match a given selector
- `.nextAll()` -  returns all sibling after  and takes an optional selector; can be chained with `first()` or `last()`
- `.nextUntil()`
- `.prevAll()`
- `.prevUntil()`

#### Utility Methods
- `$.each(object, callback(index, value))` - generic iterator that loops over arrays, array-like objects, and plain objects (by kv pairs) *that are not jQuery objects*. Use this in place of for/for in loops.
- `.each(jQuery collection, callback(index, element))` - iterate over jQuery objects, 
- `.toArray()` - returns a jQuery set as an array


## jQuery Events
Use the `$.on(eventType, [, selector], callback)` method to bind event handlers to a jQuery object. 
- jQuery has convenience methods to attach event listeners 
- Pass in an optional string `selector` to *filter the descendants* of selected elements that will trigger the handler. This is a ==shortcut to event delegation!==
	- Includes indirect children 

```js
  $("form").on("submit", function (event) {
    event.preventDefault();
    let $input = $(this).find("input[type=text]");
    $p.text(OUTPUT + $input.val());
  });
});
// attach listener on parent ul, but delegate events to descendant anchor tags
$("ul").on("click", "a", function (e) {
  e.preventDefault();
  $(e.target).closest("li").remove();
});
```

#### Convenience event handler methods
```js
$('a').click(function(e) {...}) // is the same as $.on('click')
$('li').trigger(eventType) // execute all handlers and behaviors attached to matched elements for event type
						  
$(document).off(eventType) // remove an event handler
							  
```

## .ajax() method
The power of using jQuery to make an [AJAX](./AJAX.md) request lies with the core `$.ajax()`:
- takes a *configuration object* as an argument to succinctly specify the parameters of the request
- can be chained with the `done()` and `fail()` methods that invoke callback functions if the request is succeeds/fails respectively
###### optional .ajax() settings
>📢*These are similar to [|XHR properties](XMLHttpRequest.md#Properties%20)*

- `url`: the only required property
- `type`: of HTTP request defaults to "GET"
- `dataType`: specifies the data type browser expects to get back and will parse accordingly
	- default is to infer based on the MIME type of the response
	- If json is specified, the response is parsed using `jQuery.parseJSON` before being passed, as an object, to the success handler. 
- `timeout` - time in ms for response until fail callback invoked
- `data`: data  to be sent to the server, which can be an plain object, string, array, or a query string
	- if an object is passed, the data is automatically converted into a `querystring` by jQuery
- `contentType` - default is `application/x-www-form-urlencoded; charset=UTF-8`;
[🔗additional .ajax() settings](https://learn.jquery.com/ajax/jquery-ajax-methods/)


```js
$.ajax({
  url: '/my-blog-post',
  type: "GET",
  dataType : "json",
  timeout: 5000,
  data: {} // if sending a POST request
}).done((data, textStatus, jqXHR) {
  // do something with the returned data
}).fail((xhr, status, errorThrown) {
	// code to run if request fails; raw request and status codes are passed to this method 
}).always(function(xhr, status) {
	// runs regardless of success or failure
})
```

#### Convenience AJAX methods
- `.done(function(data, textStatus, jqXHR))` is a function to be called if the request succeeds. It gets passed :
	1. `data` returned from the server, formatted according to the dataType parameter
	2. string `textStatus`, e.g. "success", "bad request"
	3. the `jqXHR` object, which is a superset of [XMLHttpRequest](./XMLHttpRequest.md) object
		1. We can access XHR object properties, such as `jqXHR.status` for the status code
- `.fail(function(jqXHR, textStatus, errorThrown))` is a function to be called if  the request fails 
- `.always(function(data|jqXHR, textStatus, jqXHR|errorThrown))` - callback that runs regardless of request status. The arguments are the same as `done` or `fail` depending on the request status

#### Forms & Serialization
- `$.seralize()` method serializes a form's data into a query string (they must have a `name` attribute)

```js
<form>
	<input type="text" name="email" id="email">
	<input id="name" type="text" name="name" >
</form>

let data = $('form').serialize() 
data // email=abc%40xya.com&name=audry
```
- `$.seralizeArray()` - serializes form data into an array of objects containing one key-value pair

## Other cool things
#### Visual Effects
```js
$.hide() // similar to $.css( "display", "none" );
$.show() // display property is cached & can be restored
$.toggle() // display or hide the matched elements
```
# Examples
###### DOMContentLoaded
```js
$(document).ready(function() {
  // DOM loaded and ready, referenced image on img tags are not ready
});
$(function() {
  // DOM is now loaded; equivalent to above
});
$(window).load(function() {
  // DOM loaded and ready, referenced image on img tags loaded and ready
});
```
###### Check if an object is a jQuery object
```js
console.log($content.jquery) // logs jQuery version number
```

###### **Updating CSS properties**
- Note that hyphenated properties must either be wrapped in quotes or camel cased
```js
$content.css('font-size', '18px').css('color', '#b00b00');
// passing in object instead 
$content.css({
  fontSize: '18px',
  color: '#b00b00'
});

$( "h1" ).width( "50px" ); // Sets the width of all <h1> elements.
$( "h1" ).width(); // Gets the width of the first <h1> element.
```

###### Sending JSON via jQuery
![Sending JSON via jQuery](./Sending%20JSON%20via%20jQuery.md#^1ad26c)

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) - 

Reference: [Moving from jQuery to Vanilla JS](https://tobiasahlin.com/blog/move-from-jquery-to-vanilla-javascript/#working-with-events)

Related: [AJAX](./AJAX.md) 

