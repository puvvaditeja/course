# DOM Manipulation & Events

## Overview

jQuery makes it simple to manipulate the Document Object Model (DOM) and handle user events. This topic covers how to create, modify, and remove elements, change content and attributes, and respond to user interactions.

---

## Getting and Setting Content

### `.text()`

Get or set the text content of elements:

```javascript
// Get text content
const headingText = $('h1').text();
console.log(headingText); // "Welcome"

// Set text content
$('h1').text('New Heading');

// Set text for multiple elements
$('p').text('Same text for all paragraphs');

// Get text from multiple elements (concatenated)
const allText = $('p').text();
```

**Note:** `.text()` gets/sets plain text only, HTML tags are escaped.

### `.html()`

Get or set the HTML content of elements:

```javascript
// Get HTML content
const content = $('#container').html();
console.log(content); // "<p>Hello <strong>World</strong></p>"

// Set HTML content
$('#container').html('<p>New <em>content</em></p>');

// Append HTML
$('#list').html($('#list').html() + '<li>New item</li>');

// Get HTML from first matched element only
const firstHtml = $('div').html(); // Only first div
```

**Warning:** Be careful with user input to avoid XSS attacks.

### `.val()`

Get or set the value of form elements:

```javascript
// Get input value
const username = $('#username').val();

// Set input value
$('#username').val('john_doe');

// Get selected option
const country = $('#country').val();

// Set selected option
$('#country').val('USA');

// Get checkbox state (use .prop() instead)
const isChecked = $('#agree').prop('checked');

// Get multiple selected options
const selectedItems = $('#multiselect').val(); // Returns array
```

### Comparison: text() vs html() vs val()

```javascript
// HTML: <div id="demo">Hello <strong>World</strong></div>

$('#demo').text();     // "Hello World"
$('#demo').html();     // "Hello <strong>World</strong>"

// For inputs
// HTML: <input id="name" value="John">

$('#name').val();      // "John"
$('#name').text();     // "" (empty)
$('#name').html();     // "" (empty)
```

---

## Manipulating Attributes and Properties

### `.attr()`

Get or set HTML attributes:

```javascript
// Get attribute value
const href = $('a').attr('href');
const src = $('img').attr('src');

// Set single attribute
$('a').attr('href', 'https://example.com');
$('img').attr('alt', 'Description');

// Set multiple attributes
$('img').attr({
    'src': 'image.jpg',
    'alt': 'My Image',
    'title': 'Click to enlarge'
});

// Remove attribute
$('a').removeAttr('target');
```

### `.prop()`

Get or set DOM properties (for boolean properties):

```javascript
// Check if checkbox is checked
const isChecked = $('#agree').prop('checked');

// Check/uncheck checkbox
$('#agree').prop('checked', true);
$('#agree').prop('checked', false);

// Disable/enable form element
$('#submit').prop('disabled', true);
$('#submit').prop('disabled', false);

// Get selected property of option
const isSelected = $('option').prop('selected');
```

### `.attr()` vs `.prop()`

Use `.prop()` for boolean properties, `.attr()` for HTML attributes:

```javascript
// For checkboxes - use .prop()
$('#checkbox').prop('checked', true);  // Correct
$('#checkbox').attr('checked', true);  // Incorrect

// For href - use .attr()
$('a').attr('href', 'page.html');      // Correct
$('a').prop('href', 'page.html');      // Also works but attr is preferred

// disabled property
$(':input').prop('disabled', true);    // Correct
$(':input').attr('disabled', 'disabled'); // Also works but prop is preferred
```

**Rule of Thumb:**
- Boolean states (checked, disabled, selected): Use `.prop()`
- HTML attributes (href, src, alt, title): Use `.attr()`

### `.data()`

Store and retrieve arbitrary data:

```javascript
// Store data
$('#user').data('userId', 12345);
$('#user').data('role', 'admin');

// Store multiple data
$('#user').data({
    userId: 12345,
    role: 'admin',
    email: 'user@example.com'
});

// Retrieve data
const userId = $('#user').data('userId');
const role = $('#user').data('role');

// Get all data
const allData = $('#user').data();

// HTML: <div id="user" data-user-id="12345" data-role="admin"></div>
// Access data attributes
const userId = $('#user').data('userId'); // Converts data-user-id to camelCase
```

---

## Manipulating CSS

### `.css()`

Get or set CSS properties:

```javascript
// Get CSS property
const color = $('p').css('color');
const fontSize = $('p').css('font-size');

// Set single CSS property
$('p').css('color', 'red');
$('p').css('font-size', '20px');

// Set multiple CSS properties
$('p').css({
    'color': 'blue',
    'font-size': '18px',
    'margin': '10px',
    'padding': '5px'
});

// Use camelCase or hyphenated
$('p').css('background-color', 'yellow');
$('p').css('backgroundColor', 'yellow'); // Both work
```

### `.addClass()`

Add one or more classes:

```javascript
// Add single class
$('p').addClass('highlight');

// Add multiple classes
$('p').addClass('highlight bold italic');

// Add class conditionally
if (isActive) {
    $('#menu').addClass('active');
}

// Add class based on function
$('div').addClass(function(index) {
    return 'item-' + index;
});
```

### `.removeClass()`

Remove one or more classes:

```javascript
// Remove single class
$('p').removeClass('highlight');

// Remove multiple classes
$('p').removeClass('highlight bold');

// Remove all classes
$('p').removeClass();

// Remove class based on function
$('div').removeClass(function(index) {
    return 'item-' + index;
});
```

### `.toggleClass()`

Toggle one or more classes:

```javascript
// Toggle single class
$('#menu').toggleClass('active');

// Toggle multiple classes
$('p').toggleClass('highlight bold');

// Toggle with boolean state
$('#menu').toggleClass('active', true);  // Always add
$('#menu').toggleClass('active', false); // Always remove

// Toggle based on condition
$('#menu').toggleClass('active', isMenuOpen);
```

### `.hasClass()`

Check if element has a class:

```javascript
// Check for class
if ($('#menu').hasClass('active')) {
    console.log('Menu is active');
}

// Use in conditions
const isHighlighted = $('p').hasClass('highlight');

// Works on first matched element
if ($('div').hasClass('container')) { // Checks first div only
    // ...
}
```

### Best Practice: Use Classes for Styling

```javascript
// Not recommended - inline styles
$('#box').css({
    'width': '200px',
    'height': '200px',
    'background': 'blue',
    'border': '1px solid black'
});

// Recommended - CSS classes
// In CSS: .box-blue { width: 200px; height: 200px; background: blue; border: 1px solid black; }
$('#box').addClass('box-blue');
```

---

## Adding and Removing Elements

### Creating Elements

```javascript
// Method 1: HTML string
const $newDiv = $('<div>Hello World</div>');

// Method 2: Element with attributes
const $newImg = $('<img>', {
    src: 'image.jpg',
    alt: 'My Image',
    class: 'thumbnail'
});

// Method 3: Complex HTML
const $newCard = $(`
    <div class="card">
        <h3>Title</h3>
        <p>Description</p>
    </div>
`);
```

### `.append()` and `.prepend()`

Add content inside an element:

```javascript
// append() - Add at the end
$('#list').append('<li>Last item</li>');

// prepend() - Add at the beginning
$('#list').prepend('<li>First item</li>');

// Append multiple items
$('#list').append('<li>Item 1</li>', '<li>Item 2</li>');

// Append jQuery object
const $newItem = $('<li>New item</li>');
$('#list').append($newItem);
```

**Visual:**
```html
<!-- Before -->
<ul id="list">
    <li>Middle item</li>
</ul>

<!-- After prepend and append -->
<ul id="list">
    <li>First item</li>      <!-- prepend -->
    <li>Middle item</li>
    <li>Last item</li>        <!-- append -->
</ul>
```

### `.appendTo()` and `.prependTo()`

Reverse syntax - add element to target:

```javascript
// Create element and append to target
$('<li>New item</li>').appendTo('#list');

// Same as: $('#list').append('<li>New item</li>');

// prepend version
$('<li>First item</li>').prependTo('#list');
```

### `.after()` and `.before()`

Add content outside an element:

```javascript
// after() - Add after the element
$('h1').after('<p>Subtitle</p>');

// before() - Add before the element
$('h1').before('<div class="logo">Logo</div>');
```

**Visual:**
```html
<!-- Before -->
<h1>Title</h1>

<!-- After before() and after() -->
<div class="logo">Logo</div>  <!-- before -->
<h1>Title</h1>
<p>Subtitle</p>                <!-- after -->
```

### `.insertAfter()` and `.insertBefore()`

Reverse syntax - insert element relative to target:

```javascript
// Create and insert after target
$('<p>Subtitle</p>').insertAfter('h1');

// Same as: $('h1').after('<p>Subtitle</p>');

// Insert before
$('<div>Header</div>').insertBefore('h1');
```

### `.wrap()`, `.wrapAll()`, `.wrapInner()`

Wrap elements with new elements:

```javascript
// wrap() - Wrap each element individually
$('p').wrap('<div class="wrapper"></div>');

// Before: <p>Text 1</p><p>Text 2</p>
// After:  <div class="wrapper"><p>Text 1</p></div>
//         <div class="wrapper"><p>Text 2</p></div>

// wrapAll() - Wrap all elements together
$('p').wrapAll('<div class="container"></div>');

// Before: <p>Text 1</p><p>Text 2</p>
// After:  <div class="container">
//             <p>Text 1</p><p>Text 2</p>
//         </div>

// wrapInner() - Wrap content inside elements
$('p').wrapInner('<strong></strong>');

// Before: <p>Text</p>
// After:  <p><strong>Text</strong></p>
```

### `.remove()` and `.detach()`

Remove elements from the DOM:

```javascript
// remove() - Remove element and all data/events
$('#temp').remove();

// Remove with selector
$('p').remove('.temporary');

// detach() - Remove but keep data/events
const $saved = $('#temp').detach(); // Can be reinserted later
// ...later...
$('#container').append($saved); // Data and events preserved
```

### `.empty()`

Remove all child elements:

```javascript
// Remove all children but keep the element
$('#container').empty();

// Before: <div id="container"><p>Text</p><span>More</span></div>
// After:  <div id="container"></div>
```

### `.replaceWith()` and `.replaceAll()`

Replace elements:

```javascript
// replaceWith() - Replace element with new content
$('h1').replaceWith('<h2>New Heading</h2>');

// replaceAll() - Reverse syntax
$('<h2>New Heading</h2>').replaceAll('h1');
```

---

## Traversing the DOM

### Parent Elements

```javascript
// .parent() - Immediate parent
$('li').parent(); // Returns ul

// .parents() - All ancestors
$('span').parents(); // All parents up to <html>

// .parents() with selector - Filter ancestors
$('span').parents('div'); // Only div ancestors

// .parentsUntil() - Ancestors up to selector
$('span').parentsUntil('body'); // All parents until body

// .closest() - First ancestor matching selector
$('span').closest('.container'); // Nearest .container
```

### Child Elements

```javascript
// .children() - Direct children
$('ul').children(); // All direct li children

// .children() with selector - Filter children
$('ul').children('.active'); // Only .active children

// .find() - All descendants
$('div').find('span'); // All span descendants
```

### Sibling Elements

```javascript
// .siblings() - All siblings
$('li').siblings();

// .siblings() with selector - Filter siblings
$('li').siblings('.active');

// .next() - Next sibling
$('li').next();

// .nextAll() - All following siblings
$('li').nextAll();

// .nextUntil() - Following siblings until selector
$('li').nextUntil('.last');

// .prev() - Previous sibling
$('li').prev();

// .prevAll() - All previous siblings
$('li').prevAll();

// .prevUntil() - Previous siblings until selector
$('li').prevUntil('.first');
```

### Filtering

```javascript
// .first() - First element
$('li').first();

// .last() - Last element
$('li').last();

// .eq() - Element at index
$('li').eq(2); // Third element (0-indexed)

// .filter() - Filter by selector
$('li').filter('.active');

// .not() - Exclude elements
$('li').not('.disabled');
```

---

## Event Handling

### Basic Event Binding

```javascript
// Click event
$('#button').click(function() {
    alert('Button clicked!');
});

// Double click
$('#button').dblclick(function() {
    console.log('Double clicked');
});

// Mouse events
$('#box')
    .mouseenter(function() { $(this).addClass('hover'); })
    .mouseleave(function() { $(this).removeClass('hover'); });

// Keyboard events
$('#input').keypress(function(e) {
    console.log('Key pressed:', e.key);
});

$('#input').keyup(function() {
    console.log('Key released');
});

// Form events
$('#myForm').submit(function(e) {
    e.preventDefault(); // Prevent default form submission
    console.log('Form submitted');
});

$('#email').change(function() {
    console.log('Email changed:', $(this).val());
});

$('#input').focus(function() {
    $(this).addClass('focused');
});

$('#input').blur(function() {
    $(this).removeClass('focused');
});
```

### `.on()` Method (Recommended)

The `.on()` method is the preferred way to attach event handlers:

```javascript
// Single event
$('#button').on('click', function() {
    console.log('Clicked');
});

// Multiple events
$('#button').on('mouseenter mouseleave', function() {
    $(this).toggleClass('hover');
});

// Multiple events with separate handlers
$('#button').on({
    mouseenter: function() {
        $(this).addClass('hover');
    },
    mouseleave: function() {
        $(this).removeClass('hover');
    },
    click: function() {
        alert('Clicked!');
    }
});

// Event with data
$('#button').on('click', { user: 'John' }, function(e) {
    console.log('User:', e.data.user);
});
```

### Event Delegation

For dynamically added elements, use event delegation:

```javascript
// Won't work for elements added later
$('.delete-btn').click(function() {
    $(this).parent().remove();
});

// Works for current and future elements
$('#list').on('click', '.delete-btn', function() {
    $(this).parent().remove();
});

// Delegation with dynamic content
$('#container').on('click', '.item', function() {
    console.log('Item clicked:', $(this).text());
});

// Can add new items and events still work
$('#container').append('<div class="item">New Item</div>');
```

### `.off()` Method

Remove event handlers:

```javascript
// Remove all click handlers
$('#button').off('click');

// Remove specific handler
function handleClick() {
    console.log('Clicked');
}
$('#button').on('click', handleClick);
$('#button').off('click', handleClick);

// Remove all event handlers
$('#button').off();

// Remove delegated event
$('#list').off('click', '.item');
```

### `.one()` Method

Bind event that runs only once:

```javascript
// Handler runs only once
$('#button').one('click', function() {
    alert('This will only show once');
});

// After first click, handler is automatically removed
```

### Event Object

```javascript
$('#button').on('click', function(event) {
    // Prevent default action
    event.preventDefault();

    // Stop event bubbling
    event.stopPropagation();

    // Event type
    console.log(event.type); // "click"

    // Target element
    console.log(event.target); // Element that triggered event

    // Current element (with delegation)
    console.log(event.currentTarget); // Element handler is attached to

    // Mouse position
    console.log('X:', event.pageX, 'Y:', event.pageY);

    // Which key/button
    console.log('Which:', event.which);

    // Timestamp
    console.log('Time:', event.timeStamp);
});
```

### `this` vs `$(this)`

```javascript
$('button').click(function() {
    // this - DOM element
    console.log(this.id);
    this.style.color = 'red';

    // $(this) - jQuery object
    console.log($(this).attr('id'));
    $(this).css('color', 'red');

    // Best practice: cache $(this)
    const $btn = $(this);
    $btn.addClass('clicked');
    $btn.text('Clicked!');
});
```

### Common Event Patterns

```javascript
// Toggle visibility
$('#toggle').click(function() {
    $('#content').toggle();
});

// Confirm before action
$('.delete-btn').click(function(e) {
    if (!confirm('Are you sure?')) {
        e.preventDefault();
    }
});

// Prevent double submission
$('#form').submit(function() {
    $(this).find(':submit').prop('disabled', true);
});

// Live search
$('#search').on('keyup', function() {
    const query = $(this).val().toLowerCase();
    $('.item').each(function() {
        const text = $(this).text().toLowerCase();
        $(this).toggle(text.includes(query));
    });
});

// Character counter
$('#message').on('keyup', function() {
    const count = $(this).val().length;
    $('#counter').text(count + ' characters');
});
```

---

## Summary

| Category | Methods | Usage |
|----------|---------|-------|
| Content | `.text()`, `.html()`, `.val()` | Get/set element content |
| Attributes | `.attr()`, `.prop()`, `.data()` | Get/set attributes and properties |
| CSS | `.css()`, `.addClass()`, `.removeClass()` | Style manipulation |
| Add Elements | `.append()`, `.prepend()`, `.after()`, `.before()` | Insert new elements |
| Remove Elements | `.remove()`, `.detach()`, `.empty()` | Remove elements |
| Traversal | `.parent()`, `.children()`, `.siblings()`, `.find()` | Navigate DOM tree |
| Events | `.on()`, `.off()`, `.one()` | Event handling |
| Event Delegation | `$(parent).on('event', 'selector', handler)` | Events for dynamic elements |

## Next Topic

Continue to [jQuery Effects](./04-jquery-effects.md) to learn about animations and visual effects.
