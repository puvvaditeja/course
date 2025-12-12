# JavaScript Fundamentals

## What is JavaScript?

JavaScript is a high-level, interpreted programming language that enables dynamic and interactive content on web pages. Created by Brendan Eich in 1995, JavaScript has evolved from a simple scripting language into a powerful, multi-paradigm language that runs on both client and server sides.

### Key Characteristics

- **Dynamic Typing**: Variable types are determined at runtime
- **Interpreted**: Code is executed line by line without compilation
- **Multi-paradigm**: Supports procedural, object-oriented, and functional programming
- **Event-driven**: Responds to user interactions and system events
- **Single-threaded**: Executes one operation at a time with asynchronous capabilities
- **Cross-platform**: Runs in browsers and on servers (Node.js)

### JavaScript vs Java

Despite similar names, JavaScript and Java are fundamentally different:

| Feature | JavaScript | Java |
|---------|------------|------|
| Type System | Dynamic | Static |
| Execution | Interpreted | Compiled to bytecode |
| Platform | Browser & Server | Platform-independent (JVM) |
| Object Model | Prototype-based | Class-based |
| Typing | Weakly typed | Strongly typed |
| Use Cases | Web development, scripting | Enterprise applications, Android |

---

## Getting Started with JavaScript

### Where JavaScript Runs

1. **Browser (Client-side)**
   - Manipulates HTML/CSS
   - Handles user interactions
   - Makes API calls
   - Manages browser storage

2. **Server (Node.js)**
   - Backend APIs
   - File system operations
   - Database interactions
   - Real-time applications

### Including JavaScript in HTML

**Inline Script:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>JavaScript Demo</title>
</head>
<body>
    <h1>Hello, JavaScript!</h1>

    <script>
        console.log('This script runs inline');
        document.querySelector('h1').style.color = 'blue';
    </script>
</body>
</html>
```

**External Script:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>JavaScript Demo</title>
</head>
<body>
    <h1>Hello, JavaScript!</h1>

    <!-- Place scripts at the end of body for better performance -->
    <script src="app.js"></script>
</body>
</html>
```

**Best Practice - Defer/Async:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>JavaScript Demo</title>
    <!-- defer: Downloads in parallel, executes after HTML parsing -->
    <script src="app.js" defer></script>

    <!-- async: Downloads in parallel, executes immediately when ready -->
    <script src="analytics.js" async></script>
</head>
<body>
    <h1>Hello, JavaScript!</h1>
</body>
</html>
```

### Your First JavaScript Program

**Console Output:**
```javascript
// This is a single-line comment

/*
  This is a
  multi-line comment
*/

// Output to browser console
console.log('Hello, World!');
console.warn('This is a warning');
console.error('This is an error');

// Alert dialog (browser only)
alert('Welcome to JavaScript!');

// Prompt for user input (browser only)
let name = prompt('What is your name?');
console.log('Hello, ' + name);
```

---

## Variables

Variables are containers for storing data values. JavaScript offers three ways to declare variables: `var`, `let`, and `const`.

### Variable Declaration

**Using `var` (Old way - avoid in modern code):**
```javascript
var age = 25;
var name = 'John';
var isStudent = true;

// var has function scope and can be redeclared
var age = 30; // No error - can redeclare
```

**Using `let` (Modern - block scoped):**
```javascript
let age = 25;
let name = 'John';
let isStudent = true;

// let is block-scoped and cannot be redeclared
// let age = 30; // Error: Cannot redeclare
age = 30; // OK - can reassign
```

**Using `const` (Modern - constant reference):**
```javascript
const PI = 3.14159;
const MAX_USERS = 100;
const API_URL = 'https://api.example.com';

// const cannot be reassigned
// PI = 3.14; // Error: Assignment to constant variable

// But object properties can be modified
const person = { name: 'John' };
person.name = 'Jane'; // OK - modifying property
person.age = 25; // OK - adding property
// person = {}; // Error - cannot reassign
```

### Variable Naming Rules

**Valid Names:**
```javascript
let userName = 'John';        // camelCase (recommended)
let user_name = 'John';       // snake_case
let _private = 'secret';      // starts with underscore
let $element = document;      // starts with dollar sign
let user2 = 'Jane';           // contains numbers
```

**Invalid Names:**
```javascript
// let 2users = 10;           // cannot start with number
// let user-name = 'John';    // cannot contain hyphens
// let let = 'value';         // cannot use reserved keywords
// let user name = 'John';    // cannot contain spaces
```

### Comparison: var vs let vs const

| Feature | var | let | const |
|---------|-----|-----|-------|
| Scope | Function | Block | Block |
| Hoisting | Yes (undefined) | Yes (TDZ) | Yes (TDZ) |
| Redeclaration | Allowed | Not allowed | Not allowed |
| Reassignment | Allowed | Allowed | Not allowed |
| Best Practice | Avoid | Use for mutable | Use for immutable |

**Scope Example:**
```javascript
function scopeExample() {
    var x = 1;
    let y = 2;
    const z = 3;

    if (true) {
        var x = 10;    // Same variable (function scope)
        let y = 20;    // Different variable (block scope)
        const z = 30;  // Different variable (block scope)

        console.log(x, y, z); // 10, 20, 30
    }

    console.log(x, y, z); // 10, 2, 3
}
```

---

## Data Types

JavaScript has dynamic typing - you don't specify the type when declaring a variable.

### Primitive Data Types

**1. Number:**
```javascript
let integer = 42;
let float = 3.14;
let negative = -10;
let scientific = 2.5e6; // 2.5 * 10^6 = 2500000

// Special numeric values
let infinite = Infinity;
let notANumber = NaN;

console.log(typeof integer); // "number"
console.log(10 / 0);         // Infinity
console.log('abc' / 2);      // NaN
```

**2. String:**
```javascript
let single = 'Hello';
let double = "World";
let template = `Hello, World!`; // Template literal

// String concatenation
let fullName = 'John' + ' ' + 'Doe';

// Template literals (ES6+)
let name = 'John';
let age = 25;
let message = `My name is ${name} and I'm ${age} years old`;

// Multi-line strings
let multiLine = `This is
a multi-line
string`;

console.log(typeof message); // "string"
```

**3. Boolean:**
```javascript
let isActive = true;
let isComplete = false;

// Boolean conversion
console.log(Boolean(1));        // true
console.log(Boolean(0));        // false
console.log(Boolean('Hello'));  // true
console.log(Boolean(''));       // false

console.log(typeof isActive); // "boolean"
```

**4. Undefined:**
```javascript
let notAssigned;
console.log(notAssigned);        // undefined
console.log(typeof notAssigned); // "undefined"

// Explicitly setting undefined
let value = undefined;
```

**5. Null:**
```javascript
let emptyValue = null;
console.log(emptyValue);        // null
console.log(typeof emptyValue); // "object" (JavaScript quirk)

// null represents intentional absence of value
let user = null; // No user logged in
```

**6. Symbol (ES6):**
```javascript
let id1 = Symbol('id');
let id2 = Symbol('id');

console.log(id1 === id2);    // false - symbols are unique
console.log(typeof id1);     // "symbol"
```

**7. BigInt (ES2020):**
```javascript
let bigNumber = 9007199254740991n;
let anotherBig = BigInt(9007199254740991);

console.log(typeof bigNumber); // "bigint"
```

### Reference Data Types

**Objects:**
```javascript
let person = {
    name: 'John',
    age: 30,
    isStudent: false
};

console.log(typeof person); // "object"
```

**Arrays:**
```javascript
let numbers = [1, 2, 3, 4, 5];
let mixed = [1, 'hello', true, null, { name: 'John' }];

console.log(typeof numbers);     // "object"
console.log(Array.isArray(numbers)); // true
```

**Functions:**
```javascript
function greet() {
    return 'Hello!';
}

console.log(typeof greet); // "function"
```

### Type Checking and Conversion

**Checking Types:**
```javascript
let age = 25;
let name = 'John';
let isActive = true;
let person = { name: 'John' };
let numbers = [1, 2, 3];

console.log(typeof age);          // "number"
console.log(typeof name);         // "string"
console.log(typeof isActive);     // "boolean"
console.log(typeof person);       // "object"
console.log(typeof numbers);      // "object"
console.log(Array.isArray(numbers)); // true
```

**Type Conversion:**
```javascript
// String to Number
let str = '123';
let num1 = Number(str);      // 123
let num2 = parseInt(str);    // 123
let num3 = parseFloat('3.14'); // 3.14
let num4 = +str;             // 123 (unary plus)

// Number to String
let age = 25;
let str1 = String(age);      // "25"
let str2 = age.toString();   // "25"
let str3 = '' + age;         // "25" (concatenation)

// To Boolean
let bool1 = Boolean(1);      // true
let bool2 = Boolean(0);      // false
let bool3 = Boolean('');     // false
let bool4 = Boolean('hello'); // true
```

---

## Operators

Operators perform operations on variables and values.

### Arithmetic Operators

```javascript
let a = 10;
let b = 3;

console.log(a + b);  // 13 - Addition
console.log(a - b);  // 7  - Subtraction
console.log(a * b);  // 30 - Multiplication
console.log(a / b);  // 3.333... - Division
console.log(a % b);  // 1  - Modulus (remainder)
console.log(a ** b); // 1000 - Exponentiation (ES2016)

// Increment and Decrement
let x = 5;
console.log(++x); // 6 - Pre-increment (increment then return)
console.log(x++); // 6 - Post-increment (return then increment)
console.log(x);   // 7

let y = 5;
console.log(--y); // 4 - Pre-decrement
console.log(y--); // 4 - Post-decrement
console.log(y);   // 3
```

### Assignment Operators

```javascript
let x = 10;

x += 5;  // x = x + 5  → 15
x -= 3;  // x = x - 3  → 12
x *= 2;  // x = x * 2  → 24
x /= 4;  // x = x / 4  → 6
x %=  5; // x = x % 5  → 1
x **= 3; // x = x ** 3 → 1
```

### Comparison Operators

```javascript
let a = 5;
let b = '5';
let c = 10;

// Equality (loose - type coercion)
console.log(a == b);   // true (5 == '5')
console.log(a != c);   // true (5 != 10)

// Strict Equality (no type coercion)
console.log(a === b);  // false (5 !== '5')
console.log(a !== b);  // true

// Relational
console.log(a < c);    // true
console.log(a > c);    // false
console.log(a <= 5);   // true
console.log(c >= 10);  // true
```

**Equality Comparison Best Practices:**
```javascript
// Always prefer === and !== to avoid unexpected type coercion
console.log(0 == false);   // true (avoid)
console.log(0 === false);  // false (preferred)

console.log('' == false);  // true (avoid)
console.log('' === false); // false (preferred)

console.log(null == undefined);  // true
console.log(null === undefined); // false
```

### Logical Operators

```javascript
let a = true;
let b = false;

// AND - returns true if both operands are true
console.log(a && b);  // false
console.log(true && true); // true

// OR - returns true if at least one operand is true
console.log(a || b);  // true
console.log(false || false); // false

// NOT - inverts the boolean value
console.log(!a);      // false
console.log(!b);      // true

// Short-circuit evaluation
let x = null;
let name = x || 'Default'; // 'Default' (x is falsy)

let user = { name: 'John' };
let userName = user && user.name; // 'John'
```

### String Operators

```javascript
// Concatenation
let firstName = 'John';
let lastName = 'Doe';
let fullName = firstName + ' ' + lastName; // "John Doe"

// Template Literals (preferred)
let greeting = `Hello, ${firstName} ${lastName}!`;

// Compound assignment
let message = 'Hello';
message += ' World'; // "Hello World"
```

### Ternary Operator

```javascript
// condition ? valueIfTrue : valueIfFalse
let age = 18;
let canVote = age >= 18 ? 'Yes' : 'No'; // "Yes"

let score = 75;
let grade = score >= 90 ? 'A' :
            score >= 80 ? 'B' :
            score >= 70 ? 'C' :
            score >= 60 ? 'D' : 'F'; // "C"

// Nested ternary (avoid for readability)
let status = age >= 18
    ? (age >= 65 ? 'Senior' : 'Adult')
    : (age >= 13 ? 'Teen' : 'Child');
```

### Nullish Coalescing Operator (??) (ES2020)

```javascript
// Returns right operand when left is null or undefined
let value1 = null ?? 'default';      // 'default'
let value2 = undefined ?? 'default'; // 'default'
let value3 = 0 ?? 'default';         // 0 (not null/undefined)
let value4 = '' ?? 'default';        // '' (not null/undefined)

// Compare with OR operator
console.log(0 || 'default');    // 'default' (0 is falsy)
console.log(0 ?? 'default');    // 0 (0 is not null/undefined)
```

### Optional Chaining Operator (?.) (ES2020)

```javascript
let user = {
    name: 'John',
    address: {
        city: 'New York'
    }
};

// Without optional chaining
let zipCode = user.address && user.address.zipCode; // undefined

// With optional chaining
let city = user?.address?.city;        // 'New York'
let zipCode = user?.address?.zipCode;  // undefined
let admin = user?.admin?.name;         // undefined (no error)

// With arrays
let firstUser = users?.[0];
let adminName = users?.[0]?.name;

// With functions
let result = someFunction?.();
```

---

## Control Flow

Control flow statements determine the order in which code is executed.

### If-Else Statements

```javascript
// Simple if
let age = 18;

if (age >= 18) {
    console.log('You are an adult');
}

// If-else
let temperature = 25;

if (temperature > 30) {
    console.log('It\'s hot!');
} else {
    console.log('It\'s pleasant');
}

// If-else if-else
let score = 85;

if (score >= 90) {
    console.log('Grade: A');
} else if (score >= 80) {
    console.log('Grade: B');
} else if (score >= 70) {
    console.log('Grade: C');
} else if (score >= 60) {
    console.log('Grade: D');
} else {
    console.log('Grade: F');
}

// Nested if
let isLoggedIn = true;
let isAdmin = true;

if (isLoggedIn) {
    if (isAdmin) {
        console.log('Welcome, Admin!');
    } else {
        console.log('Welcome, User!');
    }
} else {
    console.log('Please log in');
}
```

### Switch Statement

```javascript
let day = 'Monday';

switch (day) {
    case 'Monday':
        console.log('Start of work week');
        break;
    case 'Tuesday':
    case 'Wednesday':
    case 'Thursday':
        console.log('Midweek');
        break;
    case 'Friday':
        console.log('TGIF!');
        break;
    case 'Saturday':
    case 'Sunday':
        console.log('Weekend!');
        break;
    default:
        console.log('Invalid day');
}

// Switch with numbers
let month = 2;
let monthName;

switch (month) {
    case 1:
        monthName = 'January';
        break;
    case 2:
        monthName = 'February';
        break;
    case 3:
        monthName = 'March';
        break;
    default:
        monthName = 'Unknown';
}

console.log(monthName); // 'February'
```

### For Loop

```javascript
// Standard for loop
for (let i = 0; i < 5; i++) {
    console.log(i); // 0, 1, 2, 3, 4
}

// Loop through array
let fruits = ['apple', 'banana', 'orange'];

for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i]);
}

// Nested loops
for (let i = 1; i <= 3; i++) {
    for (let j = 1; j <= 3; j++) {
        console.log(`${i}, ${j}`);
    }
}

// For loop with multiple variables
for (let i = 0, j = 10; i < 5; i++, j--) {
    console.log(`i: ${i}, j: ${j}`);
}
```

### While Loop

```javascript
// While loop
let count = 0;

while (count < 5) {
    console.log(count);
    count++;
}

// While loop with array
let numbers = [1, 2, 3, 4, 5];
let index = 0;

while (index < numbers.length) {
    console.log(numbers[index]);
    index++;
}

// Infinite loop (be careful!)
// while (true) {
//     console.log('This will run forever');
// }
```

### Do-While Loop

```javascript
// Executes at least once, even if condition is false
let num = 0;

do {
    console.log(num); // 0
    num++;
} while (num < 0); // Condition is false, but loop ran once

// Practical example
let userInput;

do {
    userInput = prompt('Enter a number greater than 10:');
} while (userInput <= 10);
```

### For...of Loop (ES6)

```javascript
// Iterate over array values
let fruits = ['apple', 'banana', 'orange'];

for (let fruit of fruits) {
    console.log(fruit);
}

// Iterate over string characters
let text = 'Hello';

for (let char of text) {
    console.log(char); // H, e, l, l, o
}

// With index
for (let [index, fruit] of fruits.entries()) {
    console.log(`${index}: ${fruit}`);
}
```

### For...in Loop

```javascript
// Iterate over object keys
let person = {
    name: 'John',
    age: 30,
    city: 'New York'
};

for (let key in person) {
    console.log(`${key}: ${person[key]}`);
}

// With arrays (not recommended - use for...of instead)
let colors = ['red', 'green', 'blue'];

for (let index in colors) {
    console.log(`${index}: ${colors[index]}`);
}
```

### Break and Continue

```javascript
// Break - exit loop immediately
for (let i = 0; i < 10; i++) {
    if (i === 5) {
        break; // Exit loop when i is 5
    }
    console.log(i); // 0, 1, 2, 3, 4
}

// Continue - skip current iteration
for (let i = 0; i < 10; i++) {
    if (i % 2 === 0) {
        continue; // Skip even numbers
    }
    console.log(i); // 1, 3, 5, 7, 9
}

// Break with labels
outer: for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
        if (i === 1 && j === 1) {
            break outer; // Break out of outer loop
        }
        console.log(`${i}, ${j}`);
    }
}
```

---

## Best Practices

### Variable Declaration

1. **Use `const` by default**, `let` when reassignment is needed
2. **Avoid `var`** in modern JavaScript
3. **Declare variables at the top** of their scope
4. **Use meaningful names** that describe the variable's purpose
5. **Initialize variables** when declaring them when possible

```javascript
// Good
const MAX_USERS = 100;
let currentUserCount = 0;
const apiEndpoint = 'https://api.example.com';

// Avoid
var x = 10;
let a;
const temp = 'data';
```

### Code Organization

1. **Use template literals** instead of string concatenation
2. **Prefer strict equality** (`===`) over loose equality (`==`)
3. **Use descriptive conditions** instead of complex logic
4. **Keep functions focused** on a single task
5. **Add comments** for complex logic

```javascript
// Good
const fullName = `${firstName} ${lastName}`;
if (age === 18) {
    // Handle adult logic
}

// Avoid
const fullName = firstName + ' ' + lastName;
if (age == 18) {
    // Type coercion issues
}
```

### Error Prevention

1. **Validate input** before using it
2. **Use optional chaining** for nested properties
3. **Provide default values** with nullish coalescing
4. **Handle edge cases** explicitly

```javascript
// Good
const userName = user?.profile?.name ?? 'Guest';
const age = parseInt(userInput) || 0;

if (users && users.length > 0) {
    // Process users
}

// Avoid
const userName = user.profile.name; // May throw error
const age = userInput; // May be NaN
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Variables | Use `const` for constants, `let` for variables, avoid `var` |
| Data Types | Primitives: number, string, boolean, null, undefined, symbol, bigint |
| Operators | Arithmetic, comparison (use `===`), logical, ternary, nullish coalescing |
| Control Flow | if/else, switch, for, while, do-while, for...of, for...in |

## Next Topic

Continue to [Functions and Scope](./02-functions-and-scope.md) to learn about function declarations, arrow functions, callbacks, closures, and the IIFE pattern.
