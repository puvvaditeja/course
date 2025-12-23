# Code Challenges: JavaScript

Short coding problems to practice JavaScript fundamentals. Complete each challenge in a separate file.

---

## Getting Started Challenges

### Challenge 1: Mad Libs Game
**File:** `mad-libs.js`

Create a Mad Libs-style story generator:

```javascript
// Ask the user for:
// - A name
// - An adjective
// - A noun
// - A verb
// - A place

// Create a story using their inputs
// Example output:
// "One day, John found a shiny unicorn that could dance in the kitchen."
```

---

### Challenge 2: Temperature Converter
**File:** `temperature-converter.js`

Create a temperature converter:

```javascript
// Ask user to choose conversion type:
// 1. Celsius to Fahrenheit
// 2. Fahrenheit to Celsius

// Ask for the temperature value

// Perform conversion and display result
// Formulas:
// F = (C × 9/5) + 32
// C = (F − 32) × 5/9
```

---

### Challenge 3: Basic Quiz
**File:** `basic-quiz.js`

Create a simple quiz:

```javascript
// Create a 3-question quiz
// Keep track of the score
// Display final score at the end

// Example questions:
// 1. What is 5 + 3?
// 2. What is the capital of France?
// 3. What color is the sky?
```

---

### Challenge 4: BMI Calculator
**File:** `bmi-calculator.js`

Create a BMI calculator:

```javascript
// Ask for weight in kg
// Ask for height in meters
// Calculate BMI: weight / (height * height)
// Display BMI and category:
// - Underweight: < 18.5
// - Normal: 18.5 - 24.9
// - Overweight: 25 - 29.9
// - Obese: >= 30
```

---

## Variables & Data Types Challenges

### Challenge 5: Variable Swap
**File:** `variable-swap.js`

Swap two variables without using a third variable:

```javascript
let x = 5;
let y = 10;

// Swap x and y without creating a new variable
// Your code here

console.log(x); // Should be 10
console.log(y); // Should be 5
```

---

### Challenge 6: Type Checker Function
**File:** `type-checker.js`

Create a function that returns the actual type of any value:

```javascript
function getType(value) {
    // Return:
    // - "null" for null
    // - "array" for arrays
    // - "object" for plain objects
    // - "function" for functions
    // - The typeof result for primitives
}

// Tests
console.log(getType(null));        // "null"
console.log(getType([1, 2, 3]));   // "array"
console.log(getType({ a: 1 }));    // "object"
console.log(getType(() => {}));    // "function"
console.log(getType(42));          // "number"
console.log(getType("hello"));     // "string"
```

---

### Challenge 7: Deep Clone
**File:** `deep-clone.js`

Create a function to deep clone an object:

```javascript
let original = {
    name: 'John',
    address: {
        city: 'New York',
        zip: '10001'
    },
    hobbies: ['reading', 'gaming']
};

function deepClone(obj) {
    // Your code here
}

let cloned = deepClone(original);
cloned.address.city = 'Boston';
cloned.hobbies.push('swimming');

console.log(original.address.city);  // Should still be 'New York'
console.log(original.hobbies);       // Should still be ['reading', 'gaming']
```

---

## Operators & Control Flow Challenges

### Challenge 8: Number Pyramid
**File:** `number-pyramid.js`

Print a number pyramid:

```javascript
function printPyramid(rows) {
    // For rows = 5, output:
    //     1
    //    121
    //   12321
    //  1234321
    // 123454321
}
```

---

### Challenge 9: Prime Numbers
**File:** `prime-numbers.js`

Find all prime numbers:

```javascript
function findPrimes(limit) {
    // Return an array of all prime numbers from 2 to limit
}

console.log(findPrimes(30));
// [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
```

---

### Challenge 10: Validate Password
**File:** `validate-password.js`

Create a password validator:

```javascript
function validatePassword(password) {
    // Return an object with:
    // - isValid: boolean
    // - errors: array of error messages

    // Rules:
    // - At least 8 characters
    // - Contains at least one uppercase letter
    // - Contains at least one lowercase letter
    // - Contains at least one number
    // - Contains at least one special character (!@#$%^&*)
}

console.log(validatePassword('Abc123!@'));
// { isValid: true, errors: [] }

console.log(validatePassword('abc'));
// { isValid: false, errors: ['Too short', 'Missing uppercase', 'Missing number', 'Missing special character'] }
```

---

## Functions Challenges

### Challenge 11: Function Composition
**File:** `compose.js`

Create a compose function:

```javascript
// Create a function that composes multiple functions
const compose = (...functions) => {
    // Return a function that applies all functions right-to-left
};

const addOne = x => x + 1;
const double = x => x * 2;
const square = x => x * x;

const composed = compose(addOne, double, square);
console.log(composed(3)); // addOne(double(square(3))) = addOne(double(9)) = addOne(18) = 19
```

---

### Challenge 12: Curry Function
**File:** `curry.js`

Implement currying:

```javascript
// Create a curry function that allows partial application
const curry = (fn) => {
    // Return a curried version of fn
};

const add = (a, b, c) => a + b + c;
const curriedAdd = curry(add);

console.log(curriedAdd(1)(2)(3));    // 6
console.log(curriedAdd(1, 2)(3));    // 6
console.log(curriedAdd(1)(2, 3));    // 6
console.log(curriedAdd(1, 2, 3));    // 6
```

---

### Challenge 13: Memoization
**File:** `memoize.js`

Create a memoize function:

```javascript
const memoize = (fn) => {
    // Return a memoized version that caches results
};

// Test with an expensive function
const expensiveOperation = (n) => {
    console.log('Computing...');
    let result = 0;
    for (let i = 0; i < n * 1000000; i++) {
        result += i;
    }
    return result;
};

const memoizedOperation = memoize(expensiveOperation);

console.log(memoizedOperation(5)); // "Computing..." then result
console.log(memoizedOperation(5)); // Just result (cached, no "Computing...")
```

---

### Challenge 14: Debounce Function
**File:** `debounce.js`

Implement debounce:

```javascript
const debounce = (fn, delay) => {
    // Return a debounced version of fn
    // Only executes after delay ms of inactivity
};

// Usage example:
// const handleSearch = debounce((query) => {
//     console.log('Searching for:', query);
// }, 300);
```

---

## Scope & Closures Challenges

### Challenge 15: Once Function
**File:** `once.js`

A function that can only be called once:

```javascript
function once(fn) {
    // Return a function that can only execute fn once
    // Subsequent calls return the first result
}

const initialize = once(() => {
    console.log('Initializing...');
    return 'Initialized';
});

console.log(initialize()); // "Initializing..." then "Initialized"
console.log(initialize()); // Just "Initialized" (no log, cached result)
console.log(initialize()); // Just "Initialized"
```

---

### Challenge 16: Rate Limiter
**File:** `rate-limiter.js`

Limit function calls:

```javascript
function createRateLimiter(fn, limit, interval) {
    // Return a function that can only be called 'limit' times per 'interval' ms
    // Returns false if rate limit exceeded
}

const limitedFetch = createRateLimiter(
    (url) => console.log('Fetching:', url),
    3,  // 3 calls
    1000 // per second
);

console.log(limitedFetch('/api/1')); // "Fetching: /api/1", true
console.log(limitedFetch('/api/2')); // "Fetching: /api/2", true
console.log(limitedFetch('/api/3')); // "Fetching: /api/3", true
console.log(limitedFetch('/api/4')); // false (rate limited)
```

---

### Challenge 17: Private Class-like Structure
**File:** `create-person.js`

Create a "class" with private members using closures:

```javascript
function createPerson(name, age) {
    // Private variables and validation

    return {
        getName: function() { /* ... */ },
        getAge: function() { /* ... */ },
        setName: function(newName) { /* validate and set */ },
        setAge: function(newAge) { /* validate: must be 0-150 */ },
        birthday: function() { /* increment age */ },
        introduce: function() { /* return introduction string */ }
    };
}

const person = createPerson('John', 25);
console.log(person.getName());     // "John"
console.log(person.getAge());      // 25
person.birthday();
console.log(person.getAge());      // 26
person.setAge(200);                // Should fail validation
console.log(person.getAge());      // Still 26
console.log(person.introduce());   // "Hi, I'm John and I'm 26 years old."
console.log(person.name);          // undefined (private)
```

---

## Array Methods Challenges

### Challenge 18: Implement Your Own Methods
**File:** `array-methods.js`

Recreate array methods without using them:

```javascript
// Implement myMap
function myMap(arr, callback) {
    // Your implementation
}

// Implement myFilter
function myFilter(arr, callback) {
    // Your implementation
}

// Implement myReduce
function myReduce(arr, callback, initialValue) {
    // Your implementation
}

// Test
console.log(myMap([1, 2, 3], x => x * 2)); // [2, 4, 6]
console.log(myFilter([1, 2, 3, 4], x => x > 2)); // [3, 4]
console.log(myReduce([1, 2, 3, 4], (acc, x) => acc + x, 0)); // 10
```

---

### Challenge 19: Data Pipeline
**File:** `data-pipeline.js`

Create a reusable data pipeline:

```javascript
function createPipeline(...operations) {
    return function(data) {
        return operations.reduce((result, operation) => operation(result), data);
    };
}

// Create operations
const filterInStock = products => products.filter(p => p.inStock);
const sortByPrice = products => [...products].sort((a, b) => a.price - b.price);
const getNames = products => products.map(p => p.name);
const takeFirst = n => items => items.slice(0, n);

// Create pipeline
const getTopCheapestInStock = createPipeline(
    filterInStock,
    sortByPrice,
    takeFirst(3),
    getNames
);

// Test with sample products array
```

---

### Challenge 20: Complex Aggregation
**File:** `product-report.js`

Build a complete report:

```javascript
function generateProductReport(products) {
    return {
        totalProducts: /* count */,
        totalValue: /* sum of prices */,
        averagePrice: /* average */,
        averageRating: /* average */,

        byCategory: /* { category: { count, totalValue, products: [...] } } */,

        stockStatus: {
            inStock: /* count */,
            outOfStock: /* count */,
            inStockPercentage: /* percentage */
        },

        priceRanges: {
            budget: /* count of items under $100 */,
            midRange: /* count of items $100-$500 */,
            premium: /* count of items over $500 */
        },

        topRated: /* top 3 products by rating */,
        recommendations: /* products with rating >= 4.5 and in stock */
    };
}
```

---

## Submission

Place all challenge files in: `06-javascript/challenges/`

| # | Challenge | File | Status |
|---|-----------|------|--------|
| 1 | Mad Libs Game | `mad-libs.js` | ⬜ |
| 2 | Temperature Converter | `temperature-converter.js` | ⬜ |
| 3 | Basic Quiz | `basic-quiz.js` | ⬜ |
| 4 | BMI Calculator | `bmi-calculator.js` | ⬜ |
| 5 | Variable Swap | `variable-swap.js` | ⬜ |
| 6 | Type Checker | `type-checker.js` | ⬜ |
| 7 | Deep Clone | `deep-clone.js` | ⬜ |
| 8 | Number Pyramid | `number-pyramid.js` | ⬜ |
| 9 | Prime Numbers | `prime-numbers.js` | ⬜ |
| 10 | Validate Password | `validate-password.js` | ⬜ |
| 11 | Function Composition | `compose.js` | ⬜ |
| 12 | Curry Function | `curry.js` | ⬜ |
| 13 | Memoization | `memoize.js` | ⬜ |
| 14 | Debounce Function | `debounce.js` | ⬜ |
| 15 | Once Function | `once.js` | ⬜ |
| 16 | Rate Limiter | `rate-limiter.js` | ⬜ |
| 17 | Private Class | `create-person.js` | ⬜ |
| 18 | Array Methods | `array-methods.js` | ⬜ |
| 19 | Data Pipeline | `data-pipeline.js` | ⬜ |
| 20 | Product Report | `product-report.js` | ⬜ |
