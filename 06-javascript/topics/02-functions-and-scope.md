# Functions and Scope

## What are Functions?

Functions are reusable blocks of code designed to perform a specific task. They are fundamental building blocks in JavaScript that help organize code, promote reusability, and make programs easier to maintain.

### Why Use Functions?

- **Code Reusability**: Write once, use multiple times
- **Modularity**: Break complex problems into smaller, manageable pieces
- **Abstraction**: Hide implementation details
- **Maintainability**: Update logic in one place
- **Testing**: Easier to test isolated functionality

---

## Function Declaration

The traditional way to define a function using the `function` keyword.

### Basic Syntax

```javascript
// Function declaration
function greet() {
    console.log('Hello, World!');
}

// Function call/invocation
greet(); // "Hello, World!"

// Function with parameters
function greetUser(name) {
    console.log(`Hello, ${name}!`);
}

greetUser('John');  // "Hello, John!"
greetUser('Jane');  // "Hello, Jane!"

// Function with multiple parameters
function add(a, b) {
    return a + b;
}

let result = add(5, 3);
console.log(result); // 8

// Function with default parameters (ES6)
function greetWithDefault(name = 'Guest') {
    console.log(`Hello, ${name}!`);
}

greetWithDefault();        // "Hello, Guest!"
greetWithDefault('John');  // "Hello, John!"
```

### Return Statement

```javascript
// Function returning a value
function multiply(a, b) {
    return a * b;
}

let product = multiply(4, 5);
console.log(product); // 20

// Multiple return statements
function getGrade(score) {
    if (score >= 90) return 'A';
    if (score >= 80) return 'B';
    if (score >= 70) return 'C';
    if (score >= 60) return 'D';
    return 'F';
}

console.log(getGrade(85)); // "B"

// Returning objects
function createUser(name, age) {
    return {
        name: name,
        age: age,
        isActive: true
    };
}

let user = createUser('John', 30);
console.log(user); // { name: 'John', age: 30, isActive: true }

// Early return pattern
function divide(a, b) {
    if (b === 0) {
        return 'Cannot divide by zero';
    }
    return a / b;
}
```

### Rest Parameters

```javascript
// Collect remaining arguments into an array
function sum(...numbers) {
    let total = 0;
    for (let num of numbers) {
        total += num;
    }
    return total;
}

console.log(sum(1, 2, 3));        // 6
console.log(sum(1, 2, 3, 4, 5));  // 15

// Rest parameter with other parameters
function greetAll(greeting, ...names) {
    for (let name of names) {
        console.log(`${greeting}, ${name}!`);
    }
}

greetAll('Hello', 'John', 'Jane', 'Bob');
// "Hello, John!"
// "Hello, Jane!"
// "Hello, Bob!"
```

### Hoisting

Function declarations are hoisted to the top of their scope, meaning they can be called before they're defined.

```javascript
// This works due to hoisting
sayHello(); // "Hello!"

function sayHello() {
    console.log('Hello!');
}

// The interpreter moves the declaration to the top:
// function sayHello() {
//     console.log('Hello!');
// }
// sayHello();
```

---

## Function Expressions

A function expression defines a function as part of an expression, typically by assigning it to a variable.

### Anonymous Function Expression

```javascript
// Function expression (anonymous)
const greet = function() {
    console.log('Hello, World!');
};

greet(); // "Hello, World!"

// With parameters
const add = function(a, b) {
    return a + b;
};

console.log(add(5, 3)); // 8

// Function expressions are NOT hoisted
// sayHi(); // Error: Cannot access 'sayHi' before initialization

const sayHi = function() {
    console.log('Hi!');
};
```

### Named Function Expression

```javascript
// Named function expression (useful for debugging)
const factorial = function fact(n) {
    if (n <= 1) return 1;
    return n * fact(n - 1); // Can reference itself by name
};

console.log(factorial(5)); // 120

// The name is only available inside the function
// fact(5); // Error: fact is not defined
```

---

## Arrow Functions (ES6)

Arrow functions provide a shorter syntax and have different behavior with the `this` keyword.

### Basic Syntax

```javascript
// Traditional function expression
const add1 = function(a, b) {
    return a + b;
};

// Arrow function
const add2 = (a, b) => {
    return a + b;
};

// Arrow function with implicit return (single expression)
const add3 = (a, b) => a + b;

console.log(add3(5, 3)); // 8

// Single parameter - parentheses optional
const square = x => x * x;
console.log(square(5)); // 25

// No parameters - parentheses required
const greet = () => console.log('Hello!');
greet(); // "Hello!"

// Multiple lines - use curly braces and explicit return
const calculateArea = (length, width) => {
    const area = length * width;
    return area;
};

// Returning object literals - wrap in parentheses
const createPerson = (name, age) => ({
    name: name,
    age: age
});

console.log(createPerson('John', 30)); // { name: 'John', age: 30 }
```

### Arrow Functions vs Regular Functions

| Feature | Regular Function | Arrow Function |
|---------|-----------------|----------------|
| Syntax | Verbose | Concise |
| `this` binding | Dynamic | Lexical |
| `arguments` object | Available | Not available |
| Can be constructor | Yes (with `new`) | No |
| Hoisting | Yes (declarations) | No |
| Method definition | Good choice | Avoid |

### When to Use Arrow Functions

```javascript
// Good use cases:

// 1. Short callbacks
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);

// 2. Array methods
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((total, n) => total + n, 0);

// 3. Promise chains
fetch('https://api.example.com/data')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error(error));

// 4. Event handlers (when you want lexical this)
class Counter {
    constructor() {
        this.count = 0;
    }

    increment = () => {
        this.count++;
        console.log(this.count);
    }
}

// Avoid for:

// 1. Object methods (loses 'this' context)
const person = {
    name: 'John',
    greet: () => {
        console.log(`Hello, ${this.name}`); // 'this' is not the person object
    }
};

// 2. When you need 'arguments' object
const showArgs = () => {
    console.log(arguments); // Error: arguments is not defined
};
```

---

## Callbacks

A callback is a function passed as an argument to another function, to be executed later.

### Basic Callbacks

```javascript
// Simple callback example
function processUserInput(callback) {
    const name = 'John';
    callback(name);
}

function greetUser(name) {
    console.log(`Hello, ${name}!`);
}

processUserInput(greetUser); // "Hello, John!"

// Inline callback
processUserInput(function(name) {
    console.log(`Hi, ${name}!`);
});

// Arrow function callback
processUserInput(name => console.log(`Hey, ${name}!`));
```

### Asynchronous Callbacks

```javascript
// setTimeout with callback
console.log('Start');

setTimeout(function() {
    console.log('This runs after 2 seconds');
}, 2000);

console.log('End');

// Output:
// "Start"
// "End"
// "This runs after 2 seconds" (after 2 seconds)

// Event listener callback (browser)
document.querySelector('#btn').addEventListener('click', function() {
    console.log('Button clicked!');
});

// Array methods with callbacks
const numbers = [1, 2, 3, 4, 5];

// forEach
numbers.forEach(function(num, index) {
    console.log(`Index ${index}: ${num}`);
});

// map
const doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

// filter
const evens = numbers.filter(num => num % 2 === 0);
console.log(evens); // [2, 4]

// find
const found = numbers.find(num => num > 3);
console.log(found); // 4

// reduce
const sum = numbers.reduce((total, num) => total + num, 0);
console.log(sum); // 15
```

### Callback Hell (Pyramid of Doom)

```javascript
// Example of callback hell - avoid this pattern
getData(function(a) {
    getMoreData(a, function(b) {
        getEvenMoreData(b, function(c) {
            getYetMoreData(c, function(d) {
                getFinalData(d, function(e) {
                    console.log('Finally done!');
                });
            });
        });
    });
});

// Better approach: Use Promises or async/await (covered in later topics)
```

---

## Closures

A closure is a function that has access to variables in its outer (enclosing) function's scope, even after the outer function has returned.

### Understanding Closures

```javascript
// Basic closure
function outer() {
    let count = 0;

    function inner() {
        count++;
        console.log(count);
    }

    return inner;
}

const counter = outer();
counter(); // 1
counter(); // 2
counter(); // 3

// 'inner' has access to 'count' even after 'outer' has finished executing
```

### Practical Closure Examples

**1. Data Privacy:**
```javascript
function createBankAccount(initialBalance) {
    let balance = initialBalance; // Private variable

    return {
        deposit: function(amount) {
            balance += amount;
            return balance;
        },
        withdraw: function(amount) {
            if (amount > balance) {
                return 'Insufficient funds';
            }
            balance -= amount;
            return balance;
        },
        getBalance: function() {
            return balance;
        }
    };
}

const myAccount = createBankAccount(1000);
console.log(myAccount.getBalance()); // 1000
myAccount.deposit(500);              // 1500
myAccount.withdraw(200);             // 1300
console.log(myAccount.balance);      // undefined (private)
```

**2. Function Factory:**
```javascript
function createMultiplier(multiplier) {
    return function(number) {
        return number * multiplier;
    };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15
```

**3. Event Handlers:**
```javascript
function setupButton(buttonId, message) {
    const button = document.getElementById(buttonId);

    button.addEventListener('click', function() {
        console.log(message); // Closure over 'message'
    });
}

setupButton('btn1', 'Button 1 clicked');
setupButton('btn2', 'Button 2 clicked');
```

**4. Loop with Closure (Common Pitfall):**
```javascript
// Problem: var is function-scoped
for (var i = 1; i <= 3; i++) {
    setTimeout(function() {
        console.log(i); // 4, 4, 4 (not what we want)
    }, i * 1000);
}

// Solution 1: Use let (block-scoped)
for (let i = 1; i <= 3; i++) {
    setTimeout(function() {
        console.log(i); // 1, 2, 3 (correct)
    }, i * 1000);
}

// Solution 2: Use IIFE to create closure
for (var i = 1; i <= 3; i++) {
    (function(i) {
        setTimeout(function() {
            console.log(i); // 1, 2, 3 (correct)
        }, i * 1000);
    })(i);
}
```

---

## IIFE (Immediately Invoked Function Expression)

An IIFE is a function that runs as soon as it's defined. It's useful for creating private scope and avoiding global namespace pollution.

### Basic Syntax

```javascript
// IIFE with function expression
(function() {
    console.log('This runs immediately!');
})();

// Alternative syntax
(function() {
    console.log('This also runs immediately!');
}());

// IIFE with arrow function
(() => {
    console.log('Arrow IIFE!');
})();

// IIFE with parameters
(function(name) {
    console.log(`Hello, ${name}!`);
})('John');

// IIFE with return value
const result = (function() {
    return 42;
})();

console.log(result); // 42
```

### Practical Use Cases

**1. Module Pattern:**
```javascript
const calculator = (function() {
    // Private variables and functions
    let result = 0;

    function add(x) {
        result += x;
    }

    function subtract(x) {
        result -= x;
    }

    // Public API
    return {
        add: function(x) {
            add(x);
            return this;
        },
        subtract: function(x) {
            subtract(x);
            return this;
        },
        getResult: function() {
            return result;
        },
        reset: function() {
            result = 0;
            return this;
        }
    };
})();

calculator.add(10).subtract(3).add(5);
console.log(calculator.getResult()); // 12
calculator.reset();
```

**2. Avoiding Global Namespace Pollution:**
```javascript
// Without IIFE - pollutes global scope
var name = 'John';
var age = 30;

function greet() {
    console.log(`Hello, ${name}`);
}

// With IIFE - contained scope
(function() {
    var name = 'John';
    var age = 30;

    function greet() {
        console.log(`Hello, ${name}`);
    }

    greet();
})();

// name, age, and greet are not accessible here
```

**3. Creating Private State:**
```javascript
const counter = (function() {
    let count = 0;

    return {
        increment() {
            return ++count;
        },
        decrement() {
            return --count;
        },
        getValue() {
            return count;
        }
    };
})();

console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getValue());  // 2
console.log(counter.count);       // undefined (private)
```

---

## Scope

Scope determines the accessibility of variables, functions, and objects in different parts of your code.

### Global Scope

```javascript
// Global variables
let globalVar = 'I am global';

function showGlobal() {
    console.log(globalVar); // Accessible
}

showGlobal(); // "I am global"
console.log(globalVar); // "I am global"
```

### Function Scope

```javascript
function myFunction() {
    let localVar = 'I am local';
    console.log(localVar); // Accessible
}

myFunction();
// console.log(localVar); // Error: localVar is not defined

// var is function-scoped
function testVar() {
    if (true) {
        var x = 10;
    }
    console.log(x); // 10 (accessible throughout function)
}
```

### Block Scope (ES6)

```javascript
// let and const are block-scoped
if (true) {
    let blockVar = 'I am block-scoped';
    const blockConst = 'Me too';
    var functionVar = 'I am function-scoped';

    console.log(blockVar);    // Accessible
    console.log(blockConst);  // Accessible
}

// console.log(blockVar);     // Error: not defined
// console.log(blockConst);   // Error: not defined
console.log(functionVar);     // Accessible (var is function-scoped)

// Block scope in loops
for (let i = 0; i < 3; i++) {
    // i is only accessible within this block
    console.log(i);
}
// console.log(i); // Error: i is not defined

for (var j = 0; j < 3; j++) {
    // j is function-scoped
}
console.log(j); // 3 (accessible)
```

### Lexical Scope

```javascript
// Lexical (static) scoping
const global = 'global';

function outer() {
    const outerVar = 'outer';

    function inner() {
        const innerVar = 'inner';

        // Can access variables from outer scopes
        console.log(innerVar);  // 'inner'
        console.log(outerVar);  // 'outer'
        console.log(global);    // 'global'
    }

    inner();
    // console.log(innerVar); // Error: not accessible
}

outer();
```

### Scope Chain

```javascript
let a = 'global a';

function level1() {
    let b = 'level1 b';

    function level2() {
        let c = 'level2 c';

        function level3() {
            let d = 'level3 d';

            // Can access all variables in the scope chain
            console.log(d); // 'level3 d'
            console.log(c); // 'level2 c'
            console.log(b); // 'level1 b'
            console.log(a); // 'global a'
        }

        level3();
    }

    level2();
}

level1();
```

### Temporal Dead Zone (TDZ)

```javascript
// let and const are hoisted but not initialized
console.log(x); // Error: Cannot access 'x' before initialization
let x = 10;

// var is hoisted and initialized with undefined
console.log(y); // undefined
var y = 20;

// TDZ in function parameters
function testTDZ(a = b, b = 2) {
    // Error: Cannot access 'b' before initialization
}
```

---

## Best Practices

### Function Design

1. **Single Responsibility**: Each function should do one thing well
2. **Descriptive Names**: Use clear, action-oriented names
3. **Keep Functions Small**: Easier to understand and test
4. **Avoid Side Effects**: Functions should be predictable
5. **Return Early**: Use early returns for error conditions

```javascript
// Good - single responsibility
function calculateTax(amount) {
    return amount * 0.1;
}

function formatCurrency(amount) {
    return `$${amount.toFixed(2)}`;
}

// Avoid - doing too much
function processPayment(amount) {
    // Calculating, formatting, and saving in one function
    const tax = amount * 0.1;
    const total = amount + tax;
    const formatted = `$${total.toFixed(2)}`;
    saveToDatabase(formatted);
    return formatted;
}
```

### Arrow Function Usage

1. Use arrow functions for **short callbacks**
2. Use regular functions for **object methods**
3. Use arrow functions when you need **lexical this**
4. Avoid arrow functions when you need **arguments object**

```javascript
// Good
const numbers = [1, 2, 3];
const doubled = numbers.map(n => n * 2);

// Avoid
const obj = {
    value: 42,
    getValue: () => this.value // 'this' is not the object
};
```

### Scope Management

1. **Minimize global variables**
2. **Use const by default**, let when reassignment needed
3. **Declare variables at the top** of their scope
4. **Use modules** to organize code
5. **Leverage closures** for data privacy

```javascript
// Good - limited scope
function processData() {
    const data = fetchData();
    const processed = transform(data);
    return processed;
}

// Avoid - global pollution
let data;
let processed;

function processData() {
    data = fetchData();
    processed = transform(data);
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Function Declaration | Hoisted, can be called before definition |
| Function Expression | Not hoisted, assigned to variable |
| Arrow Functions | Concise syntax, lexical this, no arguments object |
| Callbacks | Functions passed as arguments, foundation for async |
| Closures | Functions that remember outer scope variables |
| IIFE | Immediately invoked, creates private scope |
| Scope | Global, function, block; determines variable accessibility |

## Next Topic

Continue to [DOM Manipulation](./03-dom-manipulation.md) to learn how to interact with and manipulate HTML elements using JavaScript.
