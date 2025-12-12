# DOM Manipulation

## What is the DOM?

The Document Object Model (DOM) is a programming interface for HTML and XML documents. It represents the page structure as a tree of objects that can be manipulated with JavaScript.

### DOM Tree Structure

```
document
    └── html
        ├── head
        │   ├── title
        │   └── meta
        └── body
            ├── header
            │   └── h1
            ├── main
            │   ├── section
            │   │   ├── h2
            │   │   └── p
            │   └── div
            │       ├── ul
            │       │   ├── li
            │       │   ├── li
            │       │   └── li
            │       └── button
            └── footer
```

### DOM Node Types

| Node Type | Description | Example |
|-----------|-------------|---------|
| Document | Root node | `document` |
| Element | HTML tags | `<div>`, `<p>`, `<span>` |
| Text | Text content | Text inside elements |
| Attribute | Element attributes | `class="container"` |
| Comment | HTML comments | `<!-- comment -->` |

### Understanding the DOM

```javascript
// The document object represents the entire page
console.log(document);

// Document properties
console.log(document.title);        // Page title
console.log(document.URL);          // Current URL
console.log(document.domain);       // Domain name
console.log(document.doctype);      // DOCTYPE
console.log(document.head);         // <head> element
console.log(document.body);         // <body> element
console.log(document.characterSet); // Character encoding

// Document collections
console.log(document.all);         // All elements (deprecated)
console.log(document.forms);       // All forms
console.log(document.links);       // All links
console.log(document.images);      // All images
console.log(document.scripts);     // All scripts
```

---

## Selecting Elements

JavaScript provides multiple methods to select DOM elements.

### getElementById

```javascript
// HTML: <div id="header">Welcome</div>

// Select element by ID (returns single element or null)
const header = document.getElementById('header');
console.log(header);        // <div id="header">Welcome</div>
console.log(header.textContent); // "Welcome"

// If element doesn't exist
const notFound = document.getElementById('nonexistent');
console.log(notFound);      // null
```

### getElementsByClassName

```javascript
// HTML: <div class="card">Card 1</div>
//       <div class="card">Card 2</div>
//       <div class="card">Card 3</div>

// Select elements by class name (returns HTMLCollection)
const cards = document.getElementsByClassName('card');
console.log(cards);         // HTMLCollection(3)
console.log(cards.length);  // 3
console.log(cards[0]);      // First card element

// Convert to array for array methods
const cardsArray = Array.from(cards);
cardsArray.forEach(card => {
    console.log(card.textContent);
});

// HTMLCollection is live - updates automatically
```

### getElementsByTagName

```javascript
// HTML: <p>Paragraph 1</p>
//       <p>Paragraph 2</p>
//       <p>Paragraph 3</p>

// Select elements by tag name (returns HTMLCollection)
const paragraphs = document.getElementsByTagName('p');
console.log(paragraphs);    // HTMLCollection(3)

// Select all div elements
const divs = document.getElementsByTagName('div');

// Select all elements
const allElements = document.getElementsByTagName('*');
```

### querySelector

```javascript
// HTML: <div class="container">
//         <p class="intro">First paragraph</p>
//         <p>Second paragraph</p>
//       </div>

// Select first matching element (returns single element or null)
const container = document.querySelector('.container');
const intro = document.querySelector('.intro');
const firstParagraph = document.querySelector('p');

// CSS selectors
const specificDiv = document.querySelector('div.container');
const nestedPara = document.querySelector('.container p');
const idElement = document.querySelector('#header');

// Attribute selectors
const input = document.querySelector('input[type="text"]');
const link = document.querySelector('a[href="#home"]');

// Pseudo-selectors
const firstChild = document.querySelector('li:first-child');
const lastChild = document.querySelector('li:last-child');
const nthChild = document.querySelector('li:nth-child(2)');
```

### querySelectorAll

```javascript
// HTML: <ul>
//         <li class="item">Item 1</li>
//         <li class="item">Item 2</li>
//         <li class="item">Item 3</li>
//       </ul>

// Select all matching elements (returns NodeList)
const items = document.querySelectorAll('.item');
console.log(items);         // NodeList(3)
console.log(items.length);  // 3

// NodeList can use forEach directly
items.forEach(item => {
    console.log(item.textContent);
});

// Complex selectors
const allParagraphs = document.querySelectorAll('p');
const containerItems = document.querySelectorAll('.container .item');
const multipleSelectors = document.querySelectorAll('p, .intro, #header');

// NodeList is static - doesn't update automatically
```

### Comparison of Selection Methods

| Method | Returns | Live/Static | Usage |
|--------|---------|-------------|-------|
| `getElementById` | Single element | N/A | Fast, specific ID |
| `getElementsByClassName` | HTMLCollection | Live | Select by class |
| `getElementsByTagName` | HTMLCollection | Live | Select by tag |
| `querySelector` | Single element | N/A | Flexible CSS selector |
| `querySelectorAll` | NodeList | Static | Multiple elements |

---

## Modifying Content

### textContent

```javascript
// HTML: <p id="demo">Original text</p>

const para = document.getElementById('demo');

// Get text content
console.log(para.textContent); // "Original text"

// Set text content (removes all HTML)
para.textContent = 'New text content';

// textContent returns all text including hidden elements
// HTML: <div id="content">
//         Visible text
//         <span style="display:none">Hidden text</span>
//       </div>
const content = document.getElementById('content');
console.log(content.textContent); // "Visible text Hidden text"
```

### innerHTML

```javascript
// HTML: <div id="container">Original content</div>

const container = document.getElementById('container');

// Get HTML content
console.log(container.innerHTML); // "Original content"

// Set HTML content (parses HTML)
container.innerHTML = '<p>New <strong>HTML</strong> content</p>';

// Append HTML
container.innerHTML += '<p>Additional paragraph</p>';

// Security warning: Be careful with user input
// const userInput = '<img src=x onerror="alert(1)">';
// container.innerHTML = userInput; // XSS vulnerability!
```

### innerText

```javascript
// HTML: <div id="text">
//         Visible text
//         <span style="display:none">Hidden text</span>
//       </div>

const text = document.getElementById('text');

// innerText returns only visible text
console.log(text.innerText);    // "Visible text"

// textContent returns all text
console.log(text.textContent);  // "Visible text Hidden text"

// innerText is aware of CSS styling
```

### Element Attributes

```javascript
// HTML: <img id="logo" src="logo.png" alt="Logo">

const img = document.getElementById('logo');

// Get attribute
console.log(img.getAttribute('src'));  // "logo.png"
console.log(img.getAttribute('alt'));  // "Logo"

// Set attribute
img.setAttribute('src', 'new-logo.png');
img.setAttribute('title', 'Company Logo');

// Remove attribute
img.removeAttribute('title');

// Check if attribute exists
console.log(img.hasAttribute('alt'));  // true

// Direct property access (for standard attributes)
console.log(img.src);   // "http://example.com/new-logo.png" (absolute URL)
img.src = 'logo.png';
img.alt = 'New Logo';

// Data attributes
// HTML: <div id="user" data-user-id="123" data-role="admin"></div>
const user = document.getElementById('user');
console.log(user.dataset.userId);  // "123"
console.log(user.dataset.role);    // "admin"
user.dataset.status = 'active';    // Adds data-status="active"
```

### Style Manipulation

```javascript
// HTML: <div id="box">Box</div>

const box = document.getElementById('box');

// Get computed style
const styles = window.getComputedStyle(box);
console.log(styles.color);
console.log(styles.backgroundColor);

// Set inline styles
box.style.color = 'blue';
box.style.backgroundColor = 'lightgray';
box.style.fontSize = '20px';
box.style.padding = '10px';
box.style.borderRadius = '5px';

// Camel case for multi-word properties
box.style.marginTop = '20px';
box.style.textAlign = 'center';

// Set multiple styles
Object.assign(box.style, {
    color: 'white',
    backgroundColor: 'navy',
    padding: '20px',
    borderRadius: '10px'
});

// Remove style
box.style.color = '';
box.style.removeProperty('background-color');
```

### Class Manipulation

```javascript
// HTML: <div id="element" class="box active">Content</div>

const element = document.getElementById('element');

// className - get/set all classes as string
console.log(element.className);  // "box active"
element.className = 'container'; // Replaces all classes

// classList - modern, more flexible
console.log(element.classList);  // DOMTokenList

// Add class
element.classList.add('highlight');
element.classList.add('large', 'bold'); // Multiple classes

// Remove class
element.classList.remove('active');
element.classList.remove('large', 'bold');

// Toggle class
element.classList.toggle('hidden'); // Adds if not present, removes if present
element.classList.toggle('active', true);  // Force add
element.classList.toggle('active', false); // Force remove

// Check if class exists
console.log(element.classList.contains('box')); // true

// Replace class
element.classList.replace('box', 'container');

// Iterate over classes
element.classList.forEach(className => {
    console.log(className);
});
```

---

## Traversing the DOM

Navigate between elements in the DOM tree.

### Parent Nodes

```javascript
// HTML: <div id="parent">
//         <p id="child">Child element</p>
//       </div>

const child = document.getElementById('child');

// Parent element
console.log(child.parentElement);  // <div id="parent">
console.log(child.parentNode);     // <div id="parent"> (same in most cases)

// Climb up the tree
console.log(child.parentElement.parentElement); // Body or higher

// Closest ancestor matching selector
const container = child.closest('.container');
const section = child.closest('section');
```

### Child Nodes

```javascript
// HTML: <ul id="list">
//         <li>Item 1</li>
//         <li>Item 2</li>
//         <li>Item 3</li>
//       </ul>

const list = document.getElementById('list');

// All child nodes (includes text nodes and comments)
console.log(list.childNodes);      // NodeList (includes whitespace)

// Only element children
console.log(list.children);        // HTMLCollection
console.log(list.children.length); // 3

// First and last child
console.log(list.firstChild);         // Text node (whitespace)
console.log(list.firstElementChild);  // <li>Item 1</li>
console.log(list.lastChild);          // Text node (whitespace)
console.log(list.lastElementChild);   // <li>Item 3</li>

// Check if has children
console.log(list.hasChildNodes());    // true
console.log(list.childElementCount);  // 3
```

### Sibling Nodes

```javascript
// HTML: <div>
//         <p id="first">First</p>
//         <p id="middle">Middle</p>
//         <p id="last">Last</p>
//       </div>

const middle = document.getElementById('middle');

// Next sibling
console.log(middle.nextSibling);        // Text node (whitespace)
console.log(middle.nextElementSibling); // <p id="last">

// Previous sibling
console.log(middle.previousSibling);        // Text node (whitespace)
console.log(middle.previousElementSibling); // <p id="first">

// Navigate through siblings
let current = document.querySelector('p');
while (current) {
    console.log(current.textContent);
    current = current.nextElementSibling;
}
```

### Creating and Adding Elements

```javascript
// Create new element
const newDiv = document.createElement('div');
newDiv.textContent = 'New div content';
newDiv.className = 'box';
newDiv.id = 'new-box';

// Create text node
const textNode = document.createTextNode('Hello, World!');

// Append to parent (adds at the end)
const container = document.getElementById('container');
container.appendChild(newDiv);

// Insert before specific element
const reference = document.getElementById('reference');
container.insertBefore(newDiv, reference);

// Modern methods (ES6+)
container.append(newDiv);                    // Adds at end (can add multiple)
container.prepend(newDiv);                   // Adds at beginning
reference.before(newDiv);                    // Adds before reference
reference.after(newDiv);                     // Adds after reference
reference.replaceWith(newDiv);               // Replaces reference with newDiv

// Insert adjacent HTML
const element = document.getElementById('target');
element.insertAdjacentHTML('beforebegin', '<p>Before element</p>');
element.insertAdjacentHTML('afterbegin', '<p>Start of element</p>');
element.insertAdjacentHTML('beforeend', '<p>End of element</p>');
element.insertAdjacentHTML('afterend', '<p>After element</p>');

// Clone element
const original = document.getElementById('original');
const clone = original.cloneNode(true);  // true = deep clone (includes children)
container.appendChild(clone);
```

### Removing Elements

```javascript
// HTML: <div id="container">
//         <p id="para">Remove me</p>
//       </div>

const para = document.getElementById('para');
const container = document.getElementById('container');

// Remove element (modern)
para.remove();

// Remove child (traditional)
container.removeChild(para);

// Remove all children
container.innerHTML = '';
// OR
while (container.firstChild) {
    container.removeChild(container.firstChild);
}
// OR (modern)
container.replaceChildren(); // Removes all children

// Replace element
const newPara = document.createElement('p');
newPara.textContent = 'New paragraph';
para.replaceWith(newPara);
// OR (traditional)
container.replaceChild(newPara, para);
```

---

## Events and Event Listeners

Events are actions that happen in the browser, such as clicks, key presses, page loads, etc.

### Common Event Types

| Category | Events |
|----------|--------|
| Mouse | `click`, `dblclick`, `mousedown`, `mouseup`, `mousemove`, `mouseenter`, `mouseleave`, `mouseover`, `mouseout` |
| Keyboard | `keydown`, `keyup`, `keypress` |
| Form | `submit`, `change`, `input`, `focus`, `blur` |
| Document | `DOMContentLoaded`, `load`, `resize`, `scroll` |
| Clipboard | `copy`, `cut`, `paste` |

### addEventListener

```javascript
// HTML: <button id="btn">Click me</button>

const btn = document.getElementById('btn');

// Add event listener
btn.addEventListener('click', function() {
    console.log('Button clicked!');
});

// Named function
function handleClick() {
    console.log('Button clicked!');
}
btn.addEventListener('click', handleClick);

// Arrow function
btn.addEventListener('click', () => {
    console.log('Button clicked!');
});

// Multiple event listeners
btn.addEventListener('click', function() {
    console.log('First listener');
});
btn.addEventListener('click', function() {
    console.log('Second listener');
});

// Remove event listener (requires named function)
btn.removeEventListener('click', handleClick);
```

### Event Object

```javascript
const btn = document.getElementById('btn');

btn.addEventListener('click', function(event) {
    console.log(event);              // Event object
    console.log(event.type);         // 'click'
    console.log(event.target);       // Element that triggered event
    console.log(event.currentTarget); // Element with the listener
    console.log(event.timeStamp);    // Time when event occurred

    // Mouse position
    console.log(event.clientX);      // X coordinate relative to viewport
    console.log(event.clientY);      // Y coordinate relative to viewport
    console.log(event.pageX);        // X coordinate relative to page
    console.log(event.pageY);        // Y coordinate relative to page

    // Keyboard events
    console.log(event.key);          // Key pressed
    console.log(event.code);         // Physical key code
    console.log(event.ctrlKey);      // Ctrl key pressed?
    console.log(event.shiftKey);     // Shift key pressed?
    console.log(event.altKey);       // Alt key pressed?
});
```

### Event Propagation

```javascript
// HTML: <div id="outer">
//         <div id="inner">
//           <button id="btn">Click</button>
//         </div>
//       </div>

const outer = document.getElementById('outer');
const inner = document.getElementById('inner');
const btn = document.getElementById('btn');

// Event bubbling (default) - from target to root
btn.addEventListener('click', () => console.log('Button'));
inner.addEventListener('click', () => console.log('Inner'));
outer.addEventListener('click', () => console.log('Outer'));
// Click button: "Button" -> "Inner" -> "Outer"

// Stop propagation
btn.addEventListener('click', (event) => {
    console.log('Button');
    event.stopPropagation(); // Stops bubbling
});
// Click button: Only "Button"

// Event capturing - from root to target
outer.addEventListener('click', () => console.log('Outer'), true);
inner.addEventListener('click', () => console.log('Inner'), true);
btn.addEventListener('click', () => console.log('Button'), true);
// Click button: "Outer" -> "Inner" -> "Button"

// Stop immediate propagation (stops all other listeners)
btn.addEventListener('click', (event) => {
    console.log('First listener');
    event.stopImmediatePropagation();
});
btn.addEventListener('click', () => {
    console.log('Second listener'); // Won't execute
});
```

### preventDefault

```javascript
// Prevent default link behavior
const link = document.querySelector('a');
link.addEventListener('click', (event) => {
    event.preventDefault();
    console.log('Link clicked but default prevented');
});

// Prevent form submission
const form = document.querySelector('form');
form.addEventListener('submit', (event) => {
    event.preventDefault();
    console.log('Form submitted but default prevented');
    // Handle form data with JavaScript
});

// Prevent right-click context menu
document.addEventListener('contextmenu', (event) => {
    event.preventDefault();
});
```

### Event Delegation

```javascript
// HTML: <ul id="list">
//         <li>Item 1</li>
//         <li>Item 2</li>
//         <li>Item 3</li>
//       </ul>

// Instead of adding listener to each li
// Add one listener to parent
const list = document.getElementById('list');

list.addEventListener('click', (event) => {
    if (event.target.tagName === 'LI') {
        console.log('Clicked:', event.target.textContent);
    }
});

// Works for dynamically added elements
const newItem = document.createElement('li');
newItem.textContent = 'Item 4';
list.appendChild(newItem);
// Click works automatically!

// More complex delegation
document.addEventListener('click', (event) => {
    // Check if clicked element or ancestor matches selector
    if (event.target.matches('.delete-btn') ||
        event.target.closest('.delete-btn')) {
        console.log('Delete button clicked');
    }
});
```

### Common Event Patterns

**Mouse Events:**
```javascript
const box = document.getElementById('box');

box.addEventListener('mouseenter', () => {
    box.style.backgroundColor = 'lightblue';
});

box.addEventListener('mouseleave', () => {
    box.style.backgroundColor = '';
});

box.addEventListener('mousemove', (event) => {
    box.textContent = `X: ${event.offsetX}, Y: ${event.offsetY}`;
});
```

**Keyboard Events:**
```javascript
const input = document.querySelector('input');

input.addEventListener('keydown', (event) => {
    console.log('Key pressed:', event.key);

    if (event.key === 'Enter') {
        console.log('Enter key pressed');
    }

    if (event.ctrlKey && event.key === 's') {
        event.preventDefault();
        console.log('Ctrl+S pressed');
    }
});

input.addEventListener('input', (event) => {
    console.log('Input value:', event.target.value);
});
```

**Form Events:**
```javascript
const form = document.querySelector('form');
const input = document.querySelector('input');

input.addEventListener('focus', () => {
    console.log('Input focused');
});

input.addEventListener('blur', () => {
    console.log('Input lost focus');
});

input.addEventListener('change', (event) => {
    console.log('Input changed:', event.target.value);
});

form.addEventListener('submit', (event) => {
    event.preventDefault();
    const formData = new FormData(form);
    console.log('Form data:', Object.fromEntries(formData));
});
```

**Document Events:**
```javascript
// DOM content loaded (HTML parsed, scripts executed)
document.addEventListener('DOMContentLoaded', () => {
    console.log('DOM ready');
});

// Window loaded (everything including images)
window.addEventListener('load', () => {
    console.log('Page fully loaded');
});

// Scroll event
window.addEventListener('scroll', () => {
    console.log('Scroll position:', window.scrollY);
});

// Resize event
window.addEventListener('resize', () => {
    console.log('Window size:', window.innerWidth, window.innerHeight);
});

// Before page unload
window.addEventListener('beforeunload', (event) => {
    event.preventDefault();
    event.returnValue = ''; // Shows confirmation dialog
});
```

---

## Best Practices

### Performance

1. **Cache DOM queries** - Don't query the same element repeatedly
```javascript
// Bad
document.getElementById('box').style.color = 'red';
document.getElementById('box').style.fontSize = '20px';
document.getElementById('box').textContent = 'Hello';

// Good
const box = document.getElementById('box');
box.style.color = 'red';
box.style.fontSize = '20px';
box.textContent = 'Hello';
```

2. **Use event delegation** for dynamic elements
```javascript
// Bad - listener for each item
items.forEach(item => {
    item.addEventListener('click', handleClick);
});

// Good - single listener on parent
parent.addEventListener('click', (event) => {
    if (event.target.matches('.item')) {
        handleClick(event);
    }
});
```

3. **Minimize DOM manipulation** - batch updates
```javascript
// Bad - multiple reflows
container.appendChild(element1);
container.appendChild(element2);
container.appendChild(element3);

// Good - single reflow
const fragment = document.createDocumentFragment();
fragment.appendChild(element1);
fragment.appendChild(element2);
fragment.appendChild(element3);
container.appendChild(fragment);
```

### Security

1. **Avoid innerHTML with user input**
```javascript
// Bad - XSS vulnerability
container.innerHTML = userInput;

// Good - use textContent or createElement
container.textContent = userInput;
// OR
const element = document.createElement('div');
element.textContent = userInput;
container.appendChild(element);
```

2. **Sanitize HTML** if you must use innerHTML
```javascript
// Use a library like DOMPurify
const clean = DOMPurify.sanitize(userInput);
container.innerHTML = clean;
```

### Code Organization

1. **Separate concerns** - keep HTML, CSS, and JS separate
2. **Use semantic selectors** - classes over IDs for styling
3. **Name event handlers** descriptively
4. **Clean up event listeners** when removing elements

```javascript
// Good
const handleButtonClick = (event) => {
    // Handle click
};

btn.addEventListener('click', handleButtonClick);

// When removing
btn.removeEventListener('click', handleButtonClick);
btn.remove();
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| DOM | Tree structure representing HTML document |
| Selection | `getElementById`, `querySelector`, `querySelectorAll` |
| Modification | `textContent`, `innerHTML`, `style`, `classList` |
| Traversal | Parent, children, siblings navigation |
| Events | `addEventListener`, event object, propagation, delegation |

## Next Topic

Continue to [Objects and JSON](./04-objects-and-json.md) to learn about JavaScript objects, properties, methods, and working with JSON data.
