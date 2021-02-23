# Web Accessibility Tips


Semantic HTML is the foundation of accessibility in a web application. Using the various [HTML elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Element) to reinforce the meaning of information in our websites will often give us accessibility for free.


### Lists
```html
<!-- bad -->
<div>
  <div>item 1</div>
  <div>item 2</div>
</div>

<!-- good -->
<ul>
  <li>item 1</li>
  <li>item 2</li>
</ul>
```

### Links
```html
<!-- bad -->
<div onclick="handleClick()">click me</div>

<!-- good -->
<a href=''>click me</a>
```

### Regions
```html
<!-- bad -->
<div class="sidebar"></div>

<!-- good -->
<aside class="sidebar"></aside>
```

### Navigation
```html
<!-- bad -->
<div class="navigation">
  <a href=''>Link 1</a>
</div>

<!-- good -->
<nav class="sidebar">
  <a href=''>Link 1</a>
</nav>

<!-- better -->
<nav class="sidebar">
  <ul>
    <li><a href=''>Link 1</a></li>
  </ul>
</nav>
```

# The role attribute
Most of the semantic HTML elements can be expressed as regular `div` elements with a specific `role` attribute.
However, more often than not, screen readers don't handle these "faked" elements the same way.
And you'll need to add additional functionality to get the same behavior like screeb reader announcements and keyboard functionality.

# Different Methods of Navigating a Page
- Mouse users
- Touch-screen users
- Keyboard navigation
- Screen reader


# CSS Utility
There are many instances where the UI has repetive text that doesn't change, but the visual content/styling around it gives it context.

Blind users will not see the styling around it and navigate the site by having the screen reader recite all links on the page.
If there are buttons that say "View more" and nothing else, then they don't fully understand the context for view more.

We can provide additonal context to a label without making it visible. It keeps the design tidy and makes it more accessible.

```css
.visuallyhidden {
  border: 0;
  clip: rect(0 0 0 0);
  height: 1px;
  margin: -1px;
  overflow: hidden;
  padding: 0;
  position: absolute;
  width: 1px;
}
```

```html
<a href='#'>View more <span class='visuallyhidden'>products in Healthy Snacks</span></a>
```

# Hiding Elements

Sometimes we have certain things that we do not want to make available to screen reders because that same information is accessible elsewhere.
To hide blocks from screen reader, use the [aria-hidden](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-hidden_attribute) attribute.

```html
<div aria-hidden='true'>
The information in here will be visible but not accessible via screen readers
</div>
```

# Images

### Overview
All images that provide context to the page should have thoughtful descriptions.

Imagine you have closed your eyes and have never seen the page before:
- How would someone describe the image to you?
- What are the important bits to help with the context of the page?
- Avoid using words like: "image", "illustration", "picture", etc..
  
### Inline Images
Any image that uses the `<img>` should have an `alt` property.
If the image is an illustration and doesn't provide context to the page, the `alt` can be an empty string.
If the image provides additional context to the page, it needs a thoughful description.

```html
<img src='' alt='' />
```


## Background Images
Most background images are used for decoration only and do not need alternate text.
However, sometimes background image includes important information.
Consider using a hidden div that contains a description of the image.

Alternatively, refactor the code to use `<img>` tags.

```html
<div style="background-image: url(...)">
  <span class='visuallyhidden'>description of svg</span>
</div>
```

### SVG
Add a `<title>` node inside of the svg that describes the image.
Hide the svg via `aria-hidden="true"` and add a visually hidden div that describes it.

```html
<svg>
  <title>description of svg</title>
</svg>
```

or
```html
<span class='visuallyhidden'>description of svg</span>
<svg aria-hidden='true'></svg>
```


# Input Labels
Each input should have a `<label>` associated with it:
```html
<label for="firstname">First name:</label>
<input type="text" name="firstname" id="firstname">
```

Sometimes we don't want to visually display the `<label>` so we can **visually hide** the element:

```html
<label for="firstname">First name:</label>
<input type="text" name="firstname" id="firstname" class="visuallyhidden">
```

> Note: we are using the `visuallyhidden` class defined in an earlier section


# Input Validation

When invalid input is entered into forms, we often give the input a different styling. This styling may not be accessible.
Instructing screen readers that a specific input is invalid can be done via the [aria-invalid](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-invalid_attribute) attribute

`<input aria-invalid='true' />`

# Alerts 

The web application might want to alert the user that something changed on the page, that can be done with the [role=alert](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_alert_role) attributes.

**Beware** that if you trigger multiple alerts that contain the same message, only the first one will be announced by the screen reader.
To avoid this announced-once issue, please ensure that each alert message is different.

```html
<p id="expirationWarning" role="alert">Your log in session will expire in 2 minutes</p>
```

# Live Regions

With javascript, we are often dynamically changing parts of the site without reloading the page.
Screen readers will not pick up those changes unless you specifically state that a certain region will change.
Instructing screen readers to watch these regions can be done with the [aria-live](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) attribute.

```html
<div id="results-count" aria-live="polite"></div>
```

```js
function updateResultsCount(count, searchTerm) {
  document.getElementById("results-count").innerText = `Found ${count} results for search term: ${searchTerm}`
}

updateResultsCount(10, "apple")
```

**Beware** that the container that has `aria-live` must exist on page load. If it gets added dynamically after page load, it will not be picked up by some screen readers.


# Focus

There may be a scenario where the focus need to be moved to a specific component on the page.
Maybe new results were loaded in an infinite scroll list or moving the focus to the invalid input.

Moving the focus to an input is easy with javascript via `$input.focus()` where `$input` represents the `DOMElement` (input, button, textarea, etc...).

It gets a bit more challenging when the focus needs to be moved to a non-interactive component.

The simplest way is to add [tabindex](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex) attribute to that destination element.
```html
<div tabindex="-1">my content here</div>
```
This approach presents a few problems:
- A non-interactive element gets the focused outline (which can be disabled via css `outline: 0`)
- It may be read out loud which may not be a desired behavior 
  
A way to move the focus but not force the screen reader to read it outloud is to create a empty component that can receive focus.
```html
<a class='visuallyhidden' tabindex="-1" id='resetFocus'></a>
<div tabindex="-1">my content here</div>
```
```js
const resetFocusAnchor = document.getElementById('resetFocus');
resetFocusAnchor.focus();
resetFocusAnchor.blur();
```

By following this approach, the focus will be moved after the `<a>` so be primed for the next `Tab` keypress. 



## Tooling
- [eslint-plugin-jsx-a11y](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y) - Static AST checker for accessibility rules on JSX elements.
- [Chrome Lighthouse](https://developers.google.com/web/tools/lighthouse) - Audits for performance, accessibility, progressive web apps, SEO and more.