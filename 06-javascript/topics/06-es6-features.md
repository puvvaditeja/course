# ES6+ Features

## Introduction to ES6

ECMAScript 2015 (ES6) was a major update to JavaScript that introduced many new features and syntax improvements. Subsequent versions (ES2016, ES2017, etc.) continue to add features.

### What is ECMAScript?

- **ECMAScript**: The specification/standard for JavaScript
- **JavaScript**: Implementation of ECMAScript
- **ES6/ES2015**: Major update released in 2015
- **ES.Next**: Refers to upcoming features

### Browser Support

Modern browsers support ES6+ features, but older browsers may require transpilation with tools like Babel.

---

## let and const

Modern variable declarations that replace `var`.

### let - Block-Scoped Variables

```javascript
// let is block-scoped
{
    let x = 10;
    console.log(x); // 10
}
// console.log(x); // Error: x is not defined

// var is function-scoped
{
    var y = 10;
    console.log(y); // 10
}
console.log(y); // 10 (accessible outside block)

// let in loops
for (let i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i); // 0, 1, 2 (each iteration has its own i)
    }, 100);
}

// var in loops
for (var j = 0; j < 3; j++) {
    setTimeout(() => {
        console.log(j); // 3, 3, 3 (shares the same j)
    }, 100);
}

// let cannot be redeclared in same scope
let name = 'John';
// let name = 'Jane'; // Error: Identifier 'name' has already been declared

// let can be reassigned
name = 'Jane'; // OK
```

### const - Constants

```javascript
// const must be initialized
// const x; // Error: Missing initializer

const PI = 3.14159;
const MAX_SIZE = 100;

// const cannot be reassigned
// PI = 3.14; // Error: Assignment to constant variable

// const with objects - reference is constant, not contents
const person = {
    name: 'John',
    age: 30
};

person.age = 31;           // OK - modifying property
person.city = 'New York';  // OK - adding property
// person = {};            // Error - cannot reassign

// const with arrays
const numbers = [1, 2, 3];
numbers.push(4);           // OK - modifying array
numbers[0] = 10;           // OK - modifying element
// numbers = [];           // Error - cannot reassign

// Prevent object modification with Object.freeze()
const frozen = Object.freeze({
    name: 'John',
    age: 30
});

frozen.age = 31;    // Silently fails (strict mode throws error)
frozen.city = 'NY'; // Silently fails
delete frozen.name; // Silently fails
```

### Best Practices

```javascript
// Use const by default
const API_URL = 'https://api.example.com';
const MAX_RETRIES = 3;

// Use let when reassignment is needed
let currentIndex = 0;
let isLoading = false;

// Avoid var in modern code
// var should not be used in ES6+ code

// Naming conventions
const CONSTANT_VALUE = 'uppercase for true constants';
const normalVariable = 'camelCase for regular const';
```

---

## Arrow Functions

Concise syntax for writing functions with lexical `this` binding.

### Basic Syntax

```javascript
// Traditional function
function add(a, b) {
    return a + b;
}

// Arrow function
const add = (a, b) => {
    return a + b;
};

// Implicit return (single expression)
const add = (a, b) => a + b;

// Single parameter - parentheses optional
const square = x => x * x;

// No parameters - parentheses required
const greet = () => console.log('Hello');

// Multiple statements - curly braces required
const calculate = (a, b) => {
    const sum = a + b;
    const product = a * b;
    return { sum, product };
};

// Returning object literal - wrap in parentheses
const createPerson = (name, age) => ({ name, age });

console.log(createPerson('John', 30)); // { name: 'John', age: 30 }
```

### Lexical 'this'

```javascript
// Traditional function - dynamic 'this'
function Person(name) {
    this.name = name;
    this.sayHi = function() {
        setTimeout(function() {
            console.log(`Hi, I'm ${this.name}`); // 'this' is undefined
        }, 1000);
    };
}

// Arrow function - lexical 'this'
function Person(name) {
    this.name = name;
    this.sayHi = function() {
        setTimeout(() => {
            console.log(`Hi, I'm ${this.name}`); // 'this' is Person instance
        }, 1000);
    };
}

// Class example
class Counter {
    constructor() {
        this.count = 0;
    }

    // Arrow function in class field (retains 'this')
    increment = () => {
        this.count++;
        console.log(this.count);
    }

    // Regular method
    decrement() {
        this.count--;
        console.log(this.count);
    }
}

const counter = new Counter();
const incrementFn = counter.increment;
incrementFn(); // Works - arrow function has bound 'this'

const decrementFn = counter.decrement;
// decrementFn(); // Error - 'this' is undefined
```

### When to Use Arrow Functions

```javascript
// Good use cases:

// 1. Array methods
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((total, n) => total + n, 0);

// 2. Callbacks
setTimeout(() => console.log('Delayed'), 1000);
button.addEventListener('click', () => console.log('Clicked'));

// 3. Promise chains
fetch('/api/data')
    .then(response => response.json())
    .then(data => processData(data))
    .catch(error => console.error(error));

// Avoid arrow functions for:

// 1. Object methods (lose 'this')
const person = {
    name: 'John',
    greet: () => {
        console.log(`Hi, ${this.name}`); // 'this' is not person
    }
};

// 2. When you need arguments object
const sum = () => {
    // console.log(arguments); // Error: arguments is not defined
};

// 3. Constructors
// const Person = (name) => {
//     this.name = name; // Error: Cannot use 'new' with arrow function
// };
```

---

## Template Literals

Template literals provide an easy way to create strings with embedded expressions.

### Basic Syntax

```javascript
// Traditional string concatenation
const name = 'John';
const age = 30;
const message = 'My name is ' + name + ' and I am ' + age + ' years old.';

// Template literal
const message = `My name is ${name} and I am ${age} years old.`;

// Expressions in template literals
const a = 5;
const b = 10;
console.log(`Sum: ${a + b}`);        // "Sum: 15"
console.log(`Product: ${a * b}`);    // "Product: 50"

// Function calls
const userName = 'john_doe';
console.log(`Welcome, ${userName.toUpperCase()}!`); // "Welcome, JOHN_DOE!"

// Nested template literals
const condition = true;
const message = `Status: ${condition ? 'Active' : 'Inactive'}`;
```

### Multi-line Strings

```javascript
// Traditional multi-line (ugly)
const html1 = '<div>\n' +
              '  <h1>Title</h1>\n' +
              '  <p>Paragraph</p>\n' +
              '</div>';

// Template literal multi-line (clean)
const html2 = `
<div>
  <h1>Title</h1>
  <p>Paragraph</p>
</div>
`;

// SQL query example
const query = `
  SELECT *
  FROM users
  WHERE age > 18
    AND active = true
  ORDER BY name
`;

// Email template
const emailTemplate = `
Dear ${name},

Thank you for your order #${orderId}.
Your total is $${total.toFixed(2)}.

Best regards,
The Team
`;
```

### Tagged Templates

```javascript
// Custom template processing
function highlight(strings, ...values) {
    return strings.reduce((result, str, i) => {
        return result + str + (values[i] ? `<strong>${values[i]}</strong>` : '');
    }, '');
}

const name = 'John';
const age = 30;
const message = highlight`My name is ${name} and I am ${age} years old.`;
console.log(message);
// "My name is <strong>John</strong> and I am <strong>30</strong> years old."

// SQL query builder
function sql(strings, ...values) {
    return {
        query: strings.join('?'),
        values: values
    };
}

const userId = 5;
const userName = 'john';
const query = sql`SELECT * FROM users WHERE id = ${userId} AND name = ${userName}`;
console.log(query);
// { query: 'SELECT * FROM users WHERE id = ? AND name = ?',
//   values: [5, 'john'] }

// Styling example
function styled(strings, ...values) {
    return strings.reduce((result, str, i) => {
        return result + str + (values[i] || '');
    }, '');
}

const color = 'blue';
const fontSize = '16px';
const css = styled`
    color: ${color};
    font-size: ${fontSize};
`;
```

---

## Destructuring

Extract values from arrays or properties from objects into distinct variables.

### Array Destructuring

```javascript
// Traditional approach
const numbers = [1, 2, 3];
const first = numbers[0];
const second = numbers[1];
const third = numbers[2];

// Array destructuring
const [first, second, third] = [1, 2, 3];
console.log(first, second, third); // 1 2 3

// Skip elements
const [first, , third] = [1, 2, 3];
console.log(first, third); // 1 3

// Rest pattern
const [first, ...rest] = [1, 2, 3, 4, 5];
console.log(first); // 1
console.log(rest);  // [2, 3, 4, 5]

// Default values
const [a = 1, b = 2, c = 3] = [10, 20];
console.log(a, b, c); // 10 20 3

// Swapping variables
let x = 1;
let y = 2;
[x, y] = [y, x];
console.log(x, y); // 2 1

// Function return values
function getCoordinates() {
    return [10, 20];
}
const [x, y] = getCoordinates();

// Nested arrays
const nested = [1, [2, 3], 4];
const [a, [b, c], d] = nested;
console.log(a, b, c, d); // 1 2 3 4
```

### Object Destructuring

```javascript
// Traditional approach
const person = { name: 'John', age: 30, city: 'New York' };
const name = person.name;
const age = person.age;
const city = person.city;

// Object destructuring
const { name, age, city } = person;
console.log(name, age, city); // "John" 30 "New York"

// Different variable names
const { name: userName, age: userAge } = person;
console.log(userName, userAge); // "John" 30

// Default values
const { name, age, country = 'USA' } = person;
console.log(country); // "USA"

// Rest pattern
const { name, ...rest } = person;
console.log(name);  // "John"
console.log(rest);  // { age: 30, city: "New York" }

// Nested objects
const user = {
    id: 1,
    name: 'John',
    address: {
        street: '123 Main St',
        city: 'New York',
        coordinates: {
            lat: 40.7128,
            lng: -74.0060
        }
    }
};

const {
    name,
    address: {
        city,
        coordinates: { lat, lng }
    }
} = user;

console.log(name, city, lat, lng); // "John" "New York" 40.7128 -74.0060

// Function parameters
function greet({ name, age = 0 }) {
    console.log(`Hello, ${name}! You are ${age} years old.`);
}

greet({ name: 'John', age: 30 }); // "Hello, John! You are 30 years old."
greet({ name: 'Jane' });          // "Hello, Jane! You are 0 years old."

// Computed property names
const key = 'name';
const { [key]: value } = { name: 'John' };
console.log(value); // "John"
```

### Practical Examples

```javascript
// API response handling
async function getUser() {
    const response = await fetch('/api/user');
    const { data: { user: { name, email } } } = await response.json();
    return { name, email };
}

// React props
function UserCard({ name, age, avatar = 'default.png' }) {
    return `<div>${name}, ${age}, <img src="${avatar}"></div>`;
}

// Configuration objects
function createServer({ port = 3000, host = 'localhost', ssl = false }) {
    console.log(`Server running on ${host}:${port}, SSL: ${ssl}`);
}

createServer({ port: 8080 }); // "Server running on localhost:8080, SSL: false"

// Array methods with destructuring
const users = [
    { name: 'John', age: 30 },
    { name: 'Jane', age: 25 },
    { name: 'Bob', age: 35 }
];

users.forEach(({ name, age }) => {
    console.log(`${name} is ${age} years old`);
});

const names = users.map(({ name }) => name);
console.log(names); // ['John', 'Jane', 'Bob']
```

---

## Spread and Rest Operators

The spread (`...`) operator expands iterables, while rest collects multiple elements.

### Spread Operator

**Array Spread:**
```javascript
// Copy array
const original = [1, 2, 3];
const copy = [...original];
copy.push(4);
console.log(original); // [1, 2, 3]
console.log(copy);     // [1, 2, 3, 4]

// Concatenate arrays
const arr1 = [1, 2];
const arr2 = [3, 4];
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4]

// Add elements
const numbers = [2, 3];
const extended = [1, ...numbers, 4, 5];
console.log(extended); // [1, 2, 3, 4, 5]

// Convert to array
const str = 'hello';
const chars = [...str];
console.log(chars); // ['h', 'e', 'l', 'l', 'o']

const nodeList = document.querySelectorAll('div');
const divsArray = [...nodeList];

// Math functions
const numbers = [5, 2, 8, 1, 9];
console.log(Math.max(...numbers)); // 9
console.log(Math.min(...numbers)); // 1

// Remove duplicates
const withDuplicates = [1, 2, 2, 3, 3, 4];
const unique = [...new Set(withDuplicates)];
console.log(unique); // [1, 2, 3, 4]
```

**Object Spread:**
```javascript
// Copy object (shallow)
const original = { name: 'John', age: 30 };
const copy = { ...original };
copy.age = 31;
console.log(original.age); // 30
console.log(copy.age);     // 31

// Merge objects
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const merged = { ...obj1, ...obj2 };
console.log(merged); // { a: 1, b: 2, c: 3, d: 4 }

// Override properties
const defaults = { port: 3000, host: 'localhost' };
const config = { ...defaults, port: 8080 };
console.log(config); // { port: 8080, host: 'localhost' }

// Add properties
const user = { name: 'John', age: 30 };
const userWithId = { id: 1, ...user, active: true };
console.log(userWithId);
// { id: 1, name: 'John', age: 30, active: true }

// Conditional properties
const includeEmail = true;
const user = {
    name: 'John',
    ...(includeEmail && { email: 'john@example.com' })
};
console.log(user); // { name: 'John', email: 'john@example.com' }
```

### Rest Operator

**Function Parameters:**
```javascript
// Collect remaining arguments
function sum(...numbers) {
    return numbers.reduce((total, n) => total + n, 0);
}

console.log(sum(1, 2, 3));        // 6
console.log(sum(1, 2, 3, 4, 5));  // 15

// Rest with other parameters
function greet(greeting, ...names) {
    return `${greeting}, ${names.join(' and ')}!`;
}

console.log(greet('Hello', 'John', 'Jane', 'Bob'));
// "Hello, John and Jane and Bob!"

// Replace arguments object
function oldWay() {
    const args = Array.from(arguments);
    return args.reduce((sum, n) => sum + n, 0);
}

function newWay(...args) {
    return args.reduce((sum, n) => sum + n, 0);
}
```

**Destructuring:**
```javascript
// Array rest
const [first, second, ...rest] = [1, 2, 3, 4, 5];
console.log(first);  // 1
console.log(second); // 2
console.log(rest);   // [3, 4, 5]

// Object rest
const { name, age, ...otherInfo } = {
    name: 'John',
    age: 30,
    city: 'New York',
    country: 'USA'
};

console.log(name);      // "John"
console.log(age);       // 30
console.log(otherInfo); // { city: "New York", country: "USA" }

// Function parameter destructuring with rest
function updateUser(id, { name, age, ...updates }) {
    console.log(`Updating user ${id}`);
    console.log(`Name: ${name}, Age: ${age}`);
    console.log('Other updates:', updates);
}

updateUser(1, {
    name: 'John',
    age: 30,
    city: 'New York',
    active: true
});
```

---

## Enhanced Object Literals

ES6 provides shorthand syntax for object properties and methods.

### Property Shorthand

```javascript
// Traditional
const name = 'John';
const age = 30;

const person1 = {
    name: name,
    age: age
};

// ES6 shorthand
const person2 = {
    name,
    age
};

console.log(person2); // { name: 'John', age: 30 }

// Practical example
function createUser(name, email, role) {
    return {
        name,
        email,
        role,
        active: true,
        createdAt: new Date()
    };
}
```

### Method Shorthand

```javascript
// Traditional
const calculator1 = {
    add: function(a, b) {
        return a + b;
    },
    subtract: function(a, b) {
        return a - b;
    }
};

// ES6 shorthand
const calculator2 = {
    add(a, b) {
        return a + b;
    },
    subtract(a, b) {
        return a - b;
    }
};
```

### Computed Property Names

```javascript
// Traditional - add property after creation
const obj = {};
const key = 'dynamicKey';
obj[key] = 'value';

// ES6 - computed property in literal
const key = 'dynamicKey';
const obj = {
    [key]: 'value'
};

console.log(obj.dynamicKey); // "value"

// Expressions
const prefix = 'user';
const id = 123;

const user = {
    [`${prefix}_${id}`]: 'John Doe',
    [prefix + 'Name']: 'John',
    [`get${prefix.charAt(0).toUpperCase() + prefix.slice(1)}`]() {
        return this.userName;
    }
};

console.log(user.user_123);  // "John Doe"
console.log(user.userName);  // "John"
console.log(user.getUser()); // "John"

// Dynamic methods
const methods = ['get', 'post', 'put', 'delete'];
const api = {};

methods.forEach(method => {
    api[method] = function(url) {
        console.log(`${method.toUpperCase()} ${url}`);
    };
});

// ES6 version
const api = methods.reduce((obj, method) => ({
    ...obj,
    [method](url) {
        console.log(`${method.toUpperCase()} ${url}`);
    }
}), {});
```

---

## Modules

ES6 modules provide a way to organize code into reusable pieces.

### Export

**Named Exports:**
```javascript
// utils.js

// Export individual items
export const PI = 3.14159;
export const MAX_SIZE = 100;

export function add(a, b) {
    return a + b;
}

export class Calculator {
    add(a, b) {
        return a + b;
    }
}

// Export list
const multiply = (a, b) => a * b;
const divide = (a, b) => a / b;

export { multiply, divide };

// Export with rename
const subtract = (a, b) => a - b;
export { subtract as sub };
```

**Default Export:**
```javascript
// person.js

// One default export per module
export default class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    greet() {
        return `Hello, I'm ${this.name}`;
    }
}

// OR

class Person {
    // ... class definition
}

export default Person;

// Default export with named exports
export const DEFAULT_AGE = 0;
export default Person;
```

### Import

**Named Imports:**
```javascript
// Import specific items
import { add, multiply } from './utils.js';

console.log(add(5, 3));      // 8
console.log(multiply(5, 3)); // 15

// Import with rename
import { sub as subtract } from './utils.js';

// Import all as namespace
import * as utils from './utils.js';

console.log(utils.PI);           // 3.14159
console.log(utils.add(5, 3));    // 8
console.log(utils.multiply(5, 3)); // 15

// Import multiple items
import { add, subtract, multiply, divide } from './utils.js';
```

**Default Import:**
```javascript
// Import default export (can name it anything)
import Person from './person.js';

const person = new Person('John', 30);
console.log(person.greet()); // "Hello, I'm John"

// Import default and named
import Person, { DEFAULT_AGE } from './person.js';

// Rename default import
import PersonClass from './person.js';
```

**Mixed Imports:**
```javascript
// Import default and named exports together
import React, { useState, useEffect } from 'react';

// Import for side effects only (runs module code)
import './styles.css';
import './polyfills.js';
```

### Dynamic Imports

```javascript
// Lazy loading
async function loadModule() {
    const module = await import('./heavy-module.js');
    module.doSomething();
}

// Conditional loading
if (condition) {
    import('./module-a.js').then(module => {
        module.init();
    });
} else {
    import('./module-b.js').then(module => {
        module.init();
    });
}

// Code splitting in React
const LazyComponent = React.lazy(() => import('./Component.js'));

// Dynamic module path
const lang = 'en';
import(`./translations/${lang}.js`).then(module => {
    console.log(module.translations);
});
```

### Module Patterns

**Single Responsibility:**
```javascript
// user.js - User-related functionality
export class User {
    constructor(name, email) {
        this.name = name;
        this.email = email;
    }
}

export function validateEmail(email) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

export const USER_ROLES = {
    ADMIN: 'admin',
    USER: 'user'
};
```

**API Client:**
```javascript
// api.js
const BASE_URL = 'https://api.example.com';

async function request(endpoint, options = {}) {
    const response = await fetch(`${BASE_URL}${endpoint}`, {
        headers: {
            'Content-Type': 'application/json',
            ...options.headers
        },
        ...options
    });

    if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
    }

    return response.json();
}

export const getUsers = () => request('/users');
export const getUser = (id) => request(`/users/${id}`);
export const createUser = (data) => request('/users', {
    method: 'POST',
    body: JSON.stringify(data)
});

export default { getUsers, getUser, createUser };
```

**Configuration:**
```javascript
// config.js
export const config = {
    api: {
        baseURL: process.env.API_URL || 'http://localhost:3000',
        timeout: 5000
    },
    features: {
        enableAnalytics: true,
        enableChat: false
    }
};

export default config;
```

---

## Other ES6+ Features

### Default Parameters

```javascript
// ES5 way
function greet(name, greeting) {
    greeting = greeting || 'Hello';
    return `${greeting}, ${name}!`;
}

// ES6 way
function greet(name, greeting = 'Hello') {
    return `${greeting}, ${name}!`;
}

console.log(greet('John'));           // "Hello, John!"
console.log(greet('John', 'Hi'));     // "Hi, John!"

// Default values can reference other parameters
function createUser(name, role = 'user', active = role === 'admin') {
    return { name, role, active };
}

// Default values can be function calls
function getDefaultName() {
    return 'Guest';
}

function greet(name = getDefaultName()) {
    return `Hello, ${name}!`;
}
```

### Classes

```javascript
// Class declaration
class Person {
    // Constructor
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    // Instance method
    greet() {
        return `Hello, I'm ${this.name}`;
    }

    // Getter
    get birthYear() {
        return new Date().getFullYear() - this.age;
    }

    // Setter
    set birthYear(year) {
        this.age = new Date().getFullYear() - year;
    }

    // Static method
    static species() {
        return 'Homo sapiens';
    }

    // Static property
    static planet = 'Earth';
}

const person = new Person('John', 30);
console.log(person.greet());         // "Hello, I'm John"
console.log(person.birthYear);       // 1994
console.log(Person.species());       // "Homo sapiens"

// Inheritance
class Employee extends Person {
    constructor(name, age, job) {
        super(name, age); // Call parent constructor
        this.job = job;
    }

    greet() {
        return `${super.greet()}. I'm a ${this.job}`;
    }
}

const employee = new Employee('Jane', 25, 'Developer');
console.log(employee.greet());
// "Hello, I'm Jane. I'm a Developer"
```

### for...of Loop

```javascript
// Iterate over iterable objects
const numbers = [1, 2, 3, 4, 5];

for (const num of numbers) {
    console.log(num);
}

// With strings
const str = 'hello';
for (const char of str) {
    console.log(char);
}

// With Set
const set = new Set([1, 2, 3]);
for (const value of set) {
    console.log(value);
}

// With Map
const map = new Map([
    ['a', 1],
    ['b', 2]
]);

for (const [key, value] of map) {
    console.log(`${key}: ${value}`);
}

// With entries()
for (const [index, value] of numbers.entries()) {
    console.log(`${index}: ${value}`);
}
```

### Symbol

```javascript
// Create unique identifiers
const id1 = Symbol('id');
const id2 = Symbol('id');

console.log(id1 === id2); // false (unique)

// Use as object property
const user = {
    name: 'John',
    [id1]: 123
};

console.log(user[id1]); // 123
console.log(user['id']); // undefined

// Symbols not included in for...in
for (const key in user) {
    console.log(key); // Only "name"
}

// Well-known symbols
const obj = {
    [Symbol.iterator]() {
        let count = 0;
        return {
            next() {
                count++;
                if (count <= 3) {
                    return { value: count, done: false };
                }
                return { done: true };
            }
        };
    }
};

for (const value of obj) {
    console.log(value); // 1, 2, 3
}
```

---

## Best Practices

### Modern Syntax

1. **Use const/let** instead of var
2. **Use arrow functions** for callbacks
3. **Use template literals** for strings
4. **Use destructuring** to extract values
5. **Use spread/rest** for arrays and objects

```javascript
// Good
const users = ['John', 'Jane', 'Bob'];
const [first, ...rest] = users;
const message = `First user: ${first}`;

// Avoid
var users = ['John', 'Jane', 'Bob'];
var first = users[0];
var rest = users.slice(1);
var message = 'First user: ' + first;
```

### Modules

1. **One module per file** (single responsibility)
2. **Named exports** for multiple items
3. **Default export** for main functionality
4. **Organize imports** (built-in, external, internal)

```javascript
// Good organization
import React from 'react';              // External default
import { useState, useEffect } from 'react'; // External named

import { api } from './utils/api';      // Internal
import Header from './components/Header'; // Internal default

// Module code
```

### Classes

1. **Use classes** for object blueprints
2. **Constructor for initialization**
3. **Methods for behavior**
4. **Static methods** for utility functions

```javascript
// Good
class UserService {
    constructor(apiUrl) {
        this.apiUrl = apiUrl;
    }

    async getUser(id) {
        const response = await fetch(`${this.apiUrl}/users/${id}`);
        return response.json();
    }

    static validateId(id) {
        return typeof id === 'number' && id > 0;
    }
}
```

---

## Summary

| Feature | Description | Example |
|---------|-------------|---------|
| let/const | Block-scoped variables | `const name = 'John'` |
| Arrow Functions | Concise function syntax | `const add = (a, b) => a + b` |
| Template Literals | String interpolation | `` `Hello, ${name}` `` |
| Destructuring | Extract values | `const { name } = user` |
| Spread/Rest | Expand/collect items | `[...array]`, `...rest` |
| Modules | Code organization | `import/export` |
| Classes | Object blueprints | `class Person {}` |

## Module Complete

You've completed the JavaScript module! You should now understand:
- JavaScript fundamentals and syntax
- Functions, scope, and closures
- DOM manipulation and events
- Objects and JSON
- Asynchronous programming
- Modern ES6+ features

Continue to the next module in your curriculum to build on these JavaScript skills.
