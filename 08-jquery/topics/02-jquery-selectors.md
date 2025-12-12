# jQuery Selectors

## Overview

jQuery selectors are one of the most powerful features of the library. They allow you to find and select HTML elements based on various criteria including element name, ID, class, attribute, type, and much more. jQuery uses CSS selector syntax, making it familiar to anyone who knows CSS.

---

## Basic Selectors

### Element Selector

Select all elements of a given type:

```javascript
// Select all paragraphs
$('p')

// Select all divs
$('div')

// Select all links
$('a')
```

**Example:**
```javascript
// Hide all paragraphs
$('p').hide();

// Change text color of all h1 elements
$('h1').css('color', 'blue');
```

### ID Selector

Select a single element by its ID (IDs should be unique):

```javascript
// Select element with ID "header"
$('#header')

// Select element with ID "main-content"
$('#main-content')
```

**Example:**
```javascript
// Show element with ID "welcome-message"
$('#welcome-message').show();

// Add content to element with ID "output"
$('#output').html('<p>Hello World</p>');
```

**Performance Note:** ID selectors are the fastest because they use native `document.getElementById()`.

### Class Selector

Select all elements with a specific class:

```javascript
// Select all elements with class "menu-item"
$('.menu-item')

// Select all elements with class "alert"
$('.alert')
```

**Example:**
```javascript
// Add border to all elements with class "highlight"
$('.highlight').css('border', '2px solid yellow');

// Remove all elements with class "error"
$('.error').remove();
```

### Universal Selector

Select all elements:

```javascript
// Select everything
$('*')

// Select all elements inside a container
$('#container *')
```

**Example:**
```javascript
// Hide all elements
$('*').hide();

// Add a class to all elements in the document
$('*').addClass('styled');
```

**Warning:** Use sparingly as it can be slow on large documents.

---

## Combining Selectors

### Multiple Selectors

Select multiple types of elements at once:

```javascript
// Select all h1, h2, and h3 elements
$('h1, h2, h3')

// Select elements with different classes
$('.warning, .error, .alert')

// Mix element types and classes
$('p, .intro, #summary')
```

**Example:**
```javascript
// Style all headings
$('h1, h2, h3, h4, h5, h6').css('font-family', 'Arial');

// Hide multiple sections
$('#sidebar, #footer, .ad-banner').hide();
```

### Descendant Selector

Select elements that are descendants of another element:

```javascript
// Select all paragraphs inside divs
$('div p')

// Select all links inside the navigation
$('nav a')

// Select all list items inside unordered lists
$('ul li')
```

**Example:**
```javascript
// Style all links in the footer
$('#footer a').css('color', 'white');

// Hide all images in articles
$('article img').hide();
```

### Child Selector

Select direct children only (not all descendants):

```javascript
// Select direct li children of ul
$('ul > li')

// Select direct p children of div
$('div > p')
```

**Example:**
```javascript
// Style only direct children
$('.menu > li').css('display', 'inline-block');

// Different from descendant selector
$('div p')     // All p elements inside div (any level)
$('div > p')   // Only direct p children of div
```

### Adjacent Sibling Selector

Select the next sibling element:

```javascript
// Select the paragraph immediately after h2
$('h2 + p')

// Select the div immediately after the header
$('#header + div')
```

**Example:**
```javascript
// Style the first paragraph after each heading
$('h2 + p').css('font-weight', 'bold');
```

### General Sibling Selector

Select all sibling elements that follow:

```javascript
// Select all paragraphs that follow h2
$('h2 ~ p')

// Select all divs that follow the header
$('#header ~ div')
```

---

## Attribute Selectors

### Has Attribute

Select elements that have a specific attribute:

```javascript
// Select all elements with a "title" attribute
$('[title]')

// Select all links with a "target" attribute
$('a[target]')
```

### Attribute Equals

Select elements where attribute exactly matches a value:

```javascript
// Select inputs with type="text"
$('input[type="text"]')

// Select links that go to example.com
$('a[href="https://example.com"]')
```

### Attribute Contains

Select elements where attribute contains a value:

```javascript
// Select all links containing "google" in href
$('a[href*="google"]')

// Select all images with "logo" in src
$('img[src*="logo"]')
```

### Attribute Starts With

Select elements where attribute starts with a value:

```javascript
// Select all links starting with "https"
$('a[href^="https"]')

// Select all images starting with "thumb"
$('img[src^="thumb"]')
```

### Attribute Ends With

Select elements where attribute ends with a value:

```javascript
// Select all links ending with ".pdf"
$('a[href$=".pdf"]')

// Select all images ending with ".png"
$('img[src$=".png"]')
```

### Attribute Not Equal

Select elements where attribute does not equal a value:

```javascript
// Select all inputs that are not type="text"
$('input[type!="text"]')
```

### Multiple Attributes

Combine multiple attribute conditions:

```javascript
// Select text inputs with class "required"
$('input[type="text"][class="required"]')

// Select external links (starting with http, not current domain)
$('a[href^="http"][href*="example.com"]')
```

### Attribute Selector Examples

```javascript
// Style all required inputs
$('input[required]').css('border', '2px solid red');

// Add icon to PDF links
$('a[href$=".pdf"]').prepend('📄 ');

// Open external links in new tab
$('a[href^="http"]').attr('target', '_blank');

// Style email inputs
$('input[type="email"]').css('background', '#f0f0f0');
```

---

## Filter Selectors

### Basic Filters

| Selector | Description | Example |
|----------|-------------|---------|
| `:first` | First element | `$('p:first')` |
| `:last` | Last element | `$('p:last')` |
| `:even` | Even-indexed elements (0, 2, 4...) | `$('tr:even')` |
| `:odd` | Odd-indexed elements (1, 3, 5...) | `$('tr:odd')` |
| `:eq(n)` | Element at index n | `$('li:eq(2)')` |
| `:gt(n)` | Elements after index n | `$('li:gt(2)')` |
| `:lt(n)` | Elements before index n | `$('li:lt(3)')` |
| `:not(selector)` | Elements that don't match | `$('p:not(.intro)')` |
| `:header` | All header elements (h1-h6) | `$(':header')` |
| `:animated` | Currently animated elements | `$(':animated')` |

**Examples:**
```javascript
// Highlight first paragraph
$('p:first').css('font-weight', 'bold');

// Zebra-stripe table rows
$('tr:even').css('background', '#f0f0f0');
$('tr:odd').css('background', '#ffffff');

// Style all but the first list item
$('li:not(:first)').css('color', 'gray');

// Select the third item
$('li:eq(2)').addClass('selected');
```

### Content Filters

| Selector | Description | Example |
|----------|-------------|---------|
| `:contains(text)` | Elements containing text | `$('p:contains("Hello")')` |
| `:empty` | Elements with no children | `$('div:empty')` |
| `:has(selector)` | Elements containing selector | `$('div:has(p)')` |
| `:parent` | Elements that are parents | `$('div:parent')` |

**Examples:**
```javascript
// Find paragraphs containing "error"
$('p:contains("error")').addClass('error-text');

// Remove empty divs
$('div:empty').remove();

// Find divs that contain images
$('div:has(img)').addClass('has-image');

// Find non-empty paragraphs
$('p:parent').css('margin', '10px');
```

### Visibility Filters

| Selector | Description | Example |
|----------|-------------|---------|
| `:hidden` | Hidden elements | `$('div:hidden')` |
| `:visible` | Visible elements | `$('div:visible')` |

**Examples:**
```javascript
// Show all hidden divs
$('div:hidden').show();

// Count visible paragraphs
const count = $('p:visible').length;

// Toggle visibility
$('div:visible').hide();
```

### Form Selectors

| Selector | Description | Example |
|----------|-------------|---------|
| `:input` | All form elements | `$(':input')` |
| `:text` | Text inputs | `$(':text')` |
| `:password` | Password inputs | `$(':password')` |
| `:radio` | Radio buttons | `$(':radio')` |
| `:checkbox` | Checkboxes | `$(':checkbox')` |
| `:submit` | Submit buttons | `$(':submit')` |
| `:reset` | Reset buttons | `$(':reset')` |
| `:button` | Button elements | `$(':button')` |
| `:image` | Image inputs | `$(':image')` |
| `:file` | File inputs | `$(':file')` |
| `:enabled` | Enabled form elements | `$(':enabled')` |
| `:disabled` | Disabled form elements | `$(':disabled')` |
| `:selected` | Selected options | `$(':selected')` |
| `:checked` | Checked checkboxes/radios | `$(':checked')` |

**Examples:**
```javascript
// Disable all text inputs
$(':text').prop('disabled', true);

// Get all checked checkboxes
const checked = $(':checkbox:checked');

// Style disabled inputs
$(':disabled').css('opacity', '0.5');

// Get selected dropdown options
const selected = $('select option:selected').val();

// Clear all form inputs
$(':input').val('');
```

---

## Selector Methods

### `.find()`

Find descendant elements:

```javascript
// Find all paragraphs inside #content
$('#content').find('p')

// Find all links inside list items
$('li').find('a')
```

### `.filter()`

Reduce the set of matched elements:

```javascript
// Filter paragraphs to only those with class "intro"
$('p').filter('.intro')

// Filter using a function
$('li').filter(function(index) {
    return index % 2 === 0; // Even indices
})
```

### `.not()`

Remove elements from the set:

```javascript
// All paragraphs except those with class "intro"
$('p').not('.intro')

// All list items except the first
$('li').not(':first')
```

### `.has()`

Keep elements that contain specific descendants:

```javascript
// Divs that contain paragraphs
$('div').has('p')

// List items that contain links
$('li').has('a')
```

### `.is()`

Check if elements match a selector:

```javascript
// Check if element is visible
if ($('#myDiv').is(':visible')) {
    console.log('Visible!');
}

// Check if element has a class
if ($('#myDiv').is('.active')) {
    console.log('Active!');
}
```

### `.eq()`

Reduce to element at specific index:

```javascript
// Get the third paragraph (index 2)
$('p').eq(2)

// Get the last list item
$('li').eq(-1)
```

### `.first()` and `.last()`

Get first or last element:

```javascript
// First paragraph
$('p').first()

// Last list item
$('li').last()
```

### `.slice()`

Reduce to a subset:

```javascript
// Get elements from index 2 to 5
$('li').slice(2, 5)

// Get all elements from index 3 onward
$('li').slice(3)
```

---

## Selector Performance

### Performance Hierarchy (Fastest to Slowest)

1. **ID Selector**: `$('#myId')` - Uses native `getElementById()`
2. **Element Selector**: `$('div')` - Uses native `getElementsByTagName()`
3. **Class Selector**: `$('.myClass')` - Uses native `getElementsByClassName()`
4. **Attribute Selector**: `$('[href]')` - Uses `querySelectorAll()`
5. **Pseudo Selectors**: `$(':hidden')` - Custom jQuery implementation (slowest)

### Optimization Tips

```javascript
// Slow - searches entire document
$('.menu-item')

// Faster - narrow the search scope
$('#navigation').find('.menu-item')

// Faster - use context parameter
$('.menu-item', '#navigation')

// Fastest - use ID selector first
$('#navigation .menu-item')
```

### Cache Selectors

```javascript
// Bad - selector runs every time
$('#myDiv').addClass('active');
$('#myDiv').css('color', 'red');
$('#myDiv').fadeIn();

// Good - selector runs once
const $myDiv = $('#myDiv');
$myDiv.addClass('active');
$myDiv.css('color', 'red');
$myDiv.fadeIn();
```

### Be Specific on the Right Side

```javascript
// Slow - too broad
$('.myClass')

// Faster - more specific
$('div.myClass')

// Balance specificity with maintainability
$('#container .myClass')
```

---

## Common Patterns

### Select and Filter

```javascript
// Get all visible paragraphs with class "intro"
$('p.intro:visible')

// Get all checked checkboxes in a form
$('#myForm :checkbox:checked')

// Get all external links
$('a[href^="http"]:not([href*="mysite.com"])')
```

### Selecting Within Context

```javascript
// Search within a specific container
const $container = $('#main-content');
const $paragraphs = $container.find('p');
const $links = $container.find('a');

// Or using context parameter
const $items = $('.menu-item', '#navigation');
```

### Working with Forms

```javascript
// Get all form inputs
const $inputs = $('#myForm :input');

// Get all text inputs that are enabled
const $textInputs = $(':text:enabled');

// Get all required fields that are empty
const $emptyRequired = $(':input[required]').filter(function() {
    return $(this).val() === '';
});

// Get form values
const formData = {
    username: $('#username').val(),
    email: $('#email').val(),
    country: $('#country option:selected').val()
};
```

### Dynamic Selection

```javascript
// Select based on variable
const className = 'active';
$('.' + className)

// Select based on data attribute
const userId = '12345';
$('[data-user-id="' + userId + '"]')

// Build complex selector
const selector = 'div.' + className + '[data-id="' + userId + '"]';
$(selector)
```

---

## Best Practices

### 1. Use Specific Selectors

```javascript
// Less specific (slower)
$('.item')

// More specific (faster)
$('#menu .item')
```

### 2. Cache Selectors

```javascript
// Bad
$('#menu').show();
$('#menu').addClass('active');

// Good
const $menu = $('#menu');
$menu.show();
$menu.addClass('active');
```

### 3. Use Method Chaining

```javascript
// Works but verbose
const $div = $('#myDiv');
$div.addClass('active');
$div.css('color', 'red');
$div.fadeIn();

// Better with chaining
$('#myDiv')
    .addClass('active')
    .css('color', 'red')
    .fadeIn();
```

### 4. Prefer Classes Over Direct Styling

```javascript
// Not ideal
$('#myDiv').css({
    'color': 'red',
    'font-size': '20px',
    'border': '1px solid black'
});

// Better - define in CSS
$('#myDiv').addClass('styled');
```

### 5. Use Event Delegation for Dynamic Elements

```javascript
// Won't work for dynamically added items
$('.menu-item').click(function() { ... });

// Works for current and future items
$('#menu').on('click', '.menu-item', function() { ... });
```

---

## Summary

| Category | Key Selectors | Example |
|----------|--------------|---------|
| Basic | `#id`, `.class`, `element` | `$('#header')`, `$('.menu')`, `$('p')` |
| Hierarchy | `parent child`, `parent > child` | `$('div p')`, `$('ul > li')` |
| Attribute | `[attr]`, `[attr=value]`, `[attr^=value]` | `$('[required]')`, `$('[type="text"]')` |
| Filter | `:first`, `:last`, `:even`, `:odd` | `$('p:first')`, `$('tr:even')` |
| Content | `:contains()`, `:empty`, `:has()` | `$('p:contains("text")')` |
| Form | `:input`, `:checked`, `:selected` | `$(':checkbox:checked')` |
| Methods | `.find()`, `.filter()`, `.not()` | `$('#main').find('p')` |

## Next Topic

Continue to [DOM Manipulation & Events](./03-dom-manipulation-events.md) to learn how to modify the DOM and handle user interactions.
