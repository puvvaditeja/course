# Objects and JSON

## JavaScript Objects

Objects are collections of key-value pairs that allow you to store and organize related data and functionality.

### What are Objects?

Objects are one of JavaScript's fundamental data types. They can contain properties (data) and methods (functions).

```javascript
// Simple object
const person = {
    name: 'John',
    age: 30,
    city: 'New York'
};

console.log(person); // { name: 'John', age: 30, city: 'New York' }
```

### Why Use Objects?

- **Organization**: Group related data together
- **Structure**: Represent real-world entities
- **Flexibility**: Dynamic property addition/removal
- **Methods**: Combine data with behavior
- **Reusability**: Create multiple instances with similar structure

---

## Creating Objects

### Object Literal

The most common and straightforward way to create objects.

```javascript
// Empty object
const emptyObj = {};

// Object with properties
const person = {
    firstName: 'John',
    lastName: 'Doe',
    age: 30,
    isEmployed: true,
    hobbies: ['reading', 'coding', 'gaming']
};

// Nested objects
const user = {
    name: 'John Doe',
    contact: {
        email: 'john@example.com',
        phone: '555-1234',
        address: {
            street: '123 Main St',
            city: 'New York',
            zipCode: '10001'
        }
    }
};

// Methods in objects
const calculator = {
    value: 0,
    add: function(num) {
        this.value += num;
        return this;
    },
    subtract: function(num) {
        this.value -= num;
        return this;
    },
    // ES6 method shorthand
    multiply(num) {
        this.value *= num;
        return this;
    }
};
```

### Object Constructor

```javascript
// Using Object constructor
const person = new Object();
person.name = 'John';
person.age = 30;
person.city = 'New York';

console.log(person); // { name: 'John', age: 30, city: 'New York' }
```

### Constructor Function

```javascript
// Constructor function (use PascalCase)
function Person(firstName, lastName, age) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
    this.getFullName = function() {
        return `${this.firstName} ${this.lastName}`;
    };
}

// Create instances
const person1 = new Person('John', 'Doe', 30);
const person2 = new Person('Jane', 'Smith', 25);

console.log(person1.getFullName()); // "John Doe"
console.log(person2.getFullName()); // "Jane Smith"
```

### Object.create()

```javascript
// Create object with specific prototype
const personProto = {
    greet: function() {
        return `Hello, I'm ${this.name}`;
    }
};

const person = Object.create(personProto);
person.name = 'John';
person.age = 30;

console.log(person.greet()); // "Hello, I'm John"

// Create object with null prototype (no inherited properties)
const pureObject = Object.create(null);
pureObject.name = 'John';
console.log(pureObject.toString); // undefined (no inherited methods)
```

### Class Syntax (ES6)

```javascript
// Modern class syntax
class Person {
    constructor(firstName, lastName, age) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }

    getFullName() {
        return `${this.firstName} ${this.lastName}`;
    }

    greet() {
        return `Hello, I'm ${this.getFullName()}`;
    }

    // Getter
    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    }

    // Setter
    set fullName(name) {
        const parts = name.split(' ');
        this.firstName = parts[0];
        this.lastName = parts[1];
    }

    // Static method
    static species() {
        return 'Homo sapiens';
    }
}

const person = new Person('John', 'Doe', 30);
console.log(person.getFullName()); // "John Doe"
console.log(person.fullName);      // "John Doe" (getter)
person.fullName = 'Jane Smith';    // (setter)
console.log(Person.species());     // "Homo sapiens"
```

---

## Object Properties

### Accessing Properties

```javascript
const person = {
    firstName: 'John',
    lastName: 'Doe',
    age: 30,
    'full name': 'John Doe', // Property with space
    123: 'numeric key'
};

// Dot notation (most common)
console.log(person.firstName); // "John"
console.log(person.age);       // 30

// Bracket notation (required for special characters or variables)
console.log(person['lastName']);    // "Doe"
console.log(person['full name']);   // "John Doe"
console.log(person[123]);           // "numeric key"

// Using variables
const prop = 'firstName';
console.log(person[prop]); // "John"

// Accessing nested properties
const user = {
    name: 'John',
    address: {
        city: 'New York',
        zipCode: '10001'
    }
};

console.log(user.address.city);        // "New York"
console.log(user['address']['city']);  // "New York"

// Optional chaining (ES2020)
console.log(user?.address?.city);      // "New York"
console.log(user?.contact?.email);     // undefined (no error)
```

### Adding Properties

```javascript
const person = {
    name: 'John'
};

// Dot notation
person.age = 30;
person.city = 'New York';

// Bracket notation
person['email'] = 'john@example.com';
person['is-employed'] = true; // Hyphens require brackets

console.log(person);
// { name: 'John', age: 30, city: 'New York',
//   email: 'john@example.com', 'is-employed': true }

// Computed property names (ES6)
const propName = 'dynamicProp';
const obj = {
    [propName]: 'value',
    ['computed' + 'Key']: 'another value'
};
console.log(obj.dynamicProp);    // "value"
console.log(obj.computedKey);    // "another value"
```

### Modifying Properties

```javascript
const person = {
    name: 'John',
    age: 30
};

// Update existing property
person.name = 'Jane';
person['age'] = 25;

console.log(person); // { name: 'Jane', age: 25 }
```

### Deleting Properties

```javascript
const person = {
    name: 'John',
    age: 30,
    city: 'New York'
};

// Delete property
delete person.age;

console.log(person); // { name: 'John', city: 'New York' }
console.log(person.age); // undefined

// Delete doesn't work on prototype properties
delete person.toString; // false (can't delete inherited properties)
```

### Checking Properties

```javascript
const person = {
    name: 'John',
    age: 30
};

// Check if property exists
console.log('name' in person);     // true
console.log('email' in person);    // false
console.log('toString' in person); // true (inherited)

// Check own property (not inherited)
console.log(person.hasOwnProperty('name'));     // true
console.log(person.hasOwnProperty('toString')); // false

// Check if property is undefined
console.log(person.email === undefined); // true
console.log(person.name !== undefined);  // true
```

### Property Descriptors

```javascript
const person = {
    name: 'John'
};

// Get property descriptor
const descriptor = Object.getOwnPropertyDescriptor(person, 'name');
console.log(descriptor);
// {
//   value: 'John',
//   writable: true,
//   enumerable: true,
//   configurable: true
// }

// Define property with descriptor
Object.defineProperty(person, 'age', {
    value: 30,
    writable: false,      // Cannot be changed
    enumerable: true,     // Shows in for...in loop
    configurable: false   // Cannot be deleted or reconfigured
});

person.age = 40; // Silently fails (strict mode throws error)
console.log(person.age); // 30

// Define multiple properties
Object.defineProperties(person, {
    firstName: {
        value: 'John',
        writable: true
    },
    lastName: {
        value: 'Doe',
        writable: true
    }
});
```

### Enumerating Properties

```javascript
const person = {
    name: 'John',
    age: 30,
    city: 'New York'
};

// for...in loop (includes inherited enumerable properties)
for (let key in person) {
    console.log(`${key}: ${person[key]}`);
}

// Object.keys() - array of own enumerable property names
const keys = Object.keys(person);
console.log(keys); // ['name', 'age', 'city']

// Object.values() - array of own enumerable property values
const values = Object.values(person);
console.log(values); // ['John', 30, 'New York']

// Object.entries() - array of [key, value] pairs
const entries = Object.entries(person);
console.log(entries);
// [['name', 'John'], ['age', 30], ['city', 'New York']]

// Iterate with forEach
Object.entries(person).forEach(([key, value]) => {
    console.log(`${key}: ${value}`);
});

// Get all property names (including non-enumerable)
const allKeys = Object.getOwnPropertyNames(person);
```

---

## Object Methods

Methods are functions stored as object properties.

### Defining Methods

```javascript
// Traditional function syntax
const calculator = {
    value: 0,
    add: function(num) {
        this.value += num;
        return this;
    },
    subtract: function(num) {
        this.value -= num;
        return this;
    }
};

// ES6 method shorthand
const calculator2 = {
    value: 0,
    add(num) {
        this.value += num;
        return this;
    },
    subtract(num) {
        this.value -= num;
        return this;
    },
    // Arrow functions as methods (avoid - no 'this')
    multiply: (num) => {
        // this.value is undefined - arrow functions don't have 'this'
        return this;
    }
};

// Method chaining
calculator.add(10).subtract(3).add(5);
console.log(calculator.value); // 12
```

### Common Built-in Methods

```javascript
const person = {
    name: 'John',
    age: 30
};

// Object.assign() - copy properties
const copy = Object.assign({}, person);
const merged = Object.assign({}, person, { city: 'New York' });

// Object.freeze() - prevent modifications
Object.freeze(person);
person.age = 40;        // Silently fails
person.city = 'LA';     // Silently fails
delete person.name;     // Silently fails
console.log(person);    // { name: 'John', age: 30 }

// Object.seal() - prevent adding/removing properties
const sealed = { name: 'John' };
Object.seal(sealed);
sealed.name = 'Jane';   // OK
sealed.age = 30;        // Silently fails
delete sealed.name;     // Silently fails

// Object.isFrozen() / Object.isSealed()
console.log(Object.isFrozen(person));  // true
console.log(Object.isSealed(sealed));  // true

// Object.keys(), Object.values(), Object.entries()
console.log(Object.keys(person));      // ['name', 'age']
console.log(Object.values(person));    // ['John', 30]
console.log(Object.entries(person));   // [['name', 'John'], ['age', 30]]

// Object.fromEntries() - create object from entries
const entries = [['name', 'John'], ['age', 30]];
const obj = Object.fromEntries(entries);
console.log(obj); // { name: 'John', age: 30 }
```

---

## The 'this' Keyword

The `this` keyword refers to the object that is executing the current function.

### 'this' in Different Contexts

```javascript
// Global context
console.log(this); // Window (browser) or global (Node.js)

// Object method
const person = {
    name: 'John',
    greet: function() {
        console.log(`Hello, I'm ${this.name}`);
    }
};
person.greet(); // "Hello, I'm John"

// Regular function
function showThis() {
    console.log(this); // Window (browser) or undefined (strict mode)
}
showThis();

// Arrow function (lexical this)
const obj = {
    name: 'John',
    regularFunc: function() {
        console.log(this.name); // 'John'
    },
    arrowFunc: () => {
        console.log(this.name); // undefined (this is from outer scope)
    }
};

// Constructor function
function Person(name) {
    this.name = name; // 'this' refers to new instance
}
const person1 = new Person('John');

// Event handler
button.addEventListener('click', function() {
    console.log(this); // The button element
});

button.addEventListener('click', () => {
    console.log(this); // Window or outer scope (not the button)
});
```

### Explicit 'this' Binding

```javascript
const person = {
    name: 'John',
    age: 30
};

function greet(greeting, punctuation) {
    return `${greeting}, I'm ${this.name} and I'm ${this.age} years old${punctuation}`;
}

// call() - invoke function with specific 'this', individual arguments
console.log(greet.call(person, 'Hello', '!'));
// "Hello, I'm John and I'm 30 years old!"

// apply() - invoke function with specific 'this', array of arguments
console.log(greet.apply(person, ['Hi', '.']));
// "Hi, I'm John and I'm 30 years old."

// bind() - create new function with specific 'this'
const boundGreet = greet.bind(person);
console.log(boundGreet('Hey', '?'));
// "Hey, I'm John and I'm 30 years old?"

// bind() with preset arguments
const boundGreetHello = greet.bind(person, 'Hello');
console.log(boundGreetHello('!'));
// "Hello, I'm John and I'm 30 years old!"
```

### Common 'this' Pitfalls

```javascript
// Lost context
const person = {
    name: 'John',
    greet: function() {
        console.log(`Hello, ${this.name}`);
    }
};

person.greet(); // "Hello, John"

const greetFunc = person.greet;
greetFunc(); // "Hello, undefined" (this is lost)

// Solutions:

// 1. Arrow function (captures outer this)
const person2 = {
    name: 'John',
    greet: function() {
        setTimeout(() => {
            console.log(`Hello, ${this.name}`); // 'this' is person2
        }, 1000);
    }
};

// 2. bind()
const person3 = {
    name: 'John',
    greet: function() {
        setTimeout(function() {
            console.log(`Hello, ${this.name}`);
        }.bind(this), 1000);
    }
};

// 3. Store 'this' reference
const person4 = {
    name: 'John',
    greet: function() {
        const self = this; // or const that = this;
        setTimeout(function() {
            console.log(`Hello, ${self.name}`);
        }, 1000);
    }
};
```

---

## JSON (JavaScript Object Notation)

JSON is a lightweight data-interchange format that's easy for humans to read and write, and easy for machines to parse and generate.

### What is JSON?

JSON is a text format for storing and transporting data, based on JavaScript object syntax but language-independent.

**Rules:**
- Data is in name/value pairs
- Data is separated by commas
- Curly braces hold objects
- Square brackets hold arrays
- Property names must be double-quoted strings
- String values must be double-quoted
- No trailing commas
- No functions or undefined values

### JSON Syntax

```json
{
  "name": "John Doe",
  "age": 30,
  "isEmployed": true,
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "zipCode": "10001"
  },
  "hobbies": ["reading", "coding", "gaming"],
  "spouse": null
}
```

### Valid JSON Data Types

| Type | Example |
|------|---------|
| String | `"Hello World"` |
| Number | `42`, `3.14`, `-10` |
| Boolean | `true`, `false` |
| Null | `null` |
| Object | `{"key": "value"}` |
| Array | `[1, 2, 3]` |

**Not Valid in JSON:**
- Functions
- Dates (must be strings)
- `undefined`
- Single-quoted strings
- Comments
- Trailing commas

### JSON.stringify()

Converts JavaScript object to JSON string.

```javascript
const person = {
    name: 'John',
    age: 30,
    isEmployed: true,
    hobbies: ['reading', 'coding']
};

// Basic conversion
const json = JSON.stringify(person);
console.log(json);
// {"name":"John","age":30,"isEmployed":true,"hobbies":["reading","coding"]}

console.log(typeof json); // "string"

// Pretty print (with indentation)
const prettyJson = JSON.stringify(person, null, 2);
console.log(prettyJson);
/*
{
  "name": "John",
  "age": 30,
  "isEmployed": true,
  "hobbies": [
    "reading",
    "coding"
  ]
}
*/

// Replacer function (filter properties)
const filtered = JSON.stringify(person, ['name', 'age']);
console.log(filtered); // {"name":"John","age":30}

// Replacer function (transform values)
const transformed = JSON.stringify(person, (key, value) => {
    if (typeof value === 'string') {
        return value.toUpperCase();
    }
    return value;
});
console.log(transformed);
// {"name":"JOHN","age":30,"isEmployed":true,"hobbies":["READING","CODING"]}

// toJSON() method
const user = {
    name: 'John',
    password: 'secret123',
    toJSON: function() {
        return {
            name: this.name
            // Exclude password
        };
    }
};
console.log(JSON.stringify(user)); // {"name":"John"}
```

### JSON.parse()

Converts JSON string to JavaScript object.

```javascript
const json = '{"name":"John","age":30,"isEmployed":true}';

// Basic parsing
const person = JSON.parse(json);
console.log(person);        // { name: 'John', age: 30, isEmployed: true }
console.log(typeof person); // "object"
console.log(person.name);   // "John"

// Parse array
const arrayJson = '[1, 2, 3, 4, 5]';
const numbers = JSON.parse(arrayJson);
console.log(numbers); // [1, 2, 3, 4, 5]

// Reviver function (transform values)
const jsonWithDate = '{"name":"John","birthDate":"1990-01-01"}';
const personWithDate = JSON.parse(jsonWithDate, (key, value) => {
    if (key === 'birthDate') {
        return new Date(value);
    }
    return value;
});
console.log(personWithDate.birthDate instanceof Date); // true

// Error handling
try {
    const invalid = JSON.parse('invalid json');
} catch (error) {
    console.error('JSON parse error:', error.message);
}
```

### Common JSON Patterns

**Nested Objects:**
```javascript
const data = {
    user: {
        id: 1,
        name: 'John Doe',
        contact: {
            email: 'john@example.com',
            phone: '555-1234'
        }
    },
    posts: [
        {
            id: 1,
            title: 'First Post',
            date: '2024-01-01'
        },
        {
            id: 2,
            title: 'Second Post',
            date: '2024-01-02'
        }
    ]
};

const json = JSON.stringify(data, null, 2);
const parsed = JSON.parse(json);
console.log(parsed.user.contact.email); // "john@example.com"
```

**Deep Copy Using JSON:**
```javascript
const original = {
    name: 'John',
    address: {
        city: 'New York'
    }
};

// Deep copy (limitations: functions, dates, undefined lost)
const copy = JSON.parse(JSON.stringify(original));

copy.address.city = 'Los Angeles';

console.log(original.address.city); // "New York"
console.log(copy.address.city);     // "Los Angeles"

// Limitations
const complex = {
    name: 'John',
    greet: function() { return 'Hi'; },  // Lost
    birthDate: new Date(),                 // Becomes string
    undefined: undefined,                  // Lost
    nullValue: null                        // Preserved
};

const complexCopy = JSON.parse(JSON.stringify(complex));
console.log(complexCopy);
// { name: 'John', birthDate: '2024-01-01T00:00:00.000Z', nullValue: null }
```

### Working with APIs

```javascript
// Sending JSON to API
const userData = {
    username: 'john_doe',
    email: 'john@example.com',
    age: 30
};

fetch('https://api.example.com/users', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify(userData)
})
.then(response => response.json())
.then(data => {
    console.log('Success:', data);
})
.catch(error => {
    console.error('Error:', error);
});

// Receiving JSON from API
fetch('https://api.example.com/users/1')
    .then(response => response.json())
    .then(user => {
        console.log('User:', user.name);
    });
```

### Local Storage with JSON

```javascript
// Store object in localStorage
const user = {
    name: 'John',
    preferences: {
        theme: 'dark',
        language: 'en'
    }
};

// Save (must be string)
localStorage.setItem('user', JSON.stringify(user));

// Retrieve
const storedUser = JSON.parse(localStorage.getItem('user'));
console.log(storedUser.preferences.theme); // "dark"

// Update
storedUser.preferences.theme = 'light';
localStorage.setItem('user', JSON.stringify(storedUser));

// Remove
localStorage.removeItem('user');
```

---

## Best Practices

### Object Creation

1. **Use object literals** for simple objects
2. **Use classes** for objects with behavior
3. **Use const** for object references
4. **Use meaningful property names**

```javascript
// Good
const user = {
    firstName: 'John',
    lastName: 'Doe',
    email: 'john@example.com'
};

// Avoid
const u = {
    fn: 'John',
    ln: 'Doe',
    e: 'john@example.com'
};
```

### Property Access

1. **Use dot notation** when possible
2. **Use bracket notation** for dynamic properties or special characters
3. **Use optional chaining** for nested properties

```javascript
// Good
const name = user.name;
const city = user?.address?.city;
const prop = user[dynamicProperty];

// Avoid
const name = user['name']; // Use dot notation instead
```

### Methods

1. **Avoid arrow functions** for object methods (lose 'this')
2. **Use method shorthand** in ES6+
3. **Return 'this'** for method chaining

```javascript
// Good
const obj = {
    value: 0,
    increment() {
        this.value++;
        return this;
    }
};

// Avoid
const obj2 = {
    value: 0,
    increment: () => {
        this.value++; // 'this' is not the object
    }
};
```

### JSON

1. **Validate JSON** before parsing
2. **Handle parse errors** with try-catch
3. **Don't use JSON** for deep cloning complex objects
4. **Sanitize user input** before parsing

```javascript
// Good
try {
    const data = JSON.parse(jsonString);
    // Use data
} catch (error) {
    console.error('Invalid JSON:', error);
}

// Avoid
const data = JSON.parse(jsonString); // May throw error
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Objects | Key-value pairs, properties and methods |
| Properties | Access with dot/bracket notation, dynamic |
| Methods | Functions as properties, use 'this' |
| 'this' | Context-dependent, use call/apply/bind |
| JSON | Text format, stringify/parse, API communication |

## Next Topic

Continue to [Asynchronous JavaScript](./05-asynchronous-javascript.md) to learn about the event loop, promises, async/await, and handling asynchronous operations.
