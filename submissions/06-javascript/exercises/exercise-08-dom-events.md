# Exercise 08: DOM Manipulation and Events

| Property | Value |
|----------|-------|
| **Topic** | 06-javascript |
| **Type** | Exercise |
| **Difficulty** | Intermediate |

## Objective
Learn to select, create, modify DOM elements and handle user events effectively.

---

## Part 1: DOM Selection

### Problem 1.1: Element Selection
Given this HTML structure, write JavaScript to select elements.

```html
<div id="app">
    <header class="header">
        <h1>My App</h1>
        <nav>
            <a href="#home" class="nav-link active">Home</a>
            <a href="#about" class="nav-link">About</a>
            <a href="#contact" class="nav-link">Contact</a>
        </nav>
    </header>
    <main>
        <article class="card" data-id="1">
            <h2>Card Title</h2>
            <p>Card content here</p>
        </article>
        <article class="card" data-id="2">
            <h2>Another Card</h2>
            <p>More content</p>
        </article>
    </main>
</div>
```

```javascript
// Select the following elements:

// 1. The element with id "app"
const app =

// 2. The first h1 element
const heading =

// 3. All elements with class "nav-link" (returns NodeList)
const navLinks =

// 4. The nav-link with class "active"
const activeLink =

// 5. All article elements with class "card"
const cards =

// 6. The card with data-id="2"
const secondCard =

// 7. All paragraph elements inside .card elements
const cardParagraphs =
```

---

## Part 2: DOM Manipulation

### Problem 2.1: Create and Append Elements
Build a product card dynamically.

```javascript
// Create this structure dynamically:
// <div class="product-card">
//     <img src="product.jpg" alt="Product Name">
//     <h3>Product Name</h3>
//     <p class="price">$29.99</p>
//     <button class="add-to-cart">Add to Cart</button>
// </div>

function createProductCard(product) {
    // product = { name: 'Laptop', price: 999.99, image: 'laptop.jpg' }
    // Return the created div element
}

const product = { name: 'Laptop', price: 999.99, image: 'laptop.jpg' };
const card = createProductCard(product);
document.body.appendChild(card);
```

### Problem 2.2: Modify Existing Elements
Write functions to manipulate element properties.

```javascript
// Given: <div id="box" class="container">Hello</div>

// 1. Change the text content to "Hello, World!"
function changeText(element, newText) {
    // your code
}

// 2. Add a class "highlight" without removing existing classes
function addClass(element, className) {
    // your code
}

// 3. Toggle a class on/off
function toggleClass(element, className) {
    // your code
}

// 4. Set multiple styles at once
function setStyles(element, styles) {
    // styles = { backgroundColor: 'blue', color: 'white', padding: '10px' }
    // your code
}

// 5. Set a data attribute
function setDataAttribute(element, key, value) {
    // your code
}
```

### Problem 2.3: Remove and Replace
Manipulate the DOM tree.

```javascript
// Given a list:
// <ul id="list">
//     <li>Item 1</li>
//     <li>Item 2</li>
//     <li>Item 3</li>
// </ul>

// 1. Remove the second item
function removeSecondItem() {
    // your code
}

// 2. Replace the first item with a new one that says "New Item 1"
function replaceFirstItem() {
    // your code
}

// 3. Insert a new item before the last item
function insertBeforeLast(text) {
    // your code
}

// 4. Clear all items from the list
function clearList() {
    // your code - find the most efficient way
}
```

---

## Part 3: Event Handling

### Problem 3.1: Basic Events
Attach event handlers to elements.

```javascript
// Given: <button id="btn">Click Me</button>

const btn = document.getElementById('btn');

// 1. Log "Button clicked!" when button is clicked
btn.addEventListener('click', () => {
    // your code
});

// 2. Change button text to "Clicked!" after click
// (modify the handler above or add another)

// 3. Add a mouseover event that changes background color
// 4. Add a mouseout event that resets background color
```

### Problem 3.2: Event Object
Use the event object to get information.

```javascript
// Create a form handler that:
// 1. Prevents default form submission
// 2. Gets the input value
// 3. Logs the value
// 4. Clears the input after submission

// <form id="search-form">
//     <input type="text" id="search-input" placeholder="Search...">
//     <button type="submit">Search</button>
// </form>

const form = document.getElementById('search-form');

form.addEventListener('submit', (event) => {
    // your code here
});
```

### Problem 3.3: Event Delegation
Handle events on dynamic elements efficiently.

```javascript
// Given a list where items can be added/removed dynamically:
// <ul id="todo-list">
//     <li><span>Task 1</span> <button class="delete">X</button></li>
//     <li><span>Task 2</span> <button class="delete">X</button></li>
// </ul>

// Use event delegation to handle delete button clicks
// (attach ONE event listener to the parent ul, not individual buttons)

const todoList = document.getElementById('todo-list');

todoList.addEventListener('click', (event) => {
    // Check if a delete button was clicked
    // If so, remove the parent li element
    // your code here
});
```

### Problem 3.4: Keyboard Events
Handle keyboard input.

```javascript
// Create a function that:
// 1. Listens for keydown events on the document
// 2. Logs the key pressed
// 3. If "Escape" is pressed, close any open modal (remove .show class from #modal)
// 4. If "Enter" is pressed while in an input, trigger form submit

document.addEventListener('keydown', (event) => {
    // your code here
});
```

---

## Part 4: Practical Exercises

### Problem 4.1: Interactive Counter
Build a counter with increment/decrement buttons.

```html
<!-- Create this HTML structure first -->
<div id="counter">
    <button id="decrement">-</button>
    <span id="count">0</span>
    <button id="increment">+</button>
</div>
```

```javascript
// Implement the counter logic:
// - Click + to increment
// - Click - to decrement
// - Count cannot go below 0
// - When count is 0, decrement button should be disabled
```

### Problem 4.2: Toggle Visibility
Create a show/hide functionality.

```javascript
// Given:
// <button id="toggle-btn">Show Details</button>
// <div id="details" class="hidden">
//     <p>These are the hidden details.</p>
// </div>

// CSS: .hidden { display: none; }

// Implement:
// 1. Click button to toggle visibility of details div
// 2. Button text changes between "Show Details" and "Hide Details"
```

### Problem 4.3: Form Validation Display
Show validation messages dynamically.

```javascript
// Given:
// <form id="signup-form">
//     <input type="text" id="username" placeholder="Username">
//     <span class="error" id="username-error"></span>
//     <input type="email" id="email" placeholder="Email">
//     <span class="error" id="email-error"></span>
//     <button type="submit">Sign Up</button>
// </form>

// Implement:
// 1. On form submit, validate that username is at least 3 characters
// 2. Validate that email contains "@"
// 3. Show error messages in the corresponding span elements
// 4. Add "invalid" class to inputs with errors
// 5. Only submit (log values) if no errors
```

---

## Skills Tested
- DOM selection methods (getElementById, querySelector, querySelectorAll)
- Creating and modifying elements
- Event listeners and the event object
- Event delegation pattern
- Keyboard events
- Form handling and validation
- Class manipulation (add, remove, toggle)

---

## Submission

### Required Files
| File | Description |
|------|-------------|
| `dom-events.html` | HTML with all elements |
| `dom-events.js` | JavaScript solutions |

### Folder Structure
```
your-repo/
└── 06-javascript/
    ├── dom-events.html
    └── dom-events.js
```

### Evaluation Criteria
| Criteria | Points |
|----------|--------|
| Element selection correct | 15 |
| DOM creation/manipulation works | 20 |
| Event handlers work correctly | 25 |
| Event delegation implemented | 15 |
| Practical exercises functional | 15 |
| Code quality & best practices | 10 |
| **Total** | **100** |
