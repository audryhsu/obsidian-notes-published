---
creation date: 2022-01-24 20:51
aliases: 
tags: 🖥️ 📥 🌱
---
 
# [HTML](HTML.md) Basics
---

Persevere to write *semantic code*, or code that describes the content rather than how the content should look. This keeps content modularized from styling.

Key terms:
- **elements** are designators that define structure and content of objects within a page. These are surrounded by **tags**
- **attributes** are properties used to provide add'l information about an element, e.g. id, class, src, href
- **tags** enclose elements; some are self-closing (don't need a second tag)
- **[CSS](./CSS.md) selectors** designate which elements within HTML to target for styling 
	- properties determine the styles that will be applied to the element after a selector (e.g. color, font-size)
	- type selectors - target elements by element type (e.g. `div {...}`)
	- class - `.class {...}`; an element can belong to multiple classes delimited with a space 
	- id - `#id {...}` are unique to one element


Visual for what HTML5 element to use:
http://html5doctor.com/downloads/h5d-sectioning-flowchart.pdf

On [semantic code](semantic%20code.md) -- HTML5 has given semantic meaning to `<b>` (Stylistically offset text, such as keywords) and `<i>` (alternate voice text, as in a dialogue);

If you intend to use boldface for important text, use `</strong>` instead of `<b>`; if you wish to use italics for emphasis, use `<em>` instead of `<i>`.

## Img element
- self closing
- `src` required - URL format can be relative or absolute
- `alt` - used by screen readers and SEO
- optional `width/height` in pixels; CSS overrides and can be better for responsiveness
- Use `figure` block element and `figcaption` to tie an image nad caption together
- An image can also be a link if nested inside an anchor tag

High level overview of different image files:
- JPGs are very good for compression, but are **lossy** when compressed (less detail than original); if consistently edited, it can lose integrity to be unrecognizable
- PNGs is non-lossy are good for photos high quality/color; does not compress as much; ability to be transparent (logos, watermarks)
- GIFs are highly compressible and very limited in size and color; hence why managable to send in text messages
Come back and do these exercises: https://launchschool.com/lessons/de86d90a/assignments/69d139b4

## Lists and Tables
- Three types of lists: ordered, unordered, description lists
- Create horizontal lists (like nav bars):
	-  setting the `ul` width to 100% and font size 0, `list-style-type: none`
	- set `li display` to `inline-block` and relative width %  
- You can use pseudo-classes like `:hover` or `:focus` in CSS to specify styling dynamically when an element is hovered/focused on
```css
nav a:hover,
nav a:focus {
 background-color: blue;
 color: white;
}
```
- Legacy design (before CSS) used tables for layout. Not a good idea anymore
- you can add semantic meaning to tables by using table header, table body, and table footers;
- `scope="row"` and `scope="column"` attributes in table headers also helpful semantically
- Best practice is to use CSS selectors or `scope` attribute to style tables

## Forms
Forms are where front-end and back-end concerns come together. Forms solicit input from user, performs basic validation, and sends form data to the server. It *does not update* information on the server.


-   the `form` tag tells the browser where and how to send data with the `action` and `method` attributes.
	- `method` attribute - HTML limits to GET and POST requests
	- `action` attribute - provides URL where to send requests (e.g. the route);
		- can be overriden by a `formaction` attribute attached to individual action items like a `button` or `input type="submit"` 
	- `fieldset` tag - optional tag that groups together form content as a set of related data; provides semantic data to the browser and CSS styling (e.g. username and password together)

-   Use `input` and `textarea` text tags.
	- `input` tag describes  a control or widget, aka a mechanism that lets user supply information to the application.
		- `type` attribute is required and specifics type of widget (`submit`, `text`, `password`, etc.)
		- `name` attribute must be supplied to label the input data for backend use.
		- `label` tags can be used in conjunction with input tags to associate identifying text with an input field (e.g. "Username" label for a text input with id="username"). 
**Form example**
```
<form action="#" method="post">
  <fieldset>
    <h1>Log In</h1>
    <label for="username">Username</label>
    <input type="text" name="username" id="username" />

    <label for="password">Password</label>
    <input type="password" name="password" id="password" />

    <input type="submit" value="Log In" />
    <input type="submit" value="Delete account"
           formaction="/account/delete" />
    <input type="submit" value="Forgot password"
           formaction="/account/password" />
    <input type="reset" value="Reset" />
  </fieldset>
</form>
```
- Note the `formaction` attributes provide add'l actions if the user clicks on the Delete or forgot password buttons. 

-   Know the differences between checkboxes, radio buttons, and selection lists.
-
##### Input types
*always validate inputs*
-   Understand the `type` attribute on `input` tags.
    -   `checkbox`,  - multichoice fields
	    -  use `name` to set a name on a set of related checkboxes
		    - option to use different names for each choice and omit the value attribute to send key value pairs of `"choice=on"`
	    -  `value` attributes are the values given to the server.
	- `radio` - single choice fields;
		- can set a default value with `checked` boolean attribute
		- use `required` attribute to each button if you want something selected from the group
    -   `text`, `email`, `tel`, `password` - create text entry fields.
	    - you can use the `placeholder` attribute to show ghost values
    -   `submit` - submits contents of form to server. Can optionally take `formaction` attribute to override the `action` attribute on the `form` tag
    - `reset` - reset the contents of a form to its default values. does not send a request to the server.
    -   `hidden`
-   Construct `select` lists (drop down) to allow user to choose from zero or more options
	- `select` tag uses `name` attribute to provide server with key name 
	- uses `option` tags with `value` attributes for each of the dropdown options.
	- can implement a placeholder by adding an `option` tag that is marked `disabled selected` 
	- optional `multiple` boolean attribute to allow multi-select

##### Input attributes
- `value`
	- for text inputs, assigns a default value
	- for checkbox and radio types, value is what is sent to server for the indicated checkbox or group of radio buttons
	- for button types (submit, reset, button), value is the label inside the button
		- use `<button>` instead of `input type="button"`
- `placeholder` - do not use in place of a label for screen readers
- `disabled` - browser will render the element but won't let the user interact with it
- `required` - browser will not let you submit the forn until the user completes required fields
- `autocomplete` - can be on or off to allow/prevent browser from storing data for later reuse; doesn't work for passwords.
- `autocapitalize` - can be set to `words`, `characters`, or `none` to capitalize first letter of words or sentences
	- non-standard, available for some mobile browsers
- `autocorrect` -can be on or off; 
	- non-standard but available for mobile browsers


**Form styling**
-   Use labels in a form with both container and `for` attribute formats.
-   Use description lists to help format forms.
	- useful to keep top bottom forms vertically aligned; using one `dl` per label/input pair
	- `dt` are the labels which in turn, identify the `dd`, or controls (form input)

## Data Attributes
HTML5 provides a feature to create custom data attributes (`data-*`) that can be added to any HTML element. This is convenient because it separates an element's data from it's CSS selectors used for styling.
We can set as many data attributes as we want!
##### JavaScript
Access the `dataset` property that is an object of key-value pairs.
- keys are the data attribute names (e.g anything that comes after)
- data attribute that contains hyphens is changed to camel case 
- data values are stored as strings
- We can view this object of data attributes by calling console.table again, passing in the gold DOM element's dataset property.
```js
<div data-block="gold">
div.dataset // {block: 'gold'}

div.dataset.block = "silver" // set the 'block' data attribute
div.dataset // {block: 'silver'}
div.dataset.poop = "smelly!"
div.dataset // {block: 'silver', poop: 'smelly!'}
```

Since these are plain HTML attributes, you can access them from CSS too:
```css
div[data-block="silver"] {...}
```
##### jQuery
There are two ways to get and set the data-block attribute:

```js
// attr() method
$link.attr('data-block', 'gold')
//using data.() method
$link.data('block') // gold
$link.data('block', 'silver')

// note that using the data() method to set is different
$link.attr('data-block') // gold
$link.data('block') // silver
```
- Note: Setter functionality of the data method sets a key-value pair in an internal data store on that DOM element, *which is different than the HTML data attributes*. 
- get or set the value of an HTML data attribute (and change the HTML markup), use the `.attr `method.
-  If you want to set and retrieve custom data on an element after the page has been rendered, use the `.data` method. 

---
Tags: [Programming](Programming.md) - [HTML](HTML.md) - [Web Development](Web%20Development.md)

Reference: [Input types](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input)

Related: 