# Exercise 07: Objects and Destructuring

| Property | Value |
|----------|-------|
| **Topic** | 06-javascript |
| **Type** | Exercise |
| **Difficulty** | Intermediate |

## Objective
Master JavaScript objects: creation, access patterns, methods, and destructuring syntax.

---

## Part 1: Object Basics

### Problem 1.1: Create a Book Object
Create an object representing a book with properties and a method.

```javascript
// Create a book object with:
// - title (string)
// - author (string)
// - pages (number)
// - isRead (boolean)
// - getSummary() method that returns: "Title by Author, Pages pages"

const book = // your code here

console.log(book.title);        // Expected: your book title
console.log(book.getSummary()); // Expected: "The Hobbit by J.R.R. Tolkien, 310 pages"
```

### Problem 1.2: Dynamic Property Access
Write a function that safely accesses nested object properties.

```javascript
const user = {
    name: 'Alice',
    address: {
        city: 'Seattle',
        zip: '98101'
    }
};

// Write a function that takes an object and a path string
// Returns the value or undefined if path doesn't exist
function getProperty(obj, path) {
    // your code here
    // Hint: split the path by '.' and traverse
}

console.log(getProperty(user, 'name'));           // 'Alice'
console.log(getProperty(user, 'address.city'));   // 'Seattle'
console.log(getProperty(user, 'address.country')); // undefined
console.log(getProperty(user, 'phone.number'));   // undefined
```

---

## Part 2: Object Methods and `this`

### Problem 2.1: Counter Object
Create a counter object with methods that modify internal state.

```javascript
// Create a counter object with:
// - count property (starts at 0)
// - increment() - adds 1 to count
// - decrement() - subtracts 1 from count
// - reset() - sets count back to 0
// - getCount() - returns current count

const counter = // your code here

counter.increment();
counter.increment();
console.log(counter.getCount()); // 2
counter.decrement();
console.log(counter.getCount()); // 1
counter.reset();
console.log(counter.getCount()); // 0
```

### Problem 2.2: Calculator Object
Build a calculator with chaining support.

```javascript
// Create a calculator that supports method chaining
// Methods: add(n), subtract(n), multiply(n), divide(n), getResult(), reset()

const calc = // your code here

const result = calc.add(10).subtract(2).multiply(3).divide(4).getResult();
console.log(result); // 6  -> ((10 - 2) * 3) / 4 = 6
```

---

## Part 3: Destructuring

### Problem 3.1: Basic Destructuring
Extract values from objects and arrays.

```javascript
const product = {
    id: 101,
    name: 'Laptop',
    price: 999,
    specs: {
        ram: '16GB',
        storage: '512GB SSD'
    }
};

// Extract name, price, and ram using destructuring (one line each)
// const name = ...
// const price = ...
// const ram = ... (from nested specs)

console.log(name);  // 'Laptop'
console.log(price); // 999
console.log(ram);   // '16GB'
```

### Problem 3.2: Destructuring with Defaults and Renaming
Handle missing properties and rename during destructuring.

```javascript
const config = {
    theme: 'dark',
    fontSize: 14
};

// Destructure with:
// - theme renamed to colorTheme
// - fontSize as is
// - language with default value 'en'
// - debugMode with default value false

// your destructuring code here

console.log(colorTheme); // 'dark'
console.log(fontSize);   // 14
console.log(language);   // 'en'
console.log(debugMode);  // false
```

### Problem 3.3: Function Parameter Destructuring
Write a function that uses destructuring in parameters.

```javascript
const users = [
    { id: 1, name: 'Alice', email: 'alice@example.com', role: 'admin' },
    { id: 2, name: 'Bob', email: 'bob@example.com' },
    { id: 3, name: 'Charlie', email: 'charlie@example.com', role: 'editor' }
];

// Write a function that takes a user object and returns a formatted string
// Use destructuring in the function parameter
// Default role should be 'user' if not provided
function formatUser(/* destructure here */) {
    // return "Name (role): email"
}

users.forEach(user => console.log(formatUser(user)));
// Alice (admin): alice@example.com
// Bob (user): bob@example.com
// Charlie (editor): charlie@example.com
```

---

## Part 4: Object Operations

### Problem 4.1: Merge Objects
Combine multiple objects using spread operator.

```javascript
const defaults = { theme: 'light', notifications: true, language: 'en' };
const userPrefs = { theme: 'dark', fontSize: 16 };

// Merge objects so userPrefs overrides defaults
// Add a timestamp property with current date
const finalSettings = // your code here

console.log(finalSettings);
// { theme: 'dark', notifications: true, language: 'en', fontSize: 16, timestamp: ... }
```

### Problem 4.2: Object Transformation
Transform an array of objects into a different structure.

```javascript
const students = [
    { id: 1, name: 'Alice', scores: [85, 90, 78] },
    { id: 2, name: 'Bob', scores: [70, 75, 80] },
    { id: 3, name: 'Charlie', scores: [90, 95, 88] }
];

// Transform into an object where keys are student names
// and values are their average scores
// Result: { Alice: 84.33, Bob: 75, Charlie: 91 }

function transformStudents(students) {
    // your code here
}

console.log(transformStudents(students));
```

---

## Skills Tested
- Object creation and property access
- Methods and the `this` keyword
- Object destructuring (basic, nested, defaults, renaming)
- Array destructuring
- Spread operator with objects
- Object transformation patterns

---

## Submission

### Required Files
| File | Description |
|------|-------------|
| `objects.js` | All challenge solutions |

### Folder Structure
```
your-repo/
└── 06-javascript/
    └── objects.js
```

### Evaluation Criteria
| Criteria | Points |
|----------|--------|
| Object creation & methods work | 20 |
| `this` keyword used correctly | 15 |
| Destructuring syntax correct | 25 |
| Object spread/merge works | 15 |
| Transformation logic correct | 15 |
| Code quality & ES6+ practices | 10 |
| **Total** | **100** |
