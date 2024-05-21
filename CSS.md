# [CSS](CSS.md)
This note covers the basics of CSS.

https://web.dev/learn/css/box-model/

### Cascade & Specificity
Styles cascade from top of a sheet to the bottom, so styles stipulated toward the bottom of the sheet override styles set at the top of the sheet. This also works for properties inside individual selectors.

CSS determines precedence based on **specificity weight** and its placement in the cascade. 
- **type** selector has lowest specificity weight of 0-0-1
- **class** selector has medium specificity weight of 0-1-0
- **id** selector has highest specificity weight of 1-0-0

If a id selector comes before a class selector in the cascade, id selector will have precedence because of its larger specificity weight. 

When selectors are combined, they are read *right to left*. The selector farthest to the right is known as the *key selector*, which identifies exactly which element the styles will be applied to. Anything to the left is a prequalifier. 

```css
.hotdog p // selects paragraph elements that reside within an element with class attribute value of hotdog

.hotdog p.mustard // selects paragraphs with class attribute of mustard that reside within an element with class hotdog
```
We can calculate a combined specificity weight when selectors are combined:
- `.hotdog p` = 0-1-1
- `.hotdog p.mustard` = 0-2-1 --> takes precedence bc higher specificity weight

## Box Model
Every thing is a box that requires horizontal and vertical space on the page. 
- Amount of space needed to draw an element or add text is defined by the CSS and browser defaults
- The browser renders boxes like Word processes lines of text; it will add text until a word doesn't fit, then it will start a new line and place the word on the new line.

**Width & height** of boxes determine how the browser lays out the inline-block elements depending on the size of the viewport. 
For example, 800px wide viewport vs 700px viewport 
![Pasted image 20220213181536.png](./images/Pasted%20image%2020220213181536.png)
![Pasted image 20220213181544.png](./images/Pasted%20image%2020220213181544.png)

### Basics that I should understand

```ad-summary
title: Box Model

Every element on a page is a rectangular box. By default, box model is additive. To get the *true* width and legnth of elements you must **add** padding, borders, and margins on all four sides.

Set `box-sizing` to `content-box` (default), `padding-box`, or `border-box` to change how the box model calculates element sizes. See [[#Box Sizing Models]].

```

### Box Properties

- **width and height** define how much horizontal and vertical space it needs for *content area* of box, which may or may not include padding and borders.
- **padding** is area surrounding content area of box and separates content from it's border. 
	- INSIDE border and *is part of the element* 
	- Use to effect the visible or clickable area of an element box
	- Add some spacing to left/right of an `inline` element
- **border** is boundary around padding
	- we can set individual borders with `border-bottom-*` and add width, style, or color
	- `border-radius` - accepts length units  to round corners of an element 
- **margin** is transparent area outside the border and creates separation between elements 
	- *is not part of the element* and will not be responsive to event listeners on an element.
	- Margins for elements adjacent to each other *collapse*, in that the greater of the two margin values are used, not the sum. 
	- Use for vertical gap between containers
- 

Total width of an element = margin-right + border-right + padding-right + width + padding-left + border-left + margin-left
 ![Pasted image 20220213181738.png](./images/Pasted%20image%2020220213181738.png)
 
-   Every box has a width, height, padding, border, and margins; know the differences.
-   Padding, borders, and margins have separate properties to set the left, right, top, and bottom of each element. You can use shortcut properties to specify all four sides at once.
-   How does the visual display model interact with margins, borders, and padding?

### Visual Display Models
Elements are boxes that all have a `display` property (typically `block`, `inline`, or `inline-block`). You can convert any element to `block`, `inline`, or `inline-block` element by setting the `display` property.

##### Display: Block
**Block elements** group one or more elements (including other blocks) into areas of the page (think `p`, `h1`, `body`, `div`, etc). Block elements are distinct from inline elements because they always start a new line and take up the full width available. 
- Block elements use the box properties (width, height, padding, border, margin) to determine size of element within the block. 

most common block elements (by default): 
-  `section`, `article`, `aside`, `header`, `footer`
-   `p`
-   `h1` through `h6`
-   `blockquote`
-   `ul`, `ol`, `dl`
-   `figure` and `figcaption`
-   `form` and `fieldset`

##### Display: Inline
**Inline elements** are used to alter the way small sections of text are displayed. Browsers handle certain box properties for inline elements differently.
- width and height are ignored (except for img) - use computed values from element content
- ignore top and bottom margins
- padding and border can be used, but may behave unexpectedly 
The following elements are `inline` by default.
-   `span`
-   `b`, `i`, `u`, `strong`, `em`
-   `a`
-   `sub` and `sup`
-   `img`
##### Display: Inline-block
- **Inline-block** elements are a mix of both types -- act like block elements except that they do *not take up entire row* when the width property is less than available width. 
	- flow the same way text and inline elements flow
	- can be placed side by side with other inline-block and inline elements
	- Use case: horizontal flowing navigation bar of list items

```ad-summary
title: Inline Elements

Top and bottom margins and padding on inline elements do not affect the placement of the element content or the content around it. Borders and padding will look wacky. 

```

-   Containers are almost always `block` elements, while non-containers are `inline`. When in doubt, check MDN.
-   Don't try to memorize which HTML elements are `block` or `inline`.

##### Other CSS Layout
🔗 [Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#top-of-site) seems to be another visual display model. It ensures that elements behave predictably when the page layout must accommodate different screen sizes and different display devices.

[CSS Grid](https://css-tricks.com/snippets/css/css-grid-starter-layouts/): seems like a good choice for grid-based designs.

### Box Sizing Models

TLDR; `border-box` alters box model so border and padding are included in width/heigh of element

-   The `content-box` and `border-box` sizing models specify how to calculate an element box's visible dimensions. 
	- The `content-box` setting is the default setting for the `box-sizing` property for all elements in all modern browsers. 
	- In this model, the `width` and `height` properties specify the size of the actual content area. You need to add padding and borders to get the size of the visible box. (e.g. the element's visible box will be width/height + padding + borders).
	- Calculated in (from content area) to out
-   The `border-box` setting causes the browser to interpret the `width` and `height` properties as the *total width and height* of the box excluding the margins. 
	- Specified width/height is the visible box. 
	- Calculated out (width) to in (border/padding move inside the width/height)
-   The `border-box` setting is "best" since it simplifies the math a front-end developer must do for responsive layouts. We tell the browser what space to use for an element, and it does the rest.  For example, if we have a box with a width of 50% and padding of 12px; `border-box` ensures that it's precisely 50% of the container width, not 50% plus 24-pixels.

To change box-sizing model : 
```css
html {
  box-sizing: border-box;
}

*, *::before, *::after {
  box-sizing: inherit;
}
```


## Dimensions
- `px` is used as an absolute unit of measurement in CSS
	- a CSS pixel or reference pixel is different than a physical/display pixel. CSS pixel will be adjusted to account for screens of different resolutions and sizes (monitor vs phone) to get an image to appear about the same size on different screens.
	- it is still the closest thing to an absolute unit
- Relative units include  `em`, `rem`, `%`, and `auto`.
	- Ems and rems are proportional to the **calculated** and **root** font sizes.
		- calculated font size is height of font in pixels
		- root size of font is height of the base font for document (`html` element font size set in `px`)
		- Once you've set the root font size, `1.5rem` means the same thing everywhere in the document -- rem can be more consistent
		- `ems` may compound font sizes, since they use the *current font size*. 

```ad-note
title: rems
Not all browsers recognize `rems`. Use fallback value for font-size property (left most)

p { font-size: 20px; font-size: 1.25rem; }
```
- We can use percentages to define a dimension as a fraction of a container's width or height 
- `auto` also is not a length value, but it can be used to let browser determine widht or height. common use cases:
	- `width/height: auto` - try to fit entire element (incl. margins) in container
	- `left/right margin: auto` - push element all the way right or left (opposite) inside container. If both, then it centers element
	- `top/bottom margin: auto` means 0
	- Padding doesn't accept auto
	- `width 100%` !== `width: auto`, as margins aren't considered in the former when fitting the element inside container

###### Rules of thumb for dimensions:
- Use absolute units sparingly:
	- good for root font size, images, top/bottom margins and padding, width/height of fixed containers (navigation sidebars), and border widths
- Use relative units liberally:
	- rems for fonts with a fallback to ems or pixels
	- use rems, ems, or pixels for left and right margins and padding 
	- % for measurements proportional to container element's width or height work well for responsive containers
	- `auto` width/heigh to let browser to calculate values
	- `auto` left and right margins to left/center/right justify block element inside a container

-   Understand why we need to talk about CSS reference pixels and physical pixels. Don't try to memorize the details, but understand the topic well enough that you won't be too surprised the first time you encounter the differences in the wild.
-   Use `auto` margins to center block elements horizontally.

## Positioning Elements
1. Float removes an element from normal flow of page
	- can be positioned to left or right of parent element

1. Using `display: inline-box`
- display elements within a line while allowing them to accept all box model properties, including height, width, padding, border, and margin.

1. Position:  identifies *how* an element is positioned on a page and whether or not it will appear within the normal flow of a document. 
	- default is `static` for all elements
	- commonly used with `offset`

Relative offset - preserves space for element and and not allowing other elements to flow around it or use it's space;
Absolute - will *not* appear within the normal flow of a document, and the original space and position of the absolutely positioned element will *not* be preserved.
- position determined by closest relatively positioned parent element


## Typeface
- `font-family` property is used to declare which font—as well as which fallback or substitute fonts—should be used to display text.
	- left most is primary, then defaults
- `line-height`- the distance between two lines of text (often referred to as leading) is declared using the line-height property. 
	- best practice: set the line-height to 1.5x font-size property value (e.g. 150% or 1.5).

---
Tags: [Programming](Programming.md) - [Front-end Development](Front-end%20Development.md) - 

Reference: [Shay Howe HTML/CSS](https://learn.shayhowe.com/html-css/positioning-content/)

Related: [HTML](./HTML.md)
