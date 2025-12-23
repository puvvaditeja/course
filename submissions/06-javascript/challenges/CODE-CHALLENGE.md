# Code Challenges: JavaScript

Practice JavaScript with DOM manipulation, objects, and core concepts. Each challenge builds practical skills.

---

## DOM Manipulation Challenges

### Challenge 1: Interactive Counter
**Files:** `counter.html`, `counter.js`

Build a counter with increment, decrement, and reset buttons:

```
┌─────────────────────────────┐
│           0                 │
│   [-]    [Reset]    [+]     │
│                             │
│   Step: [1] [5] [10]        │
└─────────────────────────────┘
```

**Requirements:**
- Display current count (starts at 0)
- Increment/decrement buttons
- Reset button returns to 0
- Step selector changes increment amount
- Count cannot go below 0
- Change color: green for positive, red for negative, black for zero

---

### Challenge 2: Dynamic Form Validator
**Files:** `form-validator.html`, `form-validator.js`

Create a registration form with real-time validation:

**Fields:**
- Username (3-15 chars, alphanumeric only)
- Email (valid email format)
- Password (8+ chars, 1 uppercase, 1 number, 1 special char)
- Confirm Password (must match)

**Requirements:**
- Validate on blur (when user leaves field)
- Show error messages below each field
- Show green checkmark for valid fields
- Disable submit button until all fields valid
- No form submission if invalid

---

### Challenge 3: Todo List with Categories
**Files:** `todo.html`, `todo.js`

Build a categorized todo list:

```
┌─────────────────────────────────────┐
│ Add Task: [____________] [Add]      │
│ Category: [Work ▼]                  │
├─────────────────────────────────────┤
│ Filter: [All] [Work] [Personal]     │
├─────────────────────────────────────┤
│ □ Complete project report    [Work] │
│ ☑ Buy groceries          [Personal] │
│ □ Review code                [Work] │
└─────────────────────────────────────┘
```

**Requirements:**
- Add tasks with category selection
- Toggle complete/incomplete (checkbox)
- Delete task (X button on hover)
- Filter by category
- Show task count per category
- Store in localStorage

---

### Challenge 4: Tab Component
**Files:** `tabs.html`, `tabs.js`

Create a reusable tab component:

```
┌──────┬──────┬──────┐
│ Tab1 │ Tab2 │ Tab3 │
├──────┴──────┴──────┤
│                    │
│   Content for      │
│   selected tab     │
│                    │
└────────────────────┘
```

**Requirements:**
- Click tab to show its content
- Active tab has different style
- Only one tab active at a time
- Keyboard navigation (arrow keys)
- Create tabs dynamically from data array

```javascript
const tabsData = [
    { title: 'Overview', content: 'Overview content here...' },
    { title: 'Features', content: 'Features content here...' },
    { title: 'Pricing', content: 'Pricing content here...' }
];
```

---

### Challenge 5: Modal Component
**Files:** `modal.html`, `modal.js`

Build a reusable modal/popup system:

**Requirements:**
- Open modal with button click
- Close with X button, overlay click, or Escape key
- Prevent body scroll when modal open
- Support multiple modals on same page
- Animate open/close (fade + scale)
- Create modal from JavaScript:

```javascript
const modal = createModal({
    title: 'Confirm Delete',
    content: 'Are you sure you want to delete this item?',
    buttons: [
        { text: 'Cancel', type: 'secondary', onClick: closeModal },
        { text: 'Delete', type: 'danger', onClick: handleDelete }
    ]
});
modal.open();
```

---

### Challenge 6: Accordion Component
**Files:** `accordion.html`, `accordion.js`

Create an expandable accordion:

```
┌────────────────────────────────┐
│ ▼ Section 1                    │
├────────────────────────────────┤
│   Expanded content for         │
│   section 1 is visible         │
├────────────────────────────────┤
│ ▶ Section 2                    │
├────────────────────────────────┤
│ ▶ Section 3                    │
└────────────────────────────────┘
```

**Requirements:**
- Click header to expand/collapse
- Smooth height animation
- Option: allow multiple open OR only one at a time
- Arrow icon rotates on expand
- Build from data array:

```javascript
const accordionData = [
    { title: 'What is JavaScript?', content: '...' },
    { title: 'What is the DOM?', content: '...' },
    { title: 'What are events?', content: '...' }
];
```

---

### Challenge 7: Image Gallery
**Files:** `gallery.html`, `gallery.js`

Build an image gallery with lightbox:

**Requirements:**
- Display thumbnail grid
- Click thumbnail to open lightbox (full view)
- Previous/Next navigation in lightbox
- Keyboard navigation (arrows, Escape to close)
- Image counter (3 of 10)
- Lazy load thumbnails

```javascript
const images = [
    { thumb: 'thumb1.jpg', full: 'full1.jpg', caption: 'Sunset' },
    { thumb: 'thumb2.jpg', full: 'full2.jpg', caption: 'Mountains' },
    // ...
];
```

---

## JavaScript Objects Challenges

### Challenge 8: Shopping Cart Object
**File:** `shopping-cart.js`

Create a shopping cart with object methods:

```javascript
const cart = createShoppingCart();

cart.addItem({ id: 1, name: 'Laptop', price: 999, quantity: 1 });
cart.addItem({ id: 2, name: 'Mouse', price: 29, quantity: 2 });
cart.addItem({ id: 1, name: 'Laptop', price: 999, quantity: 1 }); // Should increase quantity

console.log(cart.getItems());
// [{ id: 1, name: 'Laptop', price: 999, quantity: 2 }, { id: 2, name: 'Mouse', price: 29, quantity: 2 }]

cart.updateQuantity(1, 3);  // Set laptop quantity to 3
cart.removeItem(2);         // Remove mouse

console.log(cart.getTotal());        // 2997
console.log(cart.getItemCount());    // 3
console.log(cart.isEmpty());         // false

cart.applyDiscount('SAVE10', 10);    // 10% discount
console.log(cart.getTotal());        // 2697.30

cart.clear();
console.log(cart.isEmpty());         // true
```

---

### Challenge 9: Library Management System
**File:** `library.js`

Build a library system using objects and classes:

```javascript
const library = createLibrary();

// Add books
library.addBook({ isbn: '123', title: '1984', author: 'Orwell', copies: 3 });
library.addBook({ isbn: '456', title: 'Dune', author: 'Herbert', copies: 2 });

// Add members
library.addMember({ id: 'M1', name: 'John', email: 'john@example.com' });
library.addMember({ id: 'M2', name: 'Jane', email: 'jane@example.com' });

// Borrow books
library.borrowBook('M1', '123');  // John borrows 1984
library.borrowBook('M2', '123');  // Jane borrows 1984

console.log(library.getAvailableCopies('123'));  // 1

library.returnBook('M1', '123');  // John returns 1984

console.log(library.getMemberHistory('M1'));
// [{ isbn: '123', title: '1984', borrowedAt: Date, returnedAt: Date }]

console.log(library.getOverdueBooks());  // Books not returned within 14 days
console.log(library.searchBooks('orwell'));  // Search by title or author
```

---

### Challenge 10: Event Emitter
**File:** `event-emitter.js`

Implement a pub/sub event system:

```javascript
const emitter = createEventEmitter();

// Subscribe to events
const unsubscribe = emitter.on('userLogin', (user) => {
    console.log(`${user.name} logged in`);
});

emitter.on('userLogin', (user) => {
    console.log(`Send welcome email to ${user.email}`);
});

// One-time listener
emitter.once('appStart', () => {
    console.log('App started - this only runs once');
});

// Emit events
emitter.emit('userLogin', { name: 'John', email: 'john@example.com' });
// Output:
// "John logged in"
// "Send welcome email to john@example.com"

emitter.emit('appStart');  // "App started - this only runs once"
emitter.emit('appStart');  // Nothing happens

unsubscribe();  // Remove first listener
emitter.emit('userLogin', { name: 'Jane', email: 'jane@example.com' });
// Only outputs: "Send welcome email to jane@example.com"

emitter.off('userLogin');  // Remove all userLogin listeners
```

---

## Core JavaScript Challenges

### Challenge 11: Deep Clone
**File:** `deep-clone.js`

Create a function to deep clone objects and arrays:

```javascript
const original = {
    name: 'John',
    address: { city: 'New York', zip: '10001' },
    hobbies: ['reading', 'gaming'],
    metadata: { created: new Date(), tags: new Set(['user', 'admin']) }
};

function deepClone(obj) {
    // Handle: objects, arrays, Date, Set, Map, null, primitives
}

const cloned = deepClone(original);
cloned.address.city = 'Boston';
cloned.hobbies.push('swimming');

console.log(original.address.city);  // Still 'New York'
console.log(original.hobbies);       // Still ['reading', 'gaming']
```

---

### Challenge 12: Type Checker
**File:** `type-checker.js`

Create a comprehensive type checking utility:

```javascript
const typeOf = (value) => {
    // Return accurate type for any value
};

console.log(typeOf(null));           // "null"
console.log(typeOf(undefined));      // "undefined"
console.log(typeOf(42));             // "number"
console.log(typeOf(NaN));            // "nan"
console.log(typeOf('hello'));        // "string"
console.log(typeOf(true));           // "boolean"
console.log(typeOf(Symbol()));       // "symbol"
console.log(typeOf([]));             // "array"
console.log(typeOf({}));             // "object"
console.log(typeOf(() => {}));       // "function"
console.log(typeOf(new Date()));     // "date"
console.log(typeOf(new Map()));      // "map"
console.log(typeOf(new Set()));      // "set"
console.log(typeOf(/regex/));        // "regexp"
console.log(typeOf(new Error()));    // "error"
console.log(typeOf(Promise.resolve())); // "promise"
```

---

### Challenge 13: Validate Password
**File:** `validate-password.js`

Create a password validator with detailed feedback:

```javascript
function validatePassword(password) {
    // Return: { isValid: boolean, score: number, errors: [], suggestions: [] }
    //
    // Rules:
    // - Minimum 8 characters
    // - At least one uppercase letter
    // - At least one lowercase letter
    // - At least one number
    // - At least one special character (!@#$%^&*()_+-=)
    // - No common passwords (password, 123456, qwerty, etc.)
    //
    // Score: 0-100 based on strength
}

console.log(validatePassword('abc'));
// { isValid: false, score: 15, errors: ['Too short', ...], suggestions: ['Add numbers', ...] }

console.log(validatePassword('MyP@ssw0rd!2024'));
// { isValid: true, score: 95, errors: [], suggestions: [] }
```

---

### Challenge 14: Function Composition
**File:** `compose.js`

Implement compose and pipe functions:

```javascript
// compose: right to left
const compose = (...fns) => { /* implementation */ };

// pipe: left to right
const pipe = (...fns) => { /* implementation */ };

const addOne = x => x + 1;
const double = x => x * 2;
const square = x => x * x;

const composed = compose(addOne, double, square);
console.log(composed(3)); // addOne(double(square(3))) = addOne(double(9)) = addOne(18) = 19

const piped = pipe(square, double, addOne);
console.log(piped(3)); // addOne(double(square(3))) = 19
```

---

### Challenge 15: Memoization
**File:** `memoize.js`

Create a memoize function with cache management:

```javascript
const memoize = (fn, options = {}) => {
    // Options: maxSize, ttl (time-to-live in ms)
};

const expensiveFn = (n) => {
    console.log('Computing...');
    return n * n;
};

const memoized = memoize(expensiveFn, { maxSize: 100, ttl: 60000 });

console.log(memoized(5)); // "Computing..." then 25
console.log(memoized(5)); // 25 (cached, no log)
console.log(memoized(6)); // "Computing..." then 36

// After 60 seconds, cache expires
// console.log(memoized(5)); // "Computing..." then 25
```

---

### Challenge 16: Debounce & Throttle
**File:** `debounce-throttle.js`

Implement both debounce and throttle:

```javascript
// Debounce: execute after delay of inactivity
const debounce = (fn, delay) => { /* implementation */ };

// Throttle: execute at most once per interval
const throttle = (fn, interval) => { /* implementation */ };

// Usage:
const handleSearch = debounce((query) => {
    console.log('Searching:', query);
}, 300);

const handleScroll = throttle(() => {
    console.log('Scroll position:', window.scrollY);
}, 100);
```

---

### Challenge 17: Array Methods Implementation
**File:** `array-methods.js`

Implement array methods without using built-ins:

```javascript
// Implement these without using the native methods
function myMap(arr, callback) { }
function myFilter(arr, callback) { }
function myReduce(arr, callback, initialValue) { }
function myFind(arr, callback) { }
function myEvery(arr, callback) { }
function mySome(arr, callback) { }
function myFlat(arr, depth = 1) { }

// Tests
console.log(myMap([1, 2, 3], x => x * 2));           // [2, 4, 6]
console.log(myFilter([1, 2, 3, 4], x => x > 2));     // [3, 4]
console.log(myReduce([1, 2, 3], (a, b) => a + b, 0)); // 6
console.log(myFind([1, 2, 3], x => x > 1));          // 2
console.log(myFlat([1, [2, [3, [4]]]], 2));          // [1, 2, 3, [4]]
```

---

### Challenge 18: Data Transformer
**File:** `data-transformer.js`

Build a chainable data transformation utility:

```javascript
const transform = (data) => ({
    filter: (fn) => transform(data.filter(fn)),
    map: (fn) => transform(data.map(fn)),
    sort: (fn) => transform([...data].sort(fn)),
    take: (n) => transform(data.slice(0, n)),
    groupBy: (key) => { /* return grouped object */ },
    value: () => data
});

const users = [
    { name: 'John', age: 30, dept: 'Engineering' },
    { name: 'Jane', age: 25, dept: 'Marketing' },
    { name: 'Bob', age: 35, dept: 'Engineering' },
    { name: 'Alice', age: 28, dept: 'Marketing' }
];

const result = transform(users)
    .filter(u => u.age > 25)
    .sort((a, b) => a.age - b.age)
    .map(u => u.name)
    .take(2)
    .value();

console.log(result); // ['Jane', 'John'] -- wait, Jane is 25, filtered out
// Actually: ['John', 'Bob'] -- ages 30, 35 sorted = John(30), Bob(35), take 2
```

---

### Challenge 19: Observable Pattern
**File:** `observable.js`

Create a simple observable/reactive value:

```javascript
function createObservable(initialValue) {
    // Return an observable that notifies subscribers on change
}

const count = createObservable(0);

// Subscribe to changes
count.subscribe((newValue, oldValue) => {
    console.log(`Count changed from ${oldValue} to ${newValue}`);
});

count.set(1);  // "Count changed from 0 to 1"
count.set(5);  // "Count changed from 1 to 5"
console.log(count.get());  // 5

// Computed observable
const doubled = count.computed(val => val * 2);
doubled.subscribe(val => console.log('Doubled:', val));

count.set(10);
// "Count changed from 5 to 10"
// "Doubled: 20"
```

---

### Challenge 20: State Machine
**File:** `state-machine.js`

Implement a finite state machine:

```javascript
const trafficLight = createStateMachine({
    initial: 'red',
    states: {
        red: { on: { TIMER: 'green' } },
        green: { on: { TIMER: 'yellow' } },
        yellow: { on: { TIMER: 'red' } }
    }
});

console.log(trafficLight.state);  // 'red'
trafficLight.send('TIMER');
console.log(trafficLight.state);  // 'green'
trafficLight.send('TIMER');
console.log(trafficLight.state);  // 'yellow'

// With actions
const door = createStateMachine({
    initial: 'closed',
    states: {
        closed: {
            on: { OPEN: 'open' },
            onEnter: () => console.log('Door closed')
        },
        open: {
            on: { CLOSE: 'closed' },
            onEnter: () => console.log('Door opened')
        }
    }
});

door.send('OPEN');   // "Door opened"
door.send('CLOSE');  // "Door closed"
door.send('OPEN');   // "Door opened"
```

---

## Submission

Place all challenge files in your repo: `06-javascript/challenges/`

### DOM Challenges
| # | Challenge | Files | Skills |
|---|-----------|-------|--------|
| 1 | Interactive Counter | `counter.html`, `counter.js` | Events, DOM updates |
| 2 | Form Validator | `form-validator.html`, `form-validator.js` | Validation, events |
| 3 | Todo List | `todo.html`, `todo.js` | CRUD, localStorage |
| 4 | Tab Component | `tabs.html`, `tabs.js` | Components, keyboard |
| 5 | Modal Component | `modal.html`, `modal.js` | Overlay, animation |
| 6 | Accordion | `accordion.html`, `accordion.js` | Toggle, animation |
| 7 | Image Gallery | `gallery.html`, `gallery.js` | Lightbox, navigation |

### Object Challenges
| # | Challenge | File | Skills |
|---|-----------|------|--------|
| 8 | Shopping Cart | `shopping-cart.js` | Object methods, state |
| 9 | Library System | `library.js` | OOP, relationships |
| 10 | Event Emitter | `event-emitter.js` | Pub/sub pattern |

### Core JavaScript
| # | Challenge | File | Skills |
|---|-----------|------|--------|
| 11 | Deep Clone | `deep-clone.js` | Recursion, types |
| 12 | Type Checker | `type-checker.js` | Type detection |
| 13 | Password Validator | `validate-password.js` | Validation, regex |
| 14 | Compose/Pipe | `compose.js` | Higher-order functions |
| 15 | Memoization | `memoize.js` | Caching, closures |
| 16 | Debounce/Throttle | `debounce-throttle.js` | Timing, closures |
| 17 | Array Methods | `array-methods.js` | Implementation |
| 18 | Data Transformer | `data-transformer.js` | Chaining, fluent API |
| 19 | Observable | `observable.js` | Reactive pattern |
| 20 | State Machine | `state-machine.js` | FSM pattern |

---

*Updated: December 2025*
