# Web Accessibility Tips

The following accessibility tips and tricks address some technical ways of building accessible web pages. 
There are disagreements within assistive technology circles on how to handle the [WAI-ARIA](https://www.w3.org/TR/wai-aria) specificiations.
There are many opinons on how to build accessible web pages and the best method to use.
The following tips and tricks are how I managed to create accessible web pages that work with most modern assistive technologies.

## Overview
 - [Different Methods of Navigating a Page](#navigating)
 - [Semantic HTML](#semanticHTML)
    - [Lists](#lists)
    - [Links and Buttons](#links)
    - [Landmarks](#landmarks)
    - [Navigation](#navigation)
 - [The `role` attribute](#role)
 - [Providing additional context](#additionalContext)
 - [CSS hidden text](#hiddenText)
 - [`aria-describedby` and `aria-labelledby`](#describedByLabelledBy)
 - [Hiding Elements](#hiding)
 - [Images](#images)
    - [The `alt` attribute](#alt)
    - [Background Images](#backgroundImages)
    - [SVG](#svg)
 - [Input Labels](#inputLabels)
 - [Input Validation](#inputValidation)
 - [Contrast](#contrast)
 - [Alerts](#alerts)
 - [Live Regions](#liveRegions)
 - [Collapsible/Expandable Content](#expandableContent)
 - [Focus](#focus)
 - [Layout and the Tab Order](#tabOrder)
 - [Resources](#resources)

## <a name="navigating"></a>Different Methods of Navigating a Page

- **Mouse users**
- **Touch-screen users**
  - devices like a phone/tablet, interacts with the screen directly
  - tappable areas should be large enough (at least 32px x 32px)
- **Keyboard navigation**
  - uses keyboard to navigate the page, mostly through the Tab key
  - usually navigating between [interactive elements](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Content_categories#interactive_content) like text inputs, buttons, etc...
- **Screen reader**
  - assistive technology that reads the page
  - offers a "virtual cursor" where it highlights areas on the page with a virtual cursor
  - navigates page using special keyboard keys
  
When building a feature consider how it can be navigated through multiple navigation methods. 

For example: a common pattern is to use the mouse over state to reveal a menu.
That sort of functionality works well for mouse users, but it's almost inaccessible by touch/keyboard/screen readers.


## <a name="semanticHTML"></a>Semantic HTML
[Semantic HTML](https://developer.mozilla.org/en-US/docs/Glossary/Semantics#semantics_in_html) is the foundation of accessibility in a web application. Using the various [HTML elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Element) to reinforce the meaning of information in our websites will often give us accessibility for free.


### <a name="lists"></a>Lists

Any time there is content that would benefit from announcing how many items are there, use a list.
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

### <a name="links"></a>Links and Buttons
Non-interactive elements like `div` with click listeners are not recognized by screen readers and will be skipped.
- If there is a url, use `<a href='url'>` 
- If there is no url and clicking on it triggers some action on a page, use a `<button>`

```html
<!-- bad -->
<div onclick="handleClick()">not be detected by screen reader/keyboard</div>

<!-- good -->
<a href=''>read more</a>
<button onclick="handleClick()">detected as interactive element</button>
```

### <a name="landmarks"></a>Landmarks
Using the correct landmark elements helps assistive technologies navigate the page more efficiently.
See full [list of available landmarks in HTML5](https://www.w3.org/TR/wai-aria-practices/examples/landmarks/HTML5.html)
```html
<!-- bad -->
<div class="sidebar"></div>
<div class="main-content"></div>

<!-- good -->
<aside class="sidebar"></aside>
<main class="main-content"></main>
```

### <a name="navigation"></a>Navigation
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
  <ul aria-label="Main Navigation">
    <li><a href=''>Link 1</a></li>
  </ul>
</nav>
```

## <a name="role"></a>The `role` attribute
Most of the semantic HTML elements can be expressed as non-standard elements (ie. `<div>`, `<span>`, etc...) elements with a specific [`role` attribute](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques#roles).
However, more often than not, screen readers don't handle these "faked" elements the same way. 
These inconsistencies might lead one to add additional functionality (keyboard shortcuts, announcements) to get the same behavior as a semantic element.

Rule of thumb: if there is a semantic element for the purpose, use semantic element.


## <a name="additionalContext"></a>Providing additional context 

There are times where the content and styling around an interactive element provides the context for the label for that element. For example: Consider a page where there are many links that say "View more" and nothing else.

Some ways assistive technologies navigate a page is by reciting content on the page, like all links in a page. 
Screen readers will not describe the styling surrounding the entity they are reading.
This poses a problem when there are many links that don't have unique labels.

When screen reader users recite all links on the page, they won't fully understand the context for the view more link. Providing additional context via one of the methods below can help.

## <a name="hiddenText"></a>CSS hidden text

We can provide additonal context to a label without making it visible. It keeps the design tidy as designers intended and makes it more accessible to assistive technologies.
```html
<a href='#'>View more <span class='visually-hidden'>products in Healthy Snacks</span></a>
```

The `visually-hidden` css class moves the content out of the view so visual users will not see it, but screen readers will.

The `visually-hidden` css declaration can be seen below:

```css
.visually-hidden {
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

Why not just use `display: none;`? If you were to do so the element would have it's display turned off. This will make the element and all it's descendants inaccessible to screen readers.

## <a name="hiddenText"></a>aria-label

The [aria-label](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-label_attribute) property allows us to override what the screen reader would normally announce.

```html
<a href='/product/chocolate' aria-label="Add chocolate to cart">Add to Cart</a>
```


## <a name="describedByLabelledBy"></a>`aria-describedby` and `aria-labelledby`

The following two attributes of providing additional context have inconsistent behavior among assistive technologies. Consider using `aria-label` or the hidden text approach demonstrated in the section above.

The [aria-describedby](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-describedby_attribute) property allows us to declare multiple element ids that provide additional details to the element's label.

```html
<div id='product-title'>Chocolate</div>
<a href='/product/chocolate' aria-describedby="product-title">Add to Cart</a>
```

> aria-describedby will announce something like: "Add to cart, Chocolate"


The [aria-labelledby](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-labelledby_attribute) attribute allows us to declare multiple element ids that compose the label.
```html
<div id="myBillingId">Billing</div>

<div>
    <div id="myNameId">Name</div>
    <input type="text" aria-labelledby="myBillingId myNameId"/>
</div>
```

> When moving the focus to the Name input, the aria-labelledby will announce: "input, text, Billing Name"


## <a name="hiding"></a>Hiding Elements

Sometimes we have certain things that we do not want to make available to screen readers because that same information is accessible elsewhere.
To hide elements from screen reader use the [aria-hidden](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-hidden_attribute) attribute, but it __will still be visually rendered__.

```html
<div aria-hidden='true'>
  The information in here will be visible, but not accessible via screen readers.
  <p>Children will also be hidden</p>
</div>
```

## <a name="images"></a>Images

Any image that provides additional information to the page that is not provided in the text surrounding it should have thoughtful descriptions.

### <a name="alt"></a>The `alt` attribute
Any image that uses the `<img>` must have an `alt` attribute -- even if it's empty. Determining whether the `alt` is empty or not can be difficult. When In doubt consult the [WC3 alt decision tree](https://www.w3.org/WAI/tutorials/images/decision-tree/).

#### First determine if the `alt` attribute should be empty

In some cases the `alt` attribute should be an empty string when the image:

- Is purely decorative. Ex: a button says "settings" and has an image of a gear. The gear is decorative, it provides no additional information.
- Provides no additional information, but is there to convey a mood. Ex: a lifestyle image followed by a form. The lifestyle image, though full of content, provides no meaningful relation or additional context to the main content (ie the form).
- The content of the image is conveyed in the surrounding text. Ex: an image of a cat with a caption underneath that says "cat". Adding alt text stating "cat" would be redundent

```html
<img src='./someDecorativeImg.svg' alt='' />
```

#### Determining the non-empty `alt` attribute value

If the image provides additional context to the page, it needs a thoughful description.

Imagine you have closed your eyes and have never seen the image before:
- How would someone describe the image to you?
- What the image provides additional context to the page?
- Avoid using words like: "image", "illustration", "picture", etc... since the user will know they are looking at a picture.
- Be as succient as possible.

```html
<img src='./dog.svg' alt='bull dog' />
```

### <a name="backgroundImages"></a>Background Images
Most background images are used for decoration only and do not need alternate text.
However, sometimes background image includes important information and/or provide context.
Consider using a hidden div that contains a description of the image.

Alternatively, refactor the code to use `<img>` tags.

```html
<div style="background-image: url(...)">
  <span class='visually-hidden'>description of background image</span>
</div>
```

### <a name="svg"></a>SVG

When possible SVG images are prefered over bitmap. Some uses use screen enlargers to better view the content; SVG images scale losslessly while bitmap images become pixelated.

1. Add a `<title>` node inside of the svg that describes the image.

```html
<svg>
  <title>description of svg</title>
</svg>
```

2. Hide the svg via `aria-hidden="true"` and add a visually hidden element that describes it.
```html
<span class='visually-hidden'>description of svg</span>
<svg aria-hidden='true'></svg>
```


## <a name="inputLabels"></a>Input Labels

Each input should have a `<label>` associated with it:
```html
<label for="firstname">First name:</label>
<input type="text" name="firstname" id="firstname">
```

Sometimes we don't want to visually display the `<label>` so we can [**visually hide**](#hiding) the element using our previously described `visually-hidden` class:

```html
<label for="firstname" class="visually-hidden">First name:</label>
<input type="text" name="firstname" id="firstname">
```


## <a name="inputValidation"></a>Input Validation

Inputs are often given different styling to denote an invalid input. This styling may not be obvious to assistive technologies. Styling should never be the sole way of denoting an invalid input. Instead styling should be used in conjunction with a text message and the [aria-invalid](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-invalid_attribute) attribute.

```html
<input aria-invalid='true' />
```

## <a name="disablingInputs"></a>Disabling Inputs

Assistive technologies wouldn't announce the disabled styling so the disabled state should be indicated using the [aria-disabled](https://www.digitala11y.com/aria-disabled-state/) attribute. 

There are exceptions where the inputs are styled as visually disabled and they don't have the `disabled` attribute declared. For example: a checkbox needs to be visually disabled but it should still be focusable. We'd want this so its content is still accessible to the screen reader.

```html
<button aria-disabled='true' class='button--disabled'>Save</button>
```

## <a name="contrast"></a>Contrast

Contrast is an important consideration when styling text and its background. If contrast is too low the text can become illegible. "Contrast" is defined as a measure of the difference in perceived brightness between two colors (text color and background color). Contrast is expressed as a ratio ranging from 1:1 (white on white) to 21:1 (black on a white). To be legible the contrast must be 1:4.5 at a miniumum. Use WebAIM's [contrast checker](https://webaim.org/resources/contrastchecker/) to test text and background color.

## <a name="alerts"></a>Alerts 

The web pages might want to inform the user that something changed on the page, that can be done with specific [role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_alert_role) values.

There are many [types of alerts and live regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_alert_role)


```html
<p role="alert">There was an error saving your changes</p>
<p role="status">Product XYZ has been added to cart</p>
```

## <a name="liveRegions"></a>Live Regions

Javascript offers the ability to dynamically change parts of the page without reloading the page.
Screen readers will not pick up those changes to the page unless that area is marked as a live region.
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

**Beware:** if you trigger multiple announcements that contain the same message, some screen readers only announce the first one and ignore subsequent ones.
To avoid this announced-once issue, please ensure that each alert message is different.


**Beware:** the container that has `aria-live` must exist on the DOM before the content within is modified.
If `aria-live` container is added along with new content it will not be picked up by some screen readers.

A related attribute is [aria-atomic](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions#additional_live_region_attributes) which indicates to the screen reader whether it should announce:
- the entire block when anything changes, or
- the individual change by itself

## <a name="expandableContent"></a>Collapsible/Expandable Content

A common design pattern is to have a button that reveals some section, like an accordion or a modal.
The button that performs the action of revealing a section should be annotated with:
- [aria-haspopup](https://www.digitala11y.com/aria-haspopup-properties/) to indicate that the button reveals additional content when interacted.
- [aria-expanded](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/button_role#associated_aria_roles_states_and_properties) to indicate the expanded status of the content 


```html
<!-- collapsed state -->
<button aria-haspopup="true" aria-expanded="false">Learn more about foobar</button>
<div class="collapsed"></div>

<!-- expanded state -->
<button aria-haspopup="true" aria-expanded="true">Learn more about foobar</button>
<div class="expanded">Lorem ipsum...</div>
```

## <a name="focus"></a>Focus

There will be scenarios where the focus need to be moved to a specific area on the page, some of those reasons may be:
- Moving the focus to the invalid input
- Moving the focus to a newly added element
- New search results were loaded in an infinite scroll list 

Moving the focus to an input is easy with javascript via `$input.focus()` where `$input` represents the `DOMElement` (input, button, textarea, etc...).

It gets a bit more challenging when the focus needs to be moved to a non-interactive component like a `<div>`.

The simplest way is to add [tabindex](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex) attribute to that destination element.
```html
<div tabindex="-1">my content here</div>
```
This approach presents a few problems:
- A non-interactive element gets the focused outline (which can be disabled via css `outline: 0`)
- It may be read out loud which may not be a desired behavior 

  
A way to move the focus, but not force the screen reader to read it outloud is to create a empty component that can receive focus.
Then we can focus to that element and the blur out of it. That will effectively reset the focus to below the empty component.
```html
<a class='visually-hidden' tabindex="-1" id='resetFocus'></a>
<div tabindex="-1">my content here</div>
```
```js
const resetFocusAnchor = document.getElementById('resetFocus');
resetFocusAnchor.focus();
resetFocusAnchor.blur();
```

By following this approach, the focus will be moved after the `<a>` so be primed for the next `Tab` keypress. 

## <a name="tabOrder"></a>Layout and the Tab Order

We can specify the `tabindex` of each element in a page by assigning a number that indicates their tab order.
However, that approach is hard to maintain, specially in a dynamic page.

A more forgiving way is to let the browser dictate the tab order. The tab order often follows the html structure:
```html
<a href=''>first selected</a>
<a href=''>second selected</a>
<a href=''>third selected</a>
```

Designs are sometimes complex and there are elements on the page that are declared far apart from each other, but appear visually together (usually via css absolute positioning).
Consider the following example: this is a product listing that uses css to move the "see reviews" right below the title.


```html
<div class='product'>
  <a href='#'>Chocolate Pretzel</a>
  <div class='description'>
    Enjoy the combination of salty, 
    crunchy pretzels and peanuts with sweet, 
    rich, chocolaty flavor in these snack bars.
    <a href='#'>Read more</a>
  </div>
  <a href='#' class='reviews'>See reviews</a>
</div>
```

```css
.product {
  position: relative;
}
.reviews {
  position: absolute;
  top: 15px;
}
.description {
  margin-top: 20px;
}
```

This causes the tab order to feel unnatural because the tab order will go from the product title to "read more" in the description and finally land on "see reviews".
Visually the expectation would be to go from product title, see reviews, and finally read more.

Consider grouping related elements together to maintain a sensible tab order:
 ```html
<div class='product'>
  <a href='#'>Chocolate Pretzel</a>
  <a href='#'>See reviews</a>
  <div>
    Enjoy the combination of salty, 
    crunchy pretzels and peanuts with sweet, 
    rich, chocolaty flavor in these snack bars.
    <a href='#'>Read more</a>
  </div>
</div>
```


## <a name="resources"></a>Resources
- [eslint-plugin-jsx-a11y](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y) - Static AST checker for accessibility rules on JSX elements.
  - informs about missing properties and/or their values
- [Chrome Lighthouse](https://developers.google.com/web/tools/lighthouse) - Audits for performance, accessibility, progressive web apps, SEO and more.
- [WebAIM](https://webaim.org/) - Web accessibility in mind, a good resource for info on ARIA, accessibile images, and styling for web accessibility.
- [WC3 WAI](https://www.w3.org/WAI/) - WC3's web accessibility initiative 
- [How People with Disabilities Use the Web](https://www.w3.org/WAI/people-use-web/) - From WC3's WAI, introduces how people with disabilities, including people with age-related impairments, use the Web.
- [Contrast Checker Tool](https://webaim.org/resources/contrastchecker/) - Used to check if text color and background color have an acceptable [contrast ratio](#contrast). 
