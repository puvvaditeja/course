# TypeScript Advanced Types

## Overview

Advanced types in TypeScript provide powerful ways to create reusable, flexible, and type-safe code. This guide covers type aliases, interfaces, type casting, and utility types that enable sophisticated type manipulation.

---

## Type Aliases and Interfaces

### Type Aliases

Type aliases create custom names for types, making code more readable and reusable.

#### Basic Type Aliases

```typescript
// Primitive type alias
type ID = string | number;
type Age = number;
type IsActive = boolean;

let userId: ID = "abc123";
userId = 456;                          // OK - ID can be string or number

// Function type alias
type MathOperation = (x: number, y: number) => number;

const add: MathOperation = (x, y) => x + y;
const multiply: MathOperation = (x, y) => x * y;

// Object type alias
type Point = {
    x: number;
    y: number;
};

const origin: Point = { x: 0, y: 0 };
```

#### Complex Type Aliases

```typescript
// Union type alias
type Status = "pending" | "approved" | "rejected";
type Result = Success | Failure;

// Tuple type alias
type Coordinate = [number, number];
type RGB = [number, number, number];

const position: Coordinate = [10, 20];
const red: RGB = [255, 0, 0];

// Nested type alias
type User = {
    id: number;
    profile: {
        name: string;
        email: string;
        address: {
            street: string;
            city: string;
            zipCode: string;
        };
    };
};
```

### Interfaces

Interfaces define the structure of objects and classes.

#### Basic Interfaces

```typescript
// Basic interface
interface User {
    id: number;
    name: string;
    email: string;
}

const user: User = {
    id: 1,
    name: "Alice",
    email: "alice@example.com"
};

// Optional properties
interface Product {
    id: number;
    name: string;
    description?: string;              // Optional
    price: number;
}

// Readonly properties
interface Config {
    readonly apiKey: string;
    readonly apiUrl: string;
    timeout: number;
}

const config: Config = {
    apiKey: "secret123",
    apiUrl: "https://api.example.com",
    timeout: 5000
};

config.timeout = 10000;                // OK
config.apiKey = "newkey";              // Error: Cannot assign to 'apiKey' because it is a read-only property
```

#### Method Signatures

```typescript
// Interface with methods
interface Calculator {
    add(x: number, y: number): number;
    subtract(x: number, y: number): number;
}

const calc: Calculator = {
    add(x, y) {
        return x + y;
    },
    subtract(x, y) {
        return x - y;
    }
};

// Alternative method syntax
interface Logger {
    log: (message: string) => void;
    error: (message: string) => void;
}

const console: Logger = {
    log: (message) => console.log(message),
    error: (message) => console.error(message)
};
```

#### Index Signatures

```typescript
// String index signature
interface StringMap {
    [key: string]: string;
}

const translations: StringMap = {
    hello: "Hola",
    goodbye: "Adiós",
    thanks: "Gracias"
};

// Number index signature
interface NumberArray {
    [index: number]: number;
}

const fibonacci: NumberArray = [0, 1, 1, 2, 3, 5, 8];

// Combined index signature
interface MixedMap {
    [key: string]: string | number;
    count: number;                     // Must match index signature type
}
```

### Extending Interfaces

```typescript
// Basic extension
interface Animal {
    name: string;
    age: number;
}

interface Dog extends Animal {
    breed: string;
    bark(): void;
}

const myDog: Dog = {
    name: "Buddy",
    age: 3,
    breed: "Golden Retriever",
    bark() {
        console.log("Woof!");
    }
};

// Multiple inheritance
interface Flyable {
    fly(): void;
    altitude: number;
}

interface Swimmable {
    swim(): void;
    depth: number;
}

interface Duck extends Animal, Flyable, Swimmable {
    quack(): void;
}

const duck: Duck = {
    name: "Donald",
    age: 2,
    fly() { console.log("Flying"); },
    altitude: 100,
    swim() { console.log("Swimming"); },
    depth: 5,
    quack() { console.log("Quack!"); }
};
```

### Type Aliases vs Interfaces

```typescript
// Key differences

// 1. Interfaces can be extended
interface Person {
    name: string;
}

interface Employee extends Person {
    employeeId: number;
}

// 2. Type aliases can represent unions, primitives
type ID = string | number;             // Can't do this with interface
type Name = string;                    // Can't do this with interface

// 3. Interface declaration merging
interface Window {
    title: string;
}

interface Window {
    tabs: number;
}

// Both declarations merge into one
const w: Window = {
    title: "Browser",
    tabs: 5
};

// 4. Type aliases can use computed properties
type Keys = "firstName" | "lastName";
type Person = {
    [K in Keys]: string;
};

// When to use what:
// - Use interfaces for object shapes and class contracts
// - Use type aliases for unions, intersections, primitives, tuples
```

### Intersection Types

Combine multiple types into one:

```typescript
// Intersection with type aliases
type Timestamped = {
    createdAt: Date;
    updatedAt: Date;
};

type Tagged = {
    tags: string[];
};

type Article = {
    id: number;
    title: string;
    content: string;
} & Timestamped & Tagged;

const article: Article = {
    id: 1,
    title: "TypeScript Guide",
    content: "...",
    createdAt: new Date(),
    updatedAt: new Date(),
    tags: ["typescript", "programming"]
};

// Intersection with interfaces
interface Name {
    firstName: string;
    lastName: string;
}

interface Contact {
    email: string;
    phone: string;
}

type Person = Name & Contact;

const person: Person = {
    firstName: "John",
    lastName: "Doe",
    email: "john@example.com",
    phone: "555-1234"
};
```

---

## Type Casting

Type casting (type assertion) tells TypeScript to treat a value as a specific type.

### Basic Casting

```typescript
// Using 'as' syntax (recommended)
let someValue: unknown = "this is a string";
let strLength: number = (someValue as string).length;

// Angle bracket syntax (not usable in TSX/JSX)
let strLength2: number = (<string>someValue).length;

// Casting from any
let input: any = document.getElementById("myInput");
let inputElement = input as HTMLInputElement;
inputElement.value = "Hello";
```

### DOM Type Casting

```typescript
// Type casting DOM elements
const button = document.querySelector(".btn") as HTMLButtonElement;
button.disabled = true;

const input = document.getElementById("email") as HTMLInputElement;
input.value = "user@example.com";

const canvas = document.querySelector("canvas") as HTMLCanvasElement;
const ctx = canvas.getContext("2d");

// Null handling with casting
const element = document.getElementById("app");
if (element) {
    const div = element as HTMLDivElement;
    div.style.color = "blue";
}

// Non-null assertion operator
const element2 = document.getElementById("app")!; // Assert it's not null
element2.style.color = "blue";
```

### Double Assertion

Use with extreme caution:

```typescript
// Double assertion - forces incompatible types
let unusual = ("hello" as unknown) as number;

// More practical example
interface Cat {
    meow(): void;
}

interface Dog {
    bark(): void;
}

let pet = {} as Cat;
// Later, force to Dog (dangerous!)
let dog = (pet as unknown) as Dog;

// Better: use proper type guards instead
```

### Const Assertions

```typescript
// Without const assertion
let config = {
    endpoint: "https://api.example.com",
    timeout: 5000
};
// Type: { endpoint: string; timeout: number }

// With const assertion
let config2 = {
    endpoint: "https://api.example.com",
    timeout: 5000
} as const;
// Type: { readonly endpoint: "https://api.example.com"; readonly timeout: 5000 }

// Array const assertion
const colors = ["red", "green", "blue"] as const;
// Type: readonly ["red", "green", "blue"]

type Color = typeof colors[number];    // "red" | "green" | "blue"

// Enum-like pattern with const assertion
const Direction = {
    UP: "UP",
    DOWN: "DOWN",
    LEFT: "LEFT",
    RIGHT: "RIGHT"
} as const;

type Direction = typeof Direction[keyof typeof Direction];
// Type: "UP" | "DOWN" | "LEFT" | "RIGHT"
```

### Type Predicates

Custom type guards for type narrowing:

```typescript
// Type predicate function
interface Fish {
    swim(): void;
}

interface Bird {
    fly(): void;
}

// Type guard with type predicate
function isFish(pet: Fish | Bird): pet is Fish {
    return (pet as Fish).swim !== undefined;
}

function movePet(pet: Fish | Bird) {
    if (isFish(pet)) {
        pet.swim();                    // TypeScript knows pet is Fish
    } else {
        pet.fly();                     // TypeScript knows pet is Bird
    }
}

// Another example
interface Cat {
    meow(): void;
}

interface Dog {
    bark(): void;
}

function isDog(animal: Cat | Dog): animal is Dog {
    return (animal as Dog).bark !== undefined;
}

function makeSound(animal: Cat | Dog) {
    if (isDog(animal)) {
        animal.bark();
    } else {
        animal.meow();
    }
}
```

---

## Utility Types

TypeScript provides built-in utility types for common type transformations.

### Partial<T>

Makes all properties optional:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    age: number;
}

// All properties become optional
type PartialUser = Partial<User>;
// Equivalent to: { id?: number; name?: string; email?: string; age?: number; }

// Use case: Update functions
function updateUser(id: number, updates: Partial<User>): User {
    const user = getUserById(id);
    return { ...user, ...updates };
}

updateUser(1, { name: "Alice" });      // OK - only update name
updateUser(1, { email: "new@email.com", age: 30 }); // OK
```

### Required<T>

Makes all properties required:

```typescript
interface Props {
    title?: string;
    description?: string;
    visible?: boolean;
}

// All properties become required
type RequiredProps = Required<Props>;
// Equivalent to: { title: string; description: string; visible: boolean; }

function render(props: RequiredProps) {
    // All properties are guaranteed to exist
    console.log(props.title.toUpperCase());
}
```

### Readonly<T>

Makes all properties readonly:

```typescript
interface Mutable {
    id: number;
    name: string;
}

type Immutable = Readonly<Mutable>;
// Equivalent to: { readonly id: number; readonly name: string; }

const user: Immutable = { id: 1, name: "Alice" };
user.name = "Bob";                     // Error: Cannot assign to 'name' because it is a read-only property

// Use case: Prevent mutations
function process(data: Readonly<Mutable>) {
    // Can read but not modify
    console.log(data.name);
    data.name = "Bob";                 // Error
}
```

### Record<K, T>

Creates an object type with specific keys and value type:

```typescript
// Record<Keys, ValueType>
type Roles = "admin" | "user" | "guest";
type Permissions = Record<Roles, string[]>;

const permissions: Permissions = {
    admin: ["read", "write", "delete"],
    user: ["read", "write"],
    guest: ["read"]
};

// Another example
type PageInfo = Record<string, { title: string; url: string }>;

const pages: PageInfo = {
    home: { title: "Home", url: "/" },
    about: { title: "About", url: "/about" },
    contact: { title: "Contact", url: "/contact" }
};

// Enum keys with Record
enum Status {
    Active = "ACTIVE",
    Inactive = "INACTIVE",
    Pending = "PENDING"
}

type StatusMessages = Record<Status, string>;

const messages: StatusMessages = {
    [Status.Active]: "User is active",
    [Status.Inactive]: "User is inactive",
    [Status.Pending]: "User activation pending"
};
```

### Pick<T, K>

Creates a type by picking specific properties from another type:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    password: string;
    age: number;
}

// Pick only id and name
type UserPreview = Pick<User, "id" | "name">;
// Equivalent to: { id: number; name: string; }

// Use case: API responses
function getUserPreview(id: number): UserPreview {
    return {
        id: 1,
        name: "Alice"
    };
}

// Pick multiple properties
type UserProfile = Pick<User, "id" | "name" | "email" | "age">;
```

### Omit<T, K>

Creates a type by omitting specific properties:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    password: string;
}

// Omit password
type SafeUser = Omit<User, "password">;
// Equivalent to: { id: number; name: string; email: string; }

// Omit multiple properties
type PublicUser = Omit<User, "password" | "email">;
// Equivalent to: { id: number; name: string; }

// Use case: API responses
function getPublicUser(user: User): PublicUser {
    const { password, email, ...publicUser } = user;
    return publicUser;
}
```

### Exclude<T, U>

Excludes types from a union:

```typescript
// Exclude specific types
type AllTypes = string | number | boolean;
type StringAndNumber = Exclude<AllTypes, boolean>;
// Type: string | number

// Exclude multiple types
type OnlyString = Exclude<AllTypes, number | boolean>;
// Type: string

// Practical example
type Status = "pending" | "approved" | "rejected" | "cancelled";
type ActiveStatus = Exclude<Status, "rejected" | "cancelled">;
// Type: "pending" | "approved"

// Exclude null and undefined
type MaybeString = string | null | undefined;
type DefiniteString = Exclude<MaybeString, null | undefined>;
// Type: string
```

### Extract<T, U>

Extracts types from a union:

```typescript
// Extract specific types
type AllTypes = string | number | boolean;
type OnlyNumber = Extract<AllTypes, number>;
// Type: number

// Extract multiple types
type StringOrBoolean = Extract<AllTypes, string | boolean>;
// Type: string | boolean

// Practical example
type Events = "click" | "mouseover" | "keydown" | "keyup" | "scroll";
type KeyboardEvents = Extract<Events, `key${string}`>;
// Type: "keydown" | "keyup"
```

### NonNullable<T>

Removes null and undefined from a type:

```typescript
type MaybeString = string | null | undefined;
type DefiniteString = NonNullable<MaybeString>;
// Type: string

type MaybeUser = User | null | undefined;
type DefiniteUser = NonNullable<MaybeUser>;
// Type: User

// Use case: Filter arrays
function compact<T>(array: T[]): NonNullable<T>[] {
    return array.filter((item): item is NonNullable<T> => item != null);
}

const mixed = [1, null, 2, undefined, 3];
const numbers = compact(mixed);        // Type: number[]
```

### ReturnType<T>

Extracts the return type of a function:

```typescript
// Get return type of function
function getUser() {
    return { id: 1, name: "Alice", email: "alice@example.com" };
}

type User = ReturnType<typeof getUser>;
// Type: { id: number; name: string; email: string; }

// Another example
function calculate(x: number, y: number) {
    return { sum: x + y, product: x * y };
}

type CalcResult = ReturnType<typeof calculate>;
// Type: { sum: number; product: number; }

// With generic functions
function createArray<T>(item: T): T[] {
    return [item];
}

type StringArray = ReturnType<typeof createArray<string>>;
// Type: string[]
```

### Parameters<T>

Extracts parameter types of a function as a tuple:

```typescript
function createUser(name: string, age: number, email: string) {
    return { name, age, email };
}

type CreateUserParams = Parameters<typeof createUser>;
// Type: [name: string, age: number, email: string]

// Use parameters in another function
function validateAndCreateUser(...args: CreateUserParams) {
    const [name, age, email] = args;
    if (age < 18) throw new Error("Must be 18+");
    return createUser(name, age, email);
}

// Another example
type FetchParams = Parameters<typeof fetch>;
// Type: [input: RequestInfo | URL, init?: RequestInit]
```

### Awaited<T>

Unwraps Promise types:

```typescript
// Simple Promise
type AsyncString = Awaited<Promise<string>>;
// Type: string

// Nested Promises
type DeepAsync = Awaited<Promise<Promise<number>>>;
// Type: number

// Practical use
async function fetchUser(): Promise<{ id: number; name: string }> {
    return { id: 1, name: "Alice" };
}

type User = Awaited<ReturnType<typeof fetchUser>>;
// Type: { id: number; name: string }

// With union types
type MaybeAsync = string | Promise<string>;
type Resolved = Awaited<MaybeAsync>;
// Type: string
```

---

## Advanced Type Patterns

### Mapped Types

Transform properties of existing types:

```typescript
// Make all properties optional
type Optional<T> = {
    [P in keyof T]?: T[P];
};

// Make all properties nullable
type Nullable<T> = {
    [P in keyof T]: T[P] | null;
};

// Make all properties readonly and optional
type ReadonlyOptional<T> = {
    readonly [P in keyof T]?: T[P];
};

interface User {
    id: number;
    name: string;
    email: string;
}

type NullableUser = Nullable<User>;
// Type: { id: number | null; name: string | null; email: string | null; }
```

### Conditional Types

Types that depend on conditions:

```typescript
// Basic conditional type
type IsString<T> = T extends string ? true : false;

type A = IsString<string>;             // true
type B = IsString<number>;             // false

// Extract array element type
type ArrayElement<T> = T extends (infer E)[] ? E : T;

type Num = ArrayElement<number[]>;     // number
type Str = ArrayElement<string>;       // string

// Unwrap Promise
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type ResolvedString = UnwrapPromise<Promise<string>>; // string
type PlainNumber = UnwrapPromise<number>; // number
```

### Template Literal Types

```typescript
// String template types
type Color = "red" | "green" | "blue";
type HexColor = `#${string}`;
type ColorClass = `${Color}-color`;
// Type: "red-color" | "green-color" | "blue-color"

// Combining template literals
type Size = "small" | "medium" | "large";
type ButtonClass = `btn-${Size}-${Color}`;
// Type: "btn-small-red" | "btn-small-green" | ... (9 combinations)

// Event names
type EventName = "click" | "focus" | "blur";
type EventHandler = `on${Capitalize<EventName>}`;
// Type: "onClick" | "onFocus" | "onBlur"
```

---

## Practical Examples

### API Response Types

```typescript
// Generic API response
interface ApiResponse<T> {
    success: boolean;
    data?: T;
    error?: string;
    timestamp: Date;
}

// Use with different data types
type UserResponse = ApiResponse<User>;
type ProductsResponse = ApiResponse<Product[]>;

// Utility to extract data type
type ExtractData<T> = T extends ApiResponse<infer U> ? U : never;

type UserData = ExtractData<UserResponse>; // User
```

### Form State Management

```typescript
interface FormState<T> {
    values: T;
    errors: Partial<Record<keyof T, string>>;
    touched: Partial<Record<keyof T, boolean>>;
    isValid: boolean;
    isSubmitting: boolean;
}

interface LoginForm {
    email: string;
    password: string;
}

type LoginFormState = FormState<LoginForm>;

const formState: LoginFormState = {
    values: { email: "", password: "" },
    errors: { email: "Required" },
    touched: { email: true },
    isValid: false,
    isSubmitting: false
};
```

---

## Summary

| Feature | Purpose | Example |
|---------|---------|---------|
| Type Alias | Custom type names | `type ID = string \| number` |
| Interface | Object structure | `interface User { name: string }` |
| Casting | Type assertion | `value as string` |
| Partial | All props optional | `Partial<User>` |
| Required | All props required | `Required<Props>` |
| Readonly | All props readonly | `Readonly<Config>` |
| Pick | Select properties | `Pick<User, "id" \| "name">` |
| Omit | Exclude properties | `Omit<User, "password">` |
| Record | Key-value mapping | `Record<string, number>` |

## Next Topic

Continue to [Functions and Classes](./05-functions-and-classes.md) to learn about TypeScript functions, classes, access modifiers, and decorators.
