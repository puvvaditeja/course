# TypeScript Functions and Classes

## Overview

TypeScript enhances JavaScript functions and classes with strong typing, access modifiers, and decorators. This guide covers function types, class definitions, inheritance, access modifiers, and decorators.

---

## Functions

### Function Type Annotations

#### Basic Function Types

```typescript
// Function with parameter and return types
function add(x: number, y: number): number {
    return x + y;
}

// Function with no return value
function logMessage(message: string): void {
    console.log(message);
}

// Arrow function
const multiply = (x: number, y: number): number => x * y;

// Function that returns never (throws error)
function throwError(message: string): never {
    throw new Error(message);
}
```

#### Optional and Default Parameters

```typescript
// Optional parameters (use ?)
function greet(name: string, greeting?: string): string {
    if (greeting) {
        return `${greeting}, ${name}!`;
    }
    return `Hello, ${name}!`;
}

greet("Alice");                        // "Hello, Alice!"
greet("Bob", "Hi");                    // "Hi, Bob!"

// Default parameters
function createUser(name: string, age: number = 18): User {
    return { name, age };
}

createUser("Alice");                   // age defaults to 18
createUser("Bob", 25);                 // age is 25

// Optional must come after required
function invalid(optional?: string, required: string) {} // Error
function valid(required: string, optional?: string) {}   // OK
```

#### Rest Parameters

```typescript
// Rest parameters with type annotation
function sum(...numbers: number[]): number {
    return numbers.reduce((total, n) => total + n, 0);
}

sum(1, 2, 3, 4, 5);                    // 15

// Rest with specific types
function concatenate(separator: string, ...strings: string[]): string {
    return strings.join(separator);
}

concatenate(", ", "apple", "banana", "orange"); // "apple, banana, orange"

// Rest parameters with tuple types
function logInfo(...args: [string, number, boolean]): void {
    const [name, age, active] = args;
    console.log(`${name}, ${age}, ${active}`);
}

logInfo("Alice", 30, true);
```

### Function Overloads

Define multiple function signatures:

```typescript
// Overload signatures
function makeDate(timestamp: number): Date;
function makeDate(year: number, month: number, day: number): Date;

// Implementation signature
function makeDate(yearOrTimestamp: number, month?: number, day?: number): Date {
    if (month !== undefined && day !== undefined) {
        return new Date(yearOrTimestamp, month - 1, day);
    } else {
        return new Date(yearOrTimestamp);
    }
}

const date1 = makeDate(1609459200000);           // From timestamp
const date2 = makeDate(2024, 1, 1);              // From year, month, day

// Another example - flexible input types
function getValue(id: number): string;
function getValue(name: string): number;
function getValue(idOrName: number | string): string | number {
    if (typeof idOrName === "number") {
        return `ID-${idOrName}`;
    } else {
        return idOrName.length;
    }
}

const result1 = getValue(123);         // returns string
const result2 = getValue("Alice");     // returns number
```

### Function Types

```typescript
// Function type definition
type MathOperation = (x: number, y: number) => number;

const add: MathOperation = (x, y) => x + y;
const subtract: MathOperation = (x, y) => x - y;

// Function type in interface
interface Calculator {
    operation: (x: number, y: number) => number;
}

const calculator: Calculator = {
    operation: (x, y) => x + y
};

// Function that accepts another function
function applyOperation(
    x: number,
    y: number,
    operation: (a: number, b: number) => number
): number {
    return operation(x, y);
}

applyOperation(5, 3, add);             // 8
applyOperation(5, 3, subtract);        // 2

// Higher-order function
function createMultiplier(factor: number): (x: number) => number {
    return (x: number) => x * factor;
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5));                // 10
console.log(triple(5));                // 15
```

### Generic Functions

Functions that work with multiple types:

```typescript
// Basic generic function
function identity<T>(value: T): T {
    return value;
}

const num = identity<number>(42);      // Type: number
const str = identity<string>("hello"); // Type: string
const auto = identity(true);           // Type inferred as boolean

// Generic with constraints
interface Lengthwise {
    length: number;
}

function logLength<T extends Lengthwise>(item: T): void {
    console.log(item.length);
}

logLength("hello");                    // OK - string has length
logLength([1, 2, 3]);                  // OK - array has length
logLength({ length: 10 });             // OK - object has length
logLength(42);                         // Error - number doesn't have length

// Multiple type parameters
function merge<T, U>(obj1: T, obj2: U): T & U {
    return { ...obj1, ...obj2 };
}

const merged = merge(
    { name: "Alice" },
    { age: 30 }
);
// Type: { name: string } & { age: number }

// Generic array operations
function getFirst<T>(array: T[]): T | undefined {
    return array[0];
}

const firstNumber = getFirst([1, 2, 3]);     // number | undefined
const firstName = getFirst(["a", "b", "c"]); // string | undefined
```

### Arrow Functions and This

```typescript
// Arrow functions preserve 'this' context
class Counter {
    count = 0;

    // Arrow function - correct 'this' binding
    increment = (): void => {
        this.count++;
    };

    // Regular function - 'this' might be wrong
    decrement(): void {
        this.count--;
    }
}

const counter = new Counter();
const incrementFn = counter.increment;
incrementFn();                         // Works correctly - 'this' is preserved

// 'this' parameter annotation
interface Database {
    query(sql: string): any[];
}

function executeQuery(this: Database, sql: string): any[] {
    return this.query(sql);
}

// Must be called with correct 'this' context
const db: Database = {
    query: (sql) => []
};

executeQuery.call(db, "SELECT * FROM users");
```

---

## Classes

### Basic Class Definition

```typescript
// Simple class
class Person {
    // Properties
    name: string;
    age: number;

    // Constructor
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }

    // Method
    greet(): string {
        return `Hello, I'm ${this.name} and I'm ${this.age} years old.`;
    }
}

// Creating instances
const person1 = new Person("Alice", 30);
const person2 = new Person("Bob", 25);

console.log(person1.greet());          // "Hello, I'm Alice and I'm 30 years old."
```

### Property Initialization

```typescript
// Different ways to initialize properties

class User {
    // Property with type annotation
    name: string;
    email: string;

    // Property with default value
    role: string = "user";

    // Property initialized in constructor
    id: number;

    // Readonly property
    readonly createdAt: Date = new Date();

    constructor(name: string, email: string) {
        this.name = name;
        this.email = email;
        this.id = Math.random();
    }
}

// Parameter properties - shorthand
class Product {
    constructor(
        public name: string,
        public price: number,
        private sku: string
    ) {
        // Properties automatically created and assigned
    }
}

const product = new Product("Widget", 19.99, "ABC123");
console.log(product.name);             // "Widget"
console.log(product.price);            // 19.99
console.log(product.sku);              // Error - 'sku' is private
```

### Class Methods

```typescript
class Calculator {
    // Instance method
    add(x: number, y: number): number {
        return x + y;
    }

    // Method with optional parameters
    multiply(x: number, y: number, z?: number): number {
        const result = x * y;
        return z !== undefined ? result * z : result;
    }

    // Method returning void
    logResult(result: number): void {
        console.log(`Result: ${result}`);
    }
}

const calc = new Calculator();
calc.add(5, 3);                        // 8
calc.multiply(2, 3, 4);                // 24
calc.logResult(42);                    // Logs: "Result: 42"
```

### Getters and Setters

```typescript
class Employee {
    private _salary: number = 0;

    // Getter
    get salary(): number {
        return this._salary;
    }

    // Setter with validation
    set salary(value: number) {
        if (value < 0) {
            throw new Error("Salary cannot be negative");
        }
        this._salary = value;
    }

    // Computed property
    get annualSalary(): number {
        return this._salary * 12;
    }
}

const emp = new Employee();
emp.salary = 5000;                     // Uses setter
console.log(emp.salary);               // Uses getter: 5000
console.log(emp.annualSalary);         // 60000
emp.salary = -1000;                    // Error: Salary cannot be negative
```

### Static Members

```typescript
// Static properties and methods
class MathUtils {
    // Static property
    static PI: number = 3.14159;

    // Static method
    static circleArea(radius: number): number {
        return MathUtils.PI * radius * radius;
    }

    // Static factory method
    static createPoint(x: number, y: number): Point {
        return new Point(x, y);
    }
}

// Access static members on class, not instance
console.log(MathUtils.PI);             // 3.14159
console.log(MathUtils.circleArea(5));  // 78.53975

class Point {
    constructor(public x: number, public y: number) {}
}

const point = MathUtils.createPoint(10, 20);
```

### Class Inheritance

```typescript
// Base class
class Animal {
    constructor(public name: string) {}

    move(distance: number = 0): void {
        console.log(`${this.name} moved ${distance}m.`);
    }

    makeSound(): void {
        console.log("Some generic animal sound");
    }
}

// Derived class
class Dog extends Animal {
    constructor(name: string, public breed: string) {
        super(name);               // Call parent constructor
    }

    // Override method
    makeSound(): void {
        console.log("Woof! Woof!");
    }

    // New method specific to Dog
    fetch(): void {
        console.log(`${this.name} is fetching!`);
    }
}

const dog = new Dog("Buddy", "Golden Retriever");
dog.move(10);                          // "Buddy moved 10m."
dog.makeSound();                       // "Woof! Woof!"
dog.fetch();                           // "Buddy is fetching!"

// Multiple levels of inheritance
class Puppy extends Dog {
    constructor(name: string, breed: string, public age: number) {
        super(name, breed);
    }

    play(): void {
        console.log(`${this.name} the puppy is playing!`);
    }
}
```

### Abstract Classes

Classes that cannot be instantiated directly:

```typescript
// Abstract base class
abstract class Shape {
    constructor(public color: string) {}

    // Abstract method - must be implemented by derived classes
    abstract getArea(): number;
    abstract getPerimeter(): number;

    // Concrete method - can be used by all derived classes
    describe(): string {
        return `A ${this.color} shape with area ${this.getArea()}`;
    }
}

// Cannot instantiate abstract class
const shape = new Shape("red");        // Error

// Concrete implementation
class Circle extends Shape {
    constructor(color: string, public radius: number) {
        super(color);
    }

    getArea(): number {
        return Math.PI * this.radius ** 2;
    }

    getPerimeter(): number {
        return 2 * Math.PI * this.radius;
    }
}

class Rectangle extends Shape {
    constructor(
        color: string,
        public width: number,
        public height: number
    ) {
        super(color);
    }

    getArea(): number {
        return this.width * this.height;
    }

    getPerimeter(): number {
        return 2 * (this.width + this.height);
    }
}

const circle = new Circle("blue", 5);
const rectangle = new Rectangle("green", 10, 20);

console.log(circle.describe());        // "A blue shape with area 78.53981633974483"
console.log(rectangle.getArea());      // 200
```

### Implementing Interfaces

```typescript
// Interface definition
interface Printable {
    print(): void;
}

interface Serializable {
    serialize(): string;
}

// Class implementing single interface
class Document implements Printable {
    constructor(public content: string) {}

    print(): void {
        console.log(this.content);
    }
}

// Class implementing multiple interfaces
class Report implements Printable, Serializable {
    constructor(public title: string, public data: any) {}

    print(): void {
        console.log(`Report: ${this.title}`);
        console.log(this.data);
    }

    serialize(): string {
        return JSON.stringify({ title: this.title, data: this.data });
    }
}

const report = new Report("Sales Report", { revenue: 10000 });
report.print();
const json = report.serialize();
```

---

## Access Modifiers

TypeScript provides access modifiers to control member visibility.

### Public (Default)

```typescript
class Person {
    public name: string;               // Explicitly public
    age: number;                       // Implicitly public

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }

    public greet(): void {
        console.log(`Hello, I'm ${this.name}`);
    }
}

const person = new Person("Alice", 30);
console.log(person.name);              // OK - public
person.greet();                        // OK - public
```

### Private

```typescript
class BankAccount {
    private balance: number = 0;

    deposit(amount: number): void {
        if (amount > 0) {
            this.balance += amount;
        }
    }

    withdraw(amount: number): boolean {
        if (amount > 0 && amount <= this.balance) {
            this.balance -= amount;
            return true;
        }
        return false;
    }

    getBalance(): number {
        return this.balance;
    }
}

const account = new BankAccount();
account.deposit(100);
console.log(account.getBalance());     // 100
console.log(account.balance);          // Error - 'balance' is private

// Private members not accessible in derived classes
class SavingsAccount extends BankAccount {
    addInterest(rate: number): void {
        // Error - 'balance' is private
        this.balance += this.balance * rate;
    }
}
```

### Protected

```typescript
class Vehicle {
    protected speed: number = 0;

    accelerate(amount: number): void {
        this.speed += amount;
    }
}

class Car extends Vehicle {
    // Can access protected member from parent
    getCurrentSpeed(): number {
        return this.speed;             // OK - protected accessible in derived class
    }

    brake(): void {
        this.speed = 0;                // OK
    }
}

const car = new Car();
car.accelerate(50);
console.log(car.getCurrentSpeed());    // 50
console.log(car.speed);                // Error - 'speed' is protected
```

### Readonly Modifier

```typescript
class Person {
    readonly id: number;
    readonly createdAt: Date = new Date();

    constructor(id: number) {
        this.id = id;                  // OK in constructor
    }

    updateId(newId: number): void {
        this.id = newId;               // Error - cannot modify readonly property
    }
}

const person = new Person(1);
console.log(person.id);                // 1
person.id = 2;                         // Error - readonly property

// Readonly parameter properties
class User {
    constructor(
        public readonly username: string,
        public email: string
    ) {}
}

const user = new User("alice", "alice@example.com");
user.email = "newemail@example.com";   // OK
user.username = "bob";                 // Error - readonly
```

### Access Modifier Comparison

```typescript
class Example {
    public publicProp = "accessible everywhere";
    private privateProp = "only in this class";
    protected protectedProp = "in this class and derived classes";
    readonly readonlyProp = "readable everywhere, writable only in constructor";

    private privateMethod(): void {
        console.log("Private method");
    }

    protected protectedMethod(): void {
        console.log("Protected method");
    }

    public publicMethod(): void {
        // Can access all members within the class
        console.log(this.publicProp);
        console.log(this.privateProp);
        console.log(this.protectedProp);
        this.privateMethod();
        this.protectedMethod();
    }
}

class Derived extends Example {
    accessMembers(): void {
        console.log(this.publicProp);      // OK
        console.log(this.privateProp);     // Error - private
        console.log(this.protectedProp);   // OK
        this.protectedMethod();            // OK
        this.privateMethod();              // Error - private
    }
}

const instance = new Example();
console.log(instance.publicProp);          // OK
console.log(instance.privateProp);         // Error - private
console.log(instance.protectedProp);       // Error - protected
```

---

## Decorators

Decorators are special declarations that can modify classes, methods, properties, and parameters.

### Enabling Decorators

```json
// tsconfig.json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

### Class Decorators

```typescript
// Simple class decorator
function sealed(constructor: Function) {
    Object.seal(constructor);
    Object.seal(constructor.prototype);
}

@sealed
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
}

// Decorator factory - returns a decorator
function component(name: string) {
    return function(constructor: Function) {
        console.log(`Registering component: ${name}`);
        constructor.prototype.componentName = name;
    };
}

@component("MyComponent")
class MyComponent {
    render() {
        console.log("Rendering component");
    }
}

// Class decorator with metadata
function Entity(tableName: string) {
    return function<T extends { new(...args: any[]): {} }>(constructor: T) {
        return class extends constructor {
            tableName = tableName;
            save() {
                console.log(`Saving to table: ${tableName}`);
            }
        };
    };
}

@Entity("users")
class User {
    constructor(public name: string) {}
}

const user = new User("Alice");
```

### Method Decorators

```typescript
// Method decorator
function log(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;

    descriptor.value = function(...args: any[]) {
        console.log(`Calling ${propertyKey} with args:`, args);
        const result = originalMethod.apply(this, args);
        console.log(`Result:`, result);
        return result;
    };

    return descriptor;
}

class Calculator {
    @log
    add(x: number, y: number): number {
        return x + y;
    }
}

const calc = new Calculator();
calc.add(5, 3);
// Logs: Calling add with args: [5, 3]
// Logs: Result: 8

// Timing decorator
function measure(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;

    descriptor.value = function(...args: any[]) {
        const start = performance.now();
        const result = originalMethod.apply(this, args);
        const end = performance.now();
        console.log(`${propertyKey} took ${end - start}ms`);
        return result;
    };

    return descriptor;
}

class DataProcessor {
    @measure
    processLargeDataset(data: any[]): any[] {
        // Simulate processing
        return data.map(item => item * 2);
    }
}
```

### Property Decorators

```typescript
// Property decorator
function MinLength(length: number) {
    return function(target: any, propertyKey: string) {
        let value: string;

        const getter = function() {
            return value;
        };

        const setter = function(newVal: string) {
            if (newVal.length < length) {
                throw new Error(`${propertyKey} must be at least ${length} characters`);
            }
            value = newVal;
        };

        Object.defineProperty(target, propertyKey, {
            get: getter,
            set: setter,
            enumerable: true,
            configurable: true
        });
    };
}

class User {
    @MinLength(3)
    username: string;

    constructor(username: string) {
        this.username = username;
    }
}

const user1 = new User("Alice");       // OK
const user2 = new User("Al");          // Error: username must be at least 3 characters

// Read-only decorator
function readonly(target: any, propertyKey: string) {
    Object.defineProperty(target, propertyKey, {
        writable: false
    });
}

class Config {
    @readonly
    apiUrl: string = "https://api.example.com";
}
```

### Parameter Decorators

```typescript
// Parameter decorator
function required(target: any, propertyKey: string, parameterIndex: number) {
    const existingRequiredParameters: number[] =
        Reflect.getOwnMetadata("required", target, propertyKey) || [];

    existingRequiredParameters.push(parameterIndex);
    Reflect.defineMetadata("required", existingRequiredParameters, target, propertyKey);
}

class UserService {
    createUser(
        @required name: string,
        @required email: string,
        age?: number
    ): void {
        console.log(`Creating user: ${name}, ${email}, ${age}`);
    }
}

// Validation decorator
function validate(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;

    descriptor.value = function(...args: any[]) {
        const requiredParameters: number[] =
            Reflect.getOwnMetadata("required", target, propertyKey) || [];

        for (const parameterIndex of requiredParameters) {
            if (args[parameterIndex] === undefined || args[parameterIndex] === null) {
                throw new Error(`Parameter at index ${parameterIndex} is required`);
            }
        }

        return originalMethod.apply(this, args);
    };

    return descriptor;
}
```

### Decorator Composition

```typescript
// Multiple decorators on same target
function first() {
    console.log("first(): factory evaluated");
    return function(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("first(): called");
    };
}

function second() {
    console.log("second(): factory evaluated");
    return function(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("second(): called");
    };
}

class Example {
    @first()
    @second()
    method() {}
}

// Output:
// first(): factory evaluated
// second(): factory evaluated
// second(): called
// first(): called
```

### Practical Decorator Examples

```typescript
// API Route decorator
function Get(route: string) {
    return function(target: any, propertyKey: string) {
        Reflect.defineMetadata("route", route, target, propertyKey);
        Reflect.defineMetadata("method", "GET", target, propertyKey);
    };
}

function Post(route: string) {
    return function(target: any, propertyKey: string) {
        Reflect.defineMetadata("route", route, target, propertyKey);
        Reflect.defineMetadata("method", "POST", target, propertyKey);
    };
}

class UserController {
    @Get("/users")
    getUsers() {
        return ["user1", "user2"];
    }

    @Post("/users")
    createUser() {
        return { success: true };
    }
}

// Deprecation warning
function deprecated(message?: string) {
    return function(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        const original = descriptor.value;

        descriptor.value = function(...args: any[]) {
            console.warn(`Warning: ${propertyKey} is deprecated. ${message || ''}`);
            return original.apply(this, args);
        };

        return descriptor;
    };
}

class LegacyService {
    @deprecated("Use newMethod() instead")
    oldMethod() {
        console.log("Old method");
    }

    newMethod() {
        console.log("New method");
    }
}
```

---

## Practical Examples

### Complete Class Example

```typescript
interface IUser {
    id: number;
    username: string;
    email: string;
}

abstract class BaseEntity {
    protected createdAt: Date;
    protected updatedAt: Date;

    constructor() {
        this.createdAt = new Date();
        this.updatedAt = new Date();
    }

    abstract save(): Promise<void>;
    abstract delete(): Promise<void>;

    touch(): void {
        this.updatedAt = new Date();
    }
}

class User extends BaseEntity implements IUser {
    private static userCount: number = 0;

    constructor(
        public readonly id: number,
        public username: string,
        private _email: string
    ) {
        super();
        User.userCount++;
    }

    get email(): string {
        return this._email;
    }

    set email(value: string) {
        if (!value.includes("@")) {
            throw new Error("Invalid email");
        }
        this._email = value;
        this.touch();
    }

    async save(): Promise<void> {
        this.touch();
        console.log(`Saving user ${this.username}`);
        // Database save logic
    }

    async delete(): Promise<void> {
        console.log(`Deleting user ${this.username}`);
        User.userCount--;
        // Database delete logic
    }

    static getTotalUsers(): number {
        return User.userCount;
    }
}

const user = new User(1, "alice", "alice@example.com");
await user.save();
```

---

## Summary

| Feature | Purpose | Example |
|---------|---------|---------|
| Function Types | Type-safe functions | `(x: number) => number` |
| Generic Functions | Reusable with different types | `<T>(value: T) => T` |
| Classes | Object blueprints | `class User { }` |
| Inheritance | Extend classes | `class Admin extends User` |
| Access Modifiers | Control visibility | `private`, `protected`, `public` |
| Abstract Classes | Base class templates | `abstract class Shape` |
| Decorators | Modify class behavior | `@Component` |

## Best Practices

```typescript
// 1. Use parameter properties for concise constructors
class Good {
    constructor(public name: string, private age: number) {}
}

// 2. Prefer interfaces for public APIs
interface UserService {
    getUser(id: number): User;
}

// 3. Use readonly for immutable data
class Config {
    constructor(readonly apiKey: string) {}
}

// 4. Leverage access modifiers appropriately
class BankAccount {
    private balance: number;           // Hide implementation details
    public deposit(amount: number) {}  // Expose public API
}

// 5. Use generics for reusable components
class Repository<T> {
    find(id: number): T | undefined {
        return undefined;
    }
}
```

## Next Steps

You have completed the TypeScript module! You should now understand:
- TypeScript's type system and basic types
- Advanced types like interfaces, type aliases, and utility types
- Function typing and generic functions
- Object-oriented programming with classes
- Access modifiers and encapsulation
- Decorators for meta-programming

Continue practicing by:
- Building small TypeScript projects
- Converting existing JavaScript code to TypeScript
- Exploring TypeScript with frameworks (React, Angular, Node.js)
- Reading the [official TypeScript documentation](https://www.typescriptlang.org/docs/)

Return to [TypeScript Module README](../README.md)
