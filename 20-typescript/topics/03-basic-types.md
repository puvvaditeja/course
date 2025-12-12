# TypeScript Basic Types

## Overview

TypeScript's type system is the foundation of the language. Understanding basic types is essential for writing type-safe code. This guide covers primitive types, special types, object types, and union types.

---

## Simple/Variable Types

### Primitive Types

TypeScript includes all JavaScript primitive types with type annotations.

#### String

Represents textual data.

```typescript
// Type annotation
let firstName: string = "Alice";
let lastName: string = 'Smith';
let fullName: string = `${firstName} ${lastName}`;

// Type inference - TypeScript infers the type
let greeting = "Hello, World!"; // inferred as string

// String methods are fully supported
let message: string = "TypeScript";
console.log(message.toUpperCase());    // "TYPESCRIPT"
console.log(message.toLowerCase());    // "typescript"
console.log(message.length);           // 10
```

#### Number

Represents both integer and floating-point numbers.

```typescript
// All numbers are floating-point
let decimal: number = 42;
let float: number = 3.14;
let negative: number = -10;

// Different number representations
let hex: number = 0xf00d;          // Hexadecimal
let binary: number = 0b1010;       // Binary
let octal: number = 0o744;         // Octal

// Special numeric values
let notANumber: number = NaN;
let infinity: number = Infinity;

// Number methods
let price: number = 99.99;
console.log(price.toFixed(2));     // "99.99"
console.log(price.toString());     // "99.99"
```

#### Boolean

Represents true or false values.

```typescript
let isActive: boolean = true;
let isCompleted: boolean = false;

// Boolean expressions
let isAdult: boolean = age >= 18;
let hasAccess: boolean = isActive && isAdult;

// Cannot assign non-boolean values
let flag: boolean = 1;      // Error: Type 'number' is not assignable to type 'boolean'
```

#### BigInt (ES2020+)

For representing very large integers.

```typescript
// Create BigInt values
let big1: bigint = 100n;
let big2: bigint = BigInt(100);

// BigInt arithmetic
let sum: bigint = big1 + big2;        // 200n
let product: bigint = big1 * 2n;      // 200n

// Cannot mix BigInt and number
let invalid = big1 + 100;             // Error: Cannot mix BigInt and number
```

#### Symbol

Creates unique identifiers.

```typescript
// Each symbol is unique
let sym1: symbol = Symbol("key");
let sym2: symbol = Symbol("key");

console.log(sym1 === sym2);           // false - each symbol is unique

// Using symbols as object keys
const uniqueKey: symbol = Symbol("id");
const obj = {
    [uniqueKey]: 12345
};

console.log(obj[uniqueKey]);          // 12345
```

### Type Inference

TypeScript can automatically infer types:

```typescript
// Type inference in action
let inferredString = "Hello";         // inferred as string
let inferredNumber = 42;              // inferred as number
let inferredBoolean = true;           // inferred as boolean

// Inference from return values
function getAge() {
    return 25;                         // function inferred to return number
}

let age = getAge();                    // age inferred as number
```

### Variable Declarations

```typescript
// let - block-scoped, reassignable
let count: number = 0;
count = 1;                             // OK

// const - block-scoped, not reassignable
const MAX_SIZE: number = 100;
MAX_SIZE = 200;                        // Error: Cannot assign to 'MAX_SIZE' because it is a constant

// var - function-scoped (avoid in modern code)
var oldStyle: string = "Not recommended";
```

---

## Special Types

### Any Type

Disables type checking - use sparingly!

```typescript
// any allows any value without type checking
let flexible: any = 4;
flexible = "now it's a string";        // OK
flexible = false;                      // OK
flexible.doSomething();                // OK - no type checking (might cause runtime error!)

// Useful when migrating from JavaScript
function legacyFunction(data: any): any {
    return data;
}

// Working with dynamic data
let userData: any = JSON.parse('{"name": "Alice", "age": 30}');
console.log(userData.name);            // OK, but no type safety
```

**Warning:** Avoid `any` when possible - it defeats the purpose of TypeScript!

### Unknown Type

Type-safe alternative to `any`:

```typescript
// unknown requires type checking before use
let userInput: unknown = getUserInput();

// Cannot directly use unknown values
console.log(userInput.toUpperCase()); // Error: Object is of type 'unknown'

// Must check type first
if (typeof userInput === "string") {
    console.log(userInput.toUpperCase()); // OK - type is narrowed to string
}

// Type assertion
let str: string = userInput as string; // Explicitly assert type

// Better than any for unknown data
function processValue(value: unknown): string {
    if (typeof value === "string") {
        return value.toUpperCase();
    } else if (typeof value === "number") {
        return value.toString();
    } else {
        return "Unknown type";
    }
}
```

### Void Type

Represents absence of a value - typically for functions that don't return anything:

```typescript
// Functions with no return value
function logMessage(message: string): void {
    console.log(message);
    // No return statement
}

function processData(data: any[]): void {
    data.forEach(item => console.log(item));
    return undefined;                  // OK - can explicitly return undefined
}

// void variables can only be undefined or null (with strictNullChecks off)
let unusable: void = undefined;
```

### Never Type

Represents values that never occur:

```typescript
// Function that never returns (throws error)
function throwError(message: string): never {
    throw new Error(message);
    // No code after throw
}

// Function with infinite loop
function infiniteLoop(): never {
    while (true) {
        // Runs forever
    }
}

// Exhaustive type checking
type Shape = "circle" | "square";

function getArea(shape: Shape): number {
    switch (shape) {
        case "circle":
            return Math.PI * 10 * 10;
        case "square":
            return 10 * 10;
        default:
            const exhaustiveCheck: never = shape;
            throw new Error(`Unhandled shape: ${exhaustiveCheck}`);
    }
}
```

### Null and Undefined

```typescript
// null - intentional absence of value
let nullValue: null = null;

// undefined - variable declared but not assigned
let undefinedValue: undefined = undefined;

// strictNullChecks: true (recommended)
let name: string = "Alice";
name = null;                           // Error with strictNullChecks
name = undefined;                      // Error with strictNullChecks

// Allowing null/undefined explicitly
let nullable: string | null = null;
let optional: string | undefined = undefined;
let either: string | null | undefined = null;

// Optional parameters are automatically union with undefined
function greet(name?: string): void {  // name is string | undefined
    console.log(name ?? "Guest");
}
```

---

## Object Types

### Object Type Annotation

```typescript
// Basic object type
let person: { name: string; age: number } = {
    name: "Bob",
    age: 30
};

// Object with optional properties
let employee: {
    id: number;
    name: string;
    email?: string;                    // Optional property
} = {
    id: 1,
    name: "Alice"
    // email is optional
};

// Object with readonly properties
let config: {
    readonly apiUrl: string;
    timeout: number;
} = {
    apiUrl: "https://api.example.com",
    timeout: 5000
};

config.timeout = 10000;                // OK
config.apiUrl = "new-url";             // Error: Cannot assign to 'apiUrl' because it is a read-only property
```

### Array Types

```typescript
// Array type annotation - two syntaxes
let numbers: number[] = [1, 2, 3, 4, 5];
let strings: Array<string> = ["a", "b", "c"];

// Array methods are type-safe
numbers.push(6);                       // OK
numbers.push("7");                     // Error: Argument of type 'string' is not assignable to parameter of type 'number'

// Array of objects
let users: { id: number; name: string }[] = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" }
];

// Multi-dimensional arrays
let matrix: number[][] = [
    [1, 2, 3],
    [4, 5, 6]
];

// ReadonlyArray
let readonlyNumbers: ReadonlyArray<number> = [1, 2, 3];
readonlyNumbers.push(4);               // Error: Property 'push' does not exist on type 'ReadonlyArray<number>'
```

### Tuple Types

Fixed-length arrays with specific types for each position:

```typescript
// Basic tuple
let coordinate: [number, number] = [10, 20];

// Tuple with different types
let person: [string, number, boolean] = ["Alice", 30, true];

// Accessing tuple elements
let name = person[0];                  // string
let age = person[1];                   // number
let isActive = person[2];              // boolean

// Destructuring tuples
let [userName, userAge, active] = person;

// Tuple with optional elements
let response: [number, string?] = [200]; // OK
response = [404, "Not Found"];          // OK

// Tuple with rest elements
let scores: [string, ...number[]] = ["Bob", 85, 90, 92];

// Named tuples (TypeScript 4.0+)
let student: [name: string, age: number, grade: string] = ["Alice", 20, "A"];
```

### Enum Types

Define a set of named constants:

```typescript
// Numeric enum
enum Direction {
    Up,                                // 0
    Down,                              // 1
    Left,                              // 2
    Right                              // 3
}

let move: Direction = Direction.Up;
console.log(move);                     // 0

// Custom numeric values
enum Status {
    Pending = 1,
    InProgress = 2,
    Completed = 3,
    Failed = 4
}

// String enum (recommended)
enum Color {
    Red = "RED",
    Green = "GREEN",
    Blue = "BLUE"
}

let favoriteColor: Color = Color.Blue;
console.log(favoriteColor);            // "BLUE"

// Heterogeneous enum (avoid)
enum Mixed {
    No = 0,
    Yes = "YES"
}

// Using enums
function getColorName(color: Color): string {
    switch (color) {
        case Color.Red:
            return "Red";
        case Color.Green:
            return "Green";
        case Color.Blue:
            return "Blue";
    }
}

// Const enum - inlined at compile time
const enum HttpStatus {
    OK = 200,
    NotFound = 404,
    ServerError = 500
}

let status: HttpStatus = HttpStatus.OK; // Compiles to: let status = 200;
```

---

## Union Types

Variables that can be one of several types:

### Basic Union Types

```typescript
// Union of primitive types
let id: number | string;
id = 101;                              // OK
id = "ABC123";                         // OK
id = true;                             // Error: Type 'boolean' is not assignable

// Union in function parameters
function printId(id: number | string): void {
    console.log(`ID: ${id}`);
}

printId(101);                          // OK
printId("ABC");                        // OK

// Union with null/undefined
let name: string | null = null;
name = "Alice";                        // OK
name = null;                           // OK
```

### Type Narrowing

TypeScript narrows union types based on checks:

```typescript
function processValue(value: string | number): string {
    // Type narrowing with typeof
    if (typeof value === "string") {
        return value.toUpperCase();    // value is narrowed to string
    } else {
        return value.toFixed(2);       // value is narrowed to number
    }
}

// Narrowing with truthiness check
function printLength(text: string | null): void {
    if (text) {                        // text is narrowed to string
        console.log(text.length);
    } else {
        console.log("No text provided");
    }
}

// Narrowing with instanceof
function handleValue(value: Date | string): string {
    if (value instanceof Date) {
        return value.toISOString();    // value is narrowed to Date
    } else {
        return value.toUpperCase();    // value is narrowed to string
    }
}
```

### Union of Object Types

```typescript
interface Dog {
    type: "dog";
    bark(): void;
}

interface Cat {
    type: "cat";
    meow(): void;
}

type Pet = Dog | Cat;

function handlePet(pet: Pet): void {
    // Discriminated union - using type property
    if (pet.type === "dog") {
        pet.bark();                    // pet is narrowed to Dog
    } else {
        pet.meow();                    // pet is narrowed to Cat
    }
}

// Creating pets
const myDog: Dog = {
    type: "dog",
    bark() { console.log("Woof!"); }
};

const myCat: Cat = {
    type: "cat",
    meow() { console.log("Meow!"); }
};
```

### Union with Arrays

```typescript
// Array of union types
let mixed: (number | string)[] = [1, "two", 3, "four"];

// Union of array types
let arrayOrNumber: number[] | number;
arrayOrNumber = [1, 2, 3];             // OK
arrayOrNumber = 5;                     // OK

// Processing union arrays
function sumValues(values: (number | string)[]): number {
    return values.reduce((sum, value) => {
        if (typeof value === "number") {
            return sum + value;
        } else {
            return sum + parseFloat(value);
        }
    }, 0);
}
```

### Literal Union Types

Specific literal values:

```typescript
// String literal union
type Direction = "north" | "south" | "east" | "west";
let heading: Direction = "north";      // OK
heading = "northeast";                 // Error: Type '"northeast"' is not assignable to type 'Direction'

// Numeric literal union
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
let roll: DiceRoll = 4;                // OK
roll = 7;                              // Error

// Boolean literal (less common)
type AlwaysTrue = true;
let flag: AlwaysTrue = true;           // OK
flag = false;                          // Error

// Combining literal unions
type Status = "success" | "error" | "pending";
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";

function makeRequest(method: HttpMethod, status: Status): void {
    console.log(`${method} request is ${status}`);
}

makeRequest("GET", "success");         // OK
makeRequest("PATCH", "success");       // Error: Argument of type '"PATCH"' is not assignable
```

---

## Type Assertions

Telling TypeScript to treat a value as a specific type:

### As Syntax (Recommended)

```typescript
// Type assertion with 'as'
let someValue: unknown = "this is a string";
let strLength: number = (someValue as string).length;

// Asserting to more specific type
let input: any = document.getElementById("myInput");
let inputElement: HTMLInputElement = input as HTMLInputElement;
inputElement.value = "Hello";

// Double assertion (use cautiously)
let unusual = ("hello" as unknown) as number; // Forces the assertion
```

### Angle Bracket Syntax

```typescript
// Alternative syntax (not usable in JSX/TSX files)
let someValue: unknown = "this is a string";
let strLength: number = (<string>someValue).length;
```

### Const Assertions

```typescript
// Without const assertion - type is string
let color1 = "red";                    // type: string

// With const assertion - type is literal "red"
let color2 = "red" as const;           // type: "red"

// Readonly array
let numbers = [1, 2, 3] as const;      // type: readonly [1, 2, 3]
numbers.push(4);                       // Error: Property 'push' does not exist on type 'readonly [1, 2, 3]'

// Readonly object
let config = {
    api: "https://api.example.com",
    timeout: 5000
} as const;

config.timeout = 10000;                // Error: Cannot assign to 'timeout' because it is a read-only property
```

---

## Practical Examples

### Form Data Validation

```typescript
type FormField = string | number | boolean;

interface FormData {
    username: string;
    age: number;
    email?: string;
    subscribe: boolean;
    preferences: string[];
}

function validateForm(data: FormData): boolean {
    if (data.username.length < 3) {
        console.log("Username too short");
        return false;
    }

    if (data.age < 18) {
        console.log("Must be 18 or older");
        return false;
    }

    if (data.email && !data.email.includes("@")) {
        console.log("Invalid email");
        return false;
    }

    return true;
}

const formData: FormData = {
    username: "alice123",
    age: 25,
    email: "alice@example.com",
    subscribe: true,
    preferences: ["email", "sms"]
};

validateForm(formData);
```

### API Response Handling

```typescript
type ApiResponse<T> = {
    success: true;
    data: T;
} | {
    success: false;
    error: string;
};

interface User {
    id: number;
    name: string;
    email: string;
}

function handleUserResponse(response: ApiResponse<User>): void {
    if (response.success) {
        console.log(`User: ${response.data.name}`);
        console.log(`Email: ${response.data.email}`);
    } else {
        console.error(`Error: ${response.error}`);
    }
}

// Success case
const successResponse: ApiResponse<User> = {
    success: true,
    data: { id: 1, name: "Alice", email: "alice@example.com" }
};

// Error case
const errorResponse: ApiResponse<User> = {
    success: false,
    error: "User not found"
};

handleUserResponse(successResponse);
handleUserResponse(errorResponse);
```

---

## Summary

| Type Category | Examples | Use Case |
|---------------|----------|----------|
| Primitive | string, number, boolean | Basic values |
| Special | any, unknown, void, never | Special scenarios |
| Object | object, array, tuple, enum | Complex data structures |
| Union | string \| number | Multiple possible types |
| Literal | "red" \| "blue" | Specific values |

## Type System Best Practices

```typescript
// 1. Let TypeScript infer when obvious
let count = 0;                         // Inferred as number

// 2. Be explicit when needed
let ids: (string | number)[] = [];     // Explicit union type

// 3. Avoid 'any' - use 'unknown' instead
let data: unknown = JSON.parse(input); // Safer than any

// 4. Use const for literal types
const STATUS = "active" as const;      // Literal type, not string

// 5. Prefer interfaces for object shapes (see advanced types)
interface User {
    name: string;
    age: number;
}
```

## Next Topic

Continue to [Advanced Types](./04-advanced-types.md) to learn about type aliases, interfaces, casting, and utility types.
