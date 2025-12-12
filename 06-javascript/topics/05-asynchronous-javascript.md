# Asynchronous JavaScript

## Understanding Asynchronous Programming

Asynchronous programming allows JavaScript to perform long-running operations without blocking the main thread, enabling responsive applications.

### Synchronous vs Asynchronous

**Synchronous (Blocking):**
```javascript
// Synchronous code executes line by line
console.log('Start');

// This blocks execution until complete
function slowTask() {
    const start = Date.now();
    while (Date.now() - start < 3000) {
        // Block for 3 seconds
    }
    return 'Task complete';
}

console.log(slowTask()); // Blocks for 3 seconds
console.log('End');

// Output:
// "Start"
// (waits 3 seconds)
// "Task complete"
// "End"
```

**Asynchronous (Non-blocking):**
```javascript
// Asynchronous code doesn't block
console.log('Start');

setTimeout(() => {
    console.log('Task complete');
}, 3000);

console.log('End');

// Output:
// "Start"
// "End"
// (waits 3 seconds)
// "Task complete"
```

### Why Asynchronous?

- **Responsiveness**: UI remains interactive during long operations
- **Efficiency**: Don't wait for slow operations (network, file I/O)
- **Concurrency**: Handle multiple operations simultaneously
- **Better UX**: Prevent browser freezing

### Common Asynchronous Operations

| Operation | Example |
|-----------|---------|
| Network requests | Fetch API, AJAX |
| Timers | setTimeout, setInterval |
| File operations | Reading/writing files (Node.js) |
| Database queries | MongoDB, SQL queries |
| User input | Event listeners |
| Animations | requestAnimationFrame |

---

## JavaScript Runtime Model

### The Call Stack

The call stack tracks function execution in JavaScript.

```javascript
function first() {
    console.log('First');
    second();
    console.log('First again');
}

function second() {
    console.log('Second');
    third();
    console.log('Second again');
}

function third() {
    console.log('Third');
}

first();

// Call stack visualization:
// 1. first() pushed
// 2. second() pushed
// 3. third() pushed
// 4. third() executed and popped
// 5. second() executed and popped
// 6. first() executed and popped

// Output:
// "First"
// "Second"
// "Third"
// "Second again"
// "First again"
```

**Stack Overflow:**
```javascript
// Infinite recursion causes stack overflow
function recursiveFunction() {
    recursiveFunction();
}

// recursiveFunction(); // Error: Maximum call stack size exceeded
```

### Web APIs

Browser provides APIs for asynchronous operations:
- DOM APIs (addEventListener)
- setTimeout / setInterval
- Fetch API
- XMLHttpRequest
- Geolocation
- LocalStorage

```javascript
// Web API example
console.log('Start');

// setTimeout is a Web API, not part of JavaScript engine
setTimeout(() => {
    console.log('Timeout callback');
}, 0);

console.log('End');

// Output:
// "Start"
// "End"
// "Timeout callback"
```

### The Callback Queue (Task Queue)

Holds callbacks waiting to be executed.

```javascript
console.log('1');

setTimeout(() => console.log('2'), 0);
setTimeout(() => console.log('3'), 0);

console.log('4');

// Call stack must be empty before callbacks execute
// Output: "1", "4", "2", "3"
```

### The Event Loop

The event loop continuously checks if the call stack is empty and moves callbacks from the queue to the stack.

```
   ┌───────────────────────────┐
┌─>│      Call Stack           │
│  └───────────────────────────┘
│               │
│               ↓
│  ┌───────────────────────────┐
│  │      Web APIs             │
│  │  (setTimeout, fetch, etc) │
│  └───────────────────────────┘
│               │
│               ↓
│  ┌───────────────────────────┐
│  │    Callback Queue          │
│  │    (Task Queue)            │
│  └───────────────────────────┘
│               │
└───────────────┘
    Event Loop
```

**Example:**
```javascript
console.log('Start');

setTimeout(() => {
    console.log('Timeout 1');
}, 0);

Promise.resolve().then(() => {
    console.log('Promise 1');
});

setTimeout(() => {
    console.log('Timeout 2');
}, 0);

Promise.resolve().then(() => {
    console.log('Promise 2');
});

console.log('End');

// Output:
// "Start"
// "End"
// "Promise 1"
// "Promise 2"
// "Timeout 1"
// "Timeout 2"

// Promises use Microtask Queue (higher priority than Callback Queue)
```

### Microtask Queue

Promises and mutation observers use the microtask queue, which has higher priority than the callback queue.

```javascript
// Microtasks (Promises) execute before tasks (setTimeout)
setTimeout(() => console.log('setTimeout'), 0);

Promise.resolve()
    .then(() => console.log('Promise 1'))
    .then(() => console.log('Promise 2'));

console.log('Synchronous');

// Output:
// "Synchronous"
// "Promise 1"
// "Promise 2"
// "setTimeout"
```

---

## Callbacks

Callbacks are functions passed as arguments to be executed later.

### Basic Callbacks

```javascript
// Simple callback
function greet(name, callback) {
    console.log(`Hello, ${name}`);
    callback();
}

function sayGoodbye() {
    console.log('Goodbye!');
}

greet('John', sayGoodbye);
// "Hello, John"
// "Goodbye!"

// Inline callback
greet('Jane', function() {
    console.log('See you later!');
});

// Arrow function callback
greet('Bob', () => console.log('Bye!'));
```

### Asynchronous Callbacks

```javascript
// setTimeout callback
console.log('Start');

setTimeout(function() {
    console.log('This runs after 2 seconds');
}, 2000);

console.log('End');

// Array methods with callbacks
const numbers = [1, 2, 3, 4, 5];

numbers.forEach((num) => {
    console.log(num);
});

const doubled = numbers.map((num) => num * 2);
const evens = numbers.filter((num) => num % 2 === 0);
```

### Callback Hell

Multiple nested callbacks become difficult to read and maintain.

```javascript
// Callback hell example (avoid this!)
setTimeout(() => {
    console.log('Step 1');
    setTimeout(() => {
        console.log('Step 2');
        setTimeout(() => {
            console.log('Step 3');
            setTimeout(() => {
                console.log('Step 4');
                setTimeout(() => {
                    console.log('Step 5');
                }, 1000);
            }, 1000);
        }, 1000);
    }, 1000);
}, 1000);

// Hard to read, hard to maintain, hard to handle errors
```

### Error Handling with Callbacks

```javascript
// Error-first callback pattern (Node.js convention)
function readFile(filename, callback) {
    setTimeout(() => {
        const error = Math.random() > 0.5 ? new Error('File not found') : null;
        const data = error ? null : 'File contents';

        callback(error, data);
    }, 1000);
}

readFile('data.txt', (error, data) => {
    if (error) {
        console.error('Error:', error.message);
        return;
    }
    console.log('Data:', data);
});
```

---

## Promises

Promises represent the eventual completion (or failure) of an asynchronous operation.

### Promise States

| State | Description |
|-------|-------------|
| Pending | Initial state, neither fulfilled nor rejected |
| Fulfilled | Operation completed successfully |
| Rejected | Operation failed |

### Creating Promises

```javascript
// Basic promise
const promise = new Promise((resolve, reject) => {
    // Asynchronous operation
    const success = true;

    setTimeout(() => {
        if (success) {
            resolve('Operation successful!');
        } else {
            reject('Operation failed!');
        }
    }, 1000);
});

console.log(promise); // Promise { <pending> }

// Promise that resolves immediately
const resolvedPromise = Promise.resolve('Resolved!');
const rejectedPromise = Promise.reject('Rejected!');
```

### Consuming Promises

```javascript
const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('Success!');
    }, 1000);
});

// .then() for fulfillment
promise.then((result) => {
    console.log(result); // "Success!"
});

// .catch() for rejection
promise.catch((error) => {
    console.error(error);
});

// .finally() - always executes
promise.finally(() => {
    console.log('Promise settled (either resolved or rejected)');
});

// Chaining
promise
    .then((result) => {
        console.log(result);
        return 'Next step';
    })
    .then((result) => {
        console.log(result);
        return 'Final step';
    })
    .then((result) => {
        console.log(result);
    })
    .catch((error) => {
        console.error('Error:', error);
    })
    .finally(() => {
        console.log('Done');
    });
```

### Promise Chaining

```javascript
// Sequential async operations
function step1() {
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log('Step 1 complete');
            resolve(1);
        }, 1000);
    });
}

function step2(value) {
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log('Step 2 complete');
            resolve(value + 1);
        }, 1000);
    });
}

function step3(value) {
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log('Step 3 complete');
            resolve(value + 1);
        }, 1000);
    });
}

// Clean chain
step1()
    .then(result => step2(result))
    .then(result => step3(result))
    .then(result => {
        console.log('Final result:', result); // 3
    })
    .catch(error => {
        console.error('Error:', error);
    });

// Simplified
step1()
    .then(step2)
    .then(step3)
    .then(result => console.log('Final result:', result))
    .catch(error => console.error('Error:', error));
```

### Error Handling

```javascript
// Error propagation
Promise.resolve()
    .then(() => {
        console.log('Step 1');
        return 'Success';
    })
    .then((result) => {
        console.log('Step 2');
        throw new Error('Something went wrong!');
    })
    .then(() => {
        console.log('Step 3'); // Skipped due to error
    })
    .catch((error) => {
        console.error('Caught:', error.message);
        return 'Recovered';
    })
    .then((result) => {
        console.log('Step 4:', result); // Continues after recovery
    });

// Multiple catch blocks
Promise.reject('Initial error')
    .catch(error => {
        console.error('First catch:', error);
        throw new Error('New error');
    })
    .catch(error => {
        console.error('Second catch:', error.message);
    });
```

### Promise Static Methods

**Promise.all()** - Wait for all promises to resolve:
```javascript
const promise1 = Promise.resolve(1);
const promise2 = Promise.resolve(2);
const promise3 = Promise.resolve(3);

Promise.all([promise1, promise2, promise3])
    .then((results) => {
        console.log(results); // [1, 2, 3]
    });

// If any promise rejects, Promise.all rejects
const failing = Promise.all([
    Promise.resolve(1),
    Promise.reject('Error'),
    Promise.resolve(3)
]);

failing.catch(error => console.error(error)); // "Error"
```

**Promise.allSettled()** - Wait for all promises to settle:
```javascript
const promises = [
    Promise.resolve(1),
    Promise.reject('Error'),
    Promise.resolve(3)
];

Promise.allSettled(promises)
    .then((results) => {
        console.log(results);
        // [
        //   { status: 'fulfilled', value: 1 },
        //   { status: 'rejected', reason: 'Error' },
        //   { status: 'fulfilled', value: 3 }
        // ]
    });
```

**Promise.race()** - First promise to settle wins:
```javascript
const slow = new Promise(resolve => setTimeout(() => resolve('Slow'), 2000));
const fast = new Promise(resolve => setTimeout(() => resolve('Fast'), 1000));

Promise.race([slow, fast])
    .then(result => console.log(result)); // "Fast"
```

**Promise.any()** - First promise to fulfill:
```javascript
const promises = [
    Promise.reject('Error 1'),
    Promise.resolve('Success'),
    Promise.reject('Error 2')
];

Promise.any(promises)
    .then(result => console.log(result)); // "Success"

// If all reject, throws AggregateError
Promise.any([
    Promise.reject('Error 1'),
    Promise.reject('Error 2')
])
.catch(error => console.error(error)); // AggregateError
```

---

## Async/Await

Async/await is syntactic sugar over promises, making asynchronous code look synchronous.

### Async Functions

```javascript
// Regular function
function regularFunction() {
    return 'Hello';
}

// Async function - always returns a Promise
async function asyncFunction() {
    return 'Hello'; // Automatically wrapped in Promise.resolve()
}

console.log(asyncFunction()); // Promise { 'Hello' }

asyncFunction().then(result => console.log(result)); // "Hello"

// Explicit promise return
async function explicitPromise() {
    return Promise.resolve('Hello');
}
```

### Await Keyword

```javascript
// Can only use await inside async functions
async function fetchData() {
    // Wait for promise to resolve
    const result = await Promise.resolve('Data fetched');
    console.log(result); // "Data fetched"
    return result;
}

fetchData();

// Multiple awaits (sequential)
async function sequentialOperations() {
    console.log('Start');

    const result1 = await Promise.resolve('Step 1');
    console.log(result1);

    const result2 = await Promise.resolve('Step 2');
    console.log(result2);

    const result3 = await Promise.resolve('Step 3');
    console.log(result3);

    console.log('End');
}

// Parallel operations
async function parallelOperations() {
    // Start all promises simultaneously
    const promise1 = Promise.resolve('Step 1');
    const promise2 = Promise.resolve('Step 2');
    const promise3 = Promise.resolve('Step 3');

    // Wait for all to complete
    const results = await Promise.all([promise1, promise2, promise3]);
    console.log(results); // ['Step 1', 'Step 2', 'Step 3']
}
```

### Error Handling with Try-Catch

```javascript
// Try-catch with async/await
async function fetchData() {
    try {
        const response = await fetch('https://api.example.com/data');
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Error fetching data:', error);
        throw error; // Re-throw if needed
    } finally {
        console.log('Fetch attempt completed');
    }
}

// Multiple try-catch blocks
async function multipleOperations() {
    let data1, data2;

    try {
        data1 = await fetchFromAPI1();
    } catch (error) {
        console.error('API 1 failed:', error);
        data1 = null;
    }

    try {
        data2 = await fetchFromAPI2();
    } catch (error) {
        console.error('API 2 failed:', error);
        data2 = null;
    }

    return { data1, data2 };
}

// Handle errors at call site
async function getData() {
    throw new Error('Something went wrong');
}

getData()
    .then(result => console.log(result))
    .catch(error => console.error('Caught:', error.message));
```

### Async/Await Patterns

**Sequential Execution:**
```javascript
async function sequential() {
    const start = Date.now();

    const result1 = await delay(1000, 'First');  // Waits 1s
    const result2 = await delay(1000, 'Second'); // Waits 1s
    const result3 = await delay(1000, 'Third');  // Waits 1s

    console.log('Total time:', Date.now() - start); // ~3000ms

    return [result1, result2, result3];
}

function delay(ms, value) {
    return new Promise(resolve => setTimeout(() => resolve(value), ms));
}
```

**Parallel Execution:**
```javascript
async function parallel() {
    const start = Date.now();

    // Start all operations simultaneously
    const results = await Promise.all([
        delay(1000, 'First'),
        delay(1000, 'Second'),
        delay(1000, 'Third')
    ]);

    console.log('Total time:', Date.now() - start); // ~1000ms

    return results;
}
```

**Conditional Await:**
```javascript
async function conditionalAwait(useCache) {
    if (useCache) {
        return getCachedData();
    }

    const data = await fetchFromAPI();
    cacheData(data);
    return data;
}
```

**Top-level Await (ES2022):**
```javascript
// In modules only
const data = await fetch('https://api.example.com/data');
const json = await data.json();

console.log(json);
```

---

## Fetch API

The Fetch API provides a modern interface for making HTTP requests.

### Basic Fetch

```javascript
// GET request
fetch('https://api.example.com/users')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error('Error:', error));

// With async/await
async function getUsers() {
    try {
        const response = await fetch('https://api.example.com/users');
        const data = await response.json();
        console.log(data);
    } catch (error) {
        console.error('Error:', error);
    }
}
```

### Response Object

```javascript
async function checkResponse() {
    const response = await fetch('https://api.example.com/data');

    console.log(response.status);       // 200
    console.log(response.statusText);   // "OK"
    console.log(response.ok);           // true if status 200-299
    console.log(response.headers);      // Headers object
    console.log(response.url);          // Final URL after redirects

    // Check status before parsing
    if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
    }

    const data = await response.json();
    return data;
}
```

### Request Methods

**POST Request:**
```javascript
async function createUser(userData) {
    try {
        const response = await fetch('https://api.example.com/users', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(userData)
        });

        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Error creating user:', error);
        throw error;
    }
}

createUser({
    name: 'John Doe',
    email: 'john@example.com'
});
```

**PUT Request:**
```javascript
async function updateUser(id, userData) {
    const response = await fetch(`https://api.example.com/users/${id}`, {
        method: 'PUT',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(userData)
    });

    return response.json();
}
```

**DELETE Request:**
```javascript
async function deleteUser(id) {
    const response = await fetch(`https://api.example.com/users/${id}`, {
        method: 'DELETE'
    });

    if (!response.ok) {
        throw new Error('Failed to delete user');
    }

    return response.json();
}
```

### Headers

```javascript
// Custom headers
const response = await fetch('https://api.example.com/data', {
    headers: {
        'Authorization': 'Bearer token123',
        'Content-Type': 'application/json',
        'Custom-Header': 'value'
    }
});

// Headers object
const headers = new Headers();
headers.append('Authorization', 'Bearer token123');
headers.append('Content-Type', 'application/json');

const response2 = await fetch('https://api.example.com/data', {
    headers: headers
});

// Read response headers
const contentType = response.headers.get('Content-Type');
```

### Request Options

```javascript
const options = {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer token123'
    },
    body: JSON.stringify({ name: 'John' }),
    mode: 'cors',              // no-cors, cors, same-origin
    credentials: 'include',    // omit, same-origin, include
    cache: 'no-cache',         // default, no-cache, reload, force-cache
    redirect: 'follow',        // manual, follow, error
    referrerPolicy: 'no-referrer'
};

const response = await fetch('https://api.example.com/data', options);
```

### Response Types

```javascript
const response = await fetch('https://api.example.com/data');

// JSON
const json = await response.json();

// Text
const text = await response.text();

// Blob (images, files)
const blob = await response.blob();

// ArrayBuffer
const buffer = await response.arrayBuffer();

// FormData
const formData = await response.formData();
```

### Error Handling

```javascript
async function robustFetch(url) {
    try {
        const response = await fetch(url);

        // Fetch doesn't reject on HTTP errors (404, 500, etc.)
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();
        return data;
    } catch (error) {
        // Network error or JSON parse error
        console.error('Fetch error:', error);
        throw error;
    }
}
```

### Practical Example

```javascript
// Complete API interaction example
class UserAPI {
    constructor(baseURL) {
        this.baseURL = baseURL;
    }

    async request(endpoint, options = {}) {
        const url = `${this.baseURL}${endpoint}`;

        const config = {
            headers: {
                'Content-Type': 'application/json',
                ...options.headers
            },
            ...options
        };

        try {
            const response = await fetch(url, config);

            if (!response.ok) {
                const error = await response.json();
                throw new Error(error.message || 'Request failed');
            }

            return await response.json();
        } catch (error) {
            console.error('API Error:', error);
            throw error;
        }
    }

    async getUsers() {
        return this.request('/users');
    }

    async getUser(id) {
        return this.request(`/users/${id}`);
    }

    async createUser(userData) {
        return this.request('/users', {
            method: 'POST',
            body: JSON.stringify(userData)
        });
    }

    async updateUser(id, userData) {
        return this.request(`/users/${id}`, {
            method: 'PUT',
            body: JSON.stringify(userData)
        });
    }

    async deleteUser(id) {
        return this.request(`/users/${id}`, {
            method: 'DELETE'
        });
    }
}

// Usage
const api = new UserAPI('https://api.example.com');

async function main() {
    try {
        const users = await api.getUsers();
        console.log('Users:', users);

        const newUser = await api.createUser({
            name: 'John Doe',
            email: 'john@example.com'
        });
        console.log('Created:', newUser);
    } catch (error) {
        console.error('Error:', error);
    }
}
```

---

## Best Practices

### Async/Await

1. **Always use try-catch** for error handling
2. **Prefer async/await** over raw promises for readability
3. **Use Promise.all()** for parallel operations
4. **Don't await unnecessarily** in loops

```javascript
// Bad - sequential when parallel is possible
async function bad() {
    const result1 = await fetchData1();
    const result2 = await fetchData2();
    const result3 = await fetchData3();
}

// Good - parallel execution
async function good() {
    const [result1, result2, result3] = await Promise.all([
        fetchData1(),
        fetchData2(),
        fetchData3()
    ]);
}
```

### Error Handling

1. **Handle errors at appropriate level**
2. **Provide meaningful error messages**
3. **Don't swallow errors**
4. **Use finally for cleanup**

```javascript
// Good
async function fetchUserData(userId) {
    try {
        const response = await fetch(`/api/users/${userId}`);

        if (!response.ok) {
            throw new Error(`User ${userId} not found`);
        }

        return await response.json();
    } catch (error) {
        console.error('Failed to fetch user:', error);
        throw error; // Re-throw for caller to handle
    } finally {
        console.log('Fetch attempt completed');
    }
}
```

### Fetch API

1. **Always check response.ok**
2. **Set appropriate headers**
3. **Handle network errors**
4. **Use abort controller for cancellation**

```javascript
// AbortController for request cancellation
const controller = new AbortController();
const signal = controller.signal;

fetch('https://api.example.com/data', { signal })
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => {
        if (error.name === 'AbortError') {
            console.log('Fetch aborted');
        }
    });

// Cancel request
controller.abort();
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Sync vs Async | Blocking vs non-blocking execution |
| Event Loop | Manages execution of async callbacks |
| Callbacks | Functions passed as arguments, can lead to callback hell |
| Promises | Represent future values, support chaining |
| Async/Await | Syntactic sugar for promises, cleaner syntax |
| Fetch API | Modern HTTP client, returns promises |

## Next Topic

Continue to [ES6 Features](./06-es6-features.md) to learn about modern JavaScript features including let/const, arrow functions, template literals, destructuring, spread/rest operators, and modules.
