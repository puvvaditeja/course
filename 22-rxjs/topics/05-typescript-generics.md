# TypeScript Generics and Type Utilities

## Introduction

TypeScript's type system provides powerful tools for creating reusable, type-safe code. Understanding generics and utility types is essential when working with RxJS, as Observables heavily rely on generic type parameters.

---

## Basic Generics

Generics allow you to write flexible, reusable code that works with multiple types while maintaining type safety.

### What are Generics?

Generics are like variables for types - they allow you to parameterize types just as functions parameterize values.

```typescript
// Without generics - repetitive code
function identityNumber(value: number): number {
  return value;
}

function identityString(value: string): string {
  return value;
}

// With generics - one function for all types
function identity<T>(value: T): T {
  return value;
}

// Usage
const num = identity<number>(42);      // T = number
const str = identity<string>("hello"); // T = string
const bool = identity<boolean>(true);  // T = boolean

// Type inference - TypeScript infers T
const inferredNum = identity(42);      // T inferred as number
const inferredStr = identity("hello"); // T inferred as string
```

### Generic Functions

```typescript
// Generic function with type constraint
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = {
  name: 'Alice',
  age: 30,
  email: 'alice@example.com'
};

const name = getProperty(person, 'name');    // string
const age = getProperty(person, 'age');      // number
// const invalid = getProperty(person, 'invalid'); // Error!

// Multiple type parameters
function merge<T, U>(obj1: T, obj2: U): T & U {
  return { ...obj1, ...obj2 };
}

const merged = merge(
  { name: 'Alice' },
  { age: 30 }
);
// merged: { name: string; age: number; }
```

### Generic Interfaces

```typescript
interface Container<T> {
  value: T;
  getValue(): T;
  setValue(value: T): void;
}

class StringContainer implements Container<string> {
  constructor(public value: string) {}

  getValue(): string {
    return this.value;
  }

  setValue(value: string): void {
    this.value = value;
  }
}

class NumberContainer implements Container<number> {
  constructor(public value: number) {}

  getValue(): number {
    return this.value;
  }

  setValue(value: number): void {
    this.value = value;
  }
}

// Generic class
class GenericContainer<T> implements Container<T> {
  constructor(public value: T) {}

  getValue(): T {
    return this.value;
  }

  setValue(value: T): void {
    this.value = value;
  }
}

const stringContainer = new GenericContainer<string>('hello');
const numberContainer = new GenericContainer<number>(42);
```

### RxJS Generic Examples

```typescript
import { Observable, of } from 'rxjs';
import { map, filter } from 'rxjs/operators';

// Observable is generic
const numbers$: Observable<number> = of(1, 2, 3, 4, 5);
const strings$: Observable<string> = of('a', 'b', 'c');

// Type flows through operators
numbers$.pipe(
  map(n => n * 2),           // Observable<number>
  map(n => n.toString())     // Observable<string>
).subscribe(str => {
  console.log(str.toUpperCase()); // str is string
});

// Generic function with Observable
function wrapInObservable<T>(value: T): Observable<T> {
  return of(value);
}

const num$ = wrapInObservable(42);        // Observable<number>
const str$ = wrapInObservable('hello');   // Observable<string>

// Generic class with Observable
class DataService<T> {
  private data$: Observable<T[]>;

  constructor(private endpoint: string) {
    this.data$ = this.http.get<T[]>(endpoint);
  }

  getData(): Observable<T[]> {
    return this.data$;
  }

  getById(id: number): Observable<T | undefined> {
    return this.data$.pipe(
      map(items => items.find(item => item['id'] === id))
    );
  }
}

interface User {
  id: number;
  name: string;
}

const userService = new DataService<User>('/api/users');
userService.getData().subscribe(users => {
  // users is User[]
  console.log(users[0].name);
});
```

### Generic Constraints

```typescript
// Constraint: T must have a length property
function logLength<T extends { length: number }>(item: T): void {
  console.log(item.length);
}

logLength('hello');        // OK - string has length
logLength([1, 2, 3]);      // OK - array has length
// logLength(42);          // Error - number doesn't have length

// Constraint: T must extend base class
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

class Dog extends Animal {
  bark(): void {
    console.log('Woof!');
  }
}

function createAnimal<T extends Animal>(AnimalClass: new (name: string) => T, name: string): T {
  return new AnimalClass(name);
}

const dog = createAnimal(Dog, 'Buddy');
dog.bark(); // OK

// Observable constraint
function fromArray<T>(items: T[]): Observable<T> {
  return new Observable(subscriber => {
    items.forEach(item => subscriber.next(item));
    subscriber.complete();
  });
}

const numbers$ = fromArray([1, 2, 3]);    // Observable<number>
const strings$ = fromArray(['a', 'b']);   // Observable<string>
```

---

## Keyof Type Operator

The `keyof` operator takes an object type and produces a string or numeric literal union of its keys.

### Basic Usage

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

type UserKeys = keyof User;
// UserKeys = 'id' | 'name' | 'email' | 'age'

// Use in function parameters
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user: User = {
  id: 1,
  name: 'Alice',
  email: 'alice@example.com',
  age: 30
};

const name = getProperty(user, 'name');    // string
const age = getProperty(user, 'age');      // number
// const invalid = getProperty(user, 'invalid'); // Error!
```

### Practical Examples

```typescript
// Type-safe object update
function updateProperty<T, K extends keyof T>(
  obj: T,
  key: K,
  value: T[K]
): T {
  return { ...obj, [key]: value };
}

const updatedUser = updateProperty(user, 'age', 31);
// const invalid = updateProperty(user, 'age', '31'); // Error! Type mismatch

// Create a type with specific keys
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};

type UserNameAndEmail = Pick<User, 'name' | 'email'>;
// { name: string; email: string; }

// Observable value picker
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

function pluckProperty<T, K extends keyof T>(
  source$: Observable<T>,
  key: K
): Observable<T[K]> {
  return source$.pipe(map(obj => obj[key]));
}

const user$ = of({ id: 1, name: 'Alice', age: 30 });
const name$ = pluckProperty(user$, 'name'); // Observable<string>
const age$ = pluckProperty(user$, 'age');   // Observable<number>
```

### keyof with Index Signatures

```typescript
interface StringMap {
  [key: string]: string;
}

type StringMapKeys = keyof StringMap; // string | number
// Note: number is included because obj[0] is same as obj['0']

interface NumberMap {
  [key: number]: string;
}

type NumberMapKeys = keyof NumberMap; // number

// Real-world example
interface Config {
  apiUrl: string;
  timeout: number;
  retries: number;
  [key: string]: string | number;
}

function getConfig<K extends keyof Config>(key: K): Config[K] {
  const config: Config = {
    apiUrl: 'https://api.example.com',
    timeout: 5000,
    retries: 3
  };
  return config[key];
}

const apiUrl = getConfig('apiUrl');    // string
const timeout = getConfig('timeout');  // number
```

---

## Readonly Type Modifier

The `readonly` modifier prevents reassignment of properties.

### Readonly Properties

```typescript
interface User {
  readonly id: number;
  name: string;
  readonly createdAt: Date;
}

const user: User = {
  id: 1,
  name: 'Alice',
  createdAt: new Date()
};

user.name = 'Bob';           // OK
// user.id = 2;              // Error! Cannot assign to 'id'
// user.createdAt = new Date(); // Error! Cannot assign to 'createdAt'
```

### Readonly Arrays

```typescript
// Regular array
const numbers: number[] = [1, 2, 3];
numbers.push(4);           // OK
numbers[0] = 10;           // OK

// Readonly array
const readonlyNumbers: readonly number[] = [1, 2, 3];
// readonlyNumbers.push(4);    // Error! Property 'push' does not exist
// readonlyNumbers[0] = 10;    // Error! Index signature is readonly

// ReadonlyArray generic
const readonlyArray: ReadonlyArray<number> = [1, 2, 3];
// readonlyArray.push(4);      // Error!

// Use case: prevent mutation
function sum(numbers: readonly number[]): number {
  // numbers.push(0); // Error! Can't modify
  return numbers.reduce((acc, n) => acc + n, 0);
}
```

### Readonly in RxJS

```typescript
import { BehaviorSubject, Observable } from 'rxjs';

interface AppState {
  readonly user: User | null;
  readonly loading: boolean;
  readonly error: string | null;
}

class Store {
  private state$ = new BehaviorSubject<AppState>({
    user: null,
    loading: false,
    error: null
  });

  // Return readonly observable
  getState(): Observable<Readonly<AppState>> {
    return this.state$.asObservable();
  }

  // State can only be updated through actions
  setUser(user: User): void {
    this.state$.next({
      ...this.state$.value,
      user
    });
  }
}

// Usage
const store = new Store();
store.getState().subscribe(state => {
  // state.user = null; // Error! Cannot modify readonly property
  console.log(state.user);
});
```

### Deep Readonly

```typescript
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object
    ? DeepReadonly<T[P]>
    : T[P];
};

interface Config {
  database: {
    host: string;
    port: number;
    credentials: {
      username: string;
      password: string;
    };
  };
}

const config: DeepReadonly<Config> = {
  database: {
    host: 'localhost',
    port: 5432,
    credentials: {
      username: 'admin',
      password: 'secret'
    }
  }
};

// config.database.host = 'newhost';                    // Error!
// config.database.credentials.password = 'newpass';    // Error!
```

---

## Utility Types

TypeScript provides several built-in utility types for common type transformations.

### Partial<T>

Makes all properties optional.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// All properties optional
type PartialUser = Partial<User>;
// { id?: number; name?: string; email?: string; age?: number; }

// Use case: Update functions
function updateUser(id: number, updates: Partial<User>): User {
  const currentUser = getUserById(id);
  return { ...currentUser, ...updates };
}

updateUser(1, { name: 'Bob' });           // OK
updateUser(1, { email: 'bob@test.com' }); // OK
updateUser(1, { name: 'Bob', age: 31 });  // OK

// RxJS example
import { BehaviorSubject } from 'rxjs';

class UserStore {
  private user$ = new BehaviorSubject<User>({
    id: 1,
    name: 'Alice',
    email: 'alice@example.com',
    age: 30
  });

  updateUser(updates: Partial<User>): void {
    this.user$.next({
      ...this.user$.value,
      ...updates
    });
  }
}

const store = new UserStore();
store.updateUser({ age: 31 });        // Update only age
store.updateUser({ name: 'Alicia' }); // Update only name
```

### Required<T>

Makes all properties required.

```typescript
interface Config {
  apiUrl?: string;
  timeout?: number;
  retries?: number;
}

// All properties required
type RequiredConfig = Required<Config>;
// { apiUrl: string; timeout: number; retries: number; }

function initializeApp(config: Required<Config>): void {
  // All properties guaranteed to exist
  console.log(config.apiUrl);
  console.log(config.timeout);
  console.log(config.retries);
}

const defaultConfig: Config = {
  apiUrl: 'https://api.example.com'
};

const fullConfig: Required<Config> = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
  retries: 3
};

// initializeApp(defaultConfig); // Error! Missing properties
initializeApp(fullConfig);       // OK
```

### Pick<T, K>

Creates a type by picking specific properties.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
  address: string;
  phone: string;
}

// Pick only specific properties
type UserSummary = Pick<User, 'id' | 'name' | 'email'>;
// { id: number; name: string; email: string; }

type UserContact = Pick<User, 'email' | 'phone'>;
// { email: string; phone: string; }

// Use case: API responses
function getUserSummary(id: number): Observable<UserSummary> {
  return this.http.get<User>(`/api/users/${id}`).pipe(
    map(user => ({
      id: user.id,
      name: user.name,
      email: user.email
    }))
  );
}
```

### Omit<T, K>

Creates a type by omitting specific properties.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  createdAt: Date;
}

// Omit sensitive fields
type PublicUser = Omit<User, 'password'>;
// { id: number; name: string; email: string; createdAt: Date; }

type UserInput = Omit<User, 'id' | 'createdAt'>;
// { name: string; email: string; password: string; }

// Use case: Create user
function createUser(input: UserInput): Observable<User> {
  return this.http.post<User>('/api/users', input);
}

createUser({
  name: 'Alice',
  email: 'alice@example.com',
  password: 'secret'
}); // OK - id and createdAt will be generated by server
```

### Record<K, T>

Creates an object type with specified keys and value type.

```typescript
type Role = 'admin' | 'user' | 'guest';

// Map roles to permissions
type Permissions = Record<Role, string[]>;
// {
//   admin: string[];
//   user: string[];
//   guest: string[];
// }

const permissions: Permissions = {
  admin: ['read', 'write', 'delete'],
  user: ['read', 'write'],
  guest: ['read']
};

// Flexible key type
type StatusCode = 200 | 404 | 500;
type StatusMessages = Record<StatusCode, string>;

const messages: StatusMessages = {
  200: 'Success',
  404: 'Not Found',
  500: 'Internal Server Error'
};

// With string keys
type Cache = Record<string, any>;

const cache: Cache = {
  user1: { name: 'Alice' },
  user2: { name: 'Bob' },
  config: { timeout: 5000 }
};
```

### Exclude<T, U>

Excludes types from a union.

```typescript
type AllTypes = 'a' | 'b' | 'c' | 'd';
type ExcludedTypes = Exclude<AllTypes, 'a' | 'c'>;
// 'b' | 'd'

// Practical example
type Primitive = string | number | boolean | null | undefined;
type NonNullablePrimitive = Exclude<Primitive, null | undefined>;
// string | number | boolean

// Function parameter type exclusion
type AllowedMethods = 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH';
type ReadOnlyMethods = Exclude<AllowedMethods, 'POST' | 'PUT' | 'DELETE' | 'PATCH'>;
// 'GET'
```

### Extract<T, U>

Extracts types from a union.

```typescript
type AllTypes = 'a' | 'b' | 'c' | 'd';
type ExtractedTypes = Extract<AllTypes, 'a' | 'c'>;
// 'a' | 'c'

// Practical example
type Shapes = 'circle' | 'square' | 'triangle' | 'rectangle';
type RoundShapes = Extract<Shapes, 'circle'>;
// 'circle'

type AngularShapes = Extract<Shapes, 'square' | 'triangle' | 'rectangle'>;
// 'square' | 'triangle' | 'rectangle'
```

### NonNullable<T>

Removes null and undefined from a type.

```typescript
type NullableString = string | null | undefined;
type NonNullableString = NonNullable<NullableString>;
// string

// Use case: Filter null values
import { Observable } from 'rxjs';
import { filter, map } from 'rxjs/operators';

function removeNulls<T>(
  source$: Observable<T | null | undefined>
): Observable<NonNullable<T>> {
  return source$.pipe(
    filter((value): value is NonNullable<T> => value != null)
  );
}

const numbers$ = of(1, null, 2, undefined, 3);
const nonNullNumbers$ = removeNulls(numbers$);
// Observable<number>
```

### ReturnType<T>

Extracts the return type of a function.

```typescript
function getUser() {
  return {
    id: 1,
    name: 'Alice',
    email: 'alice@example.com'
  };
}

type User = ReturnType<typeof getUser>;
// { id: number; name: string; email: string; }

// With generics
function createObservable<T>(value: T) {
  return of(value);
}

type ObservableType = ReturnType<typeof createObservable<string>>;
// Observable<string>

// Practical use
class UserService {
  getUsers() {
    return this.http.get<User[]>('/api/users');
  }
}

type GetUsersReturnType = ReturnType<UserService['getUsers']>;
// Observable<User[]>
```

---

## Array Generics

Working with typed arrays in TypeScript and RxJS.

### Basic Array Types

```typescript
// Array syntax
const numbers: number[] = [1, 2, 3, 4, 5];
const strings: string[] = ['a', 'b', 'c'];

// Generic syntax
const numbers2: Array<number> = [1, 2, 3, 4, 5];
const strings2: Array<string> = ['a', 'b', 'c'];

// Array of objects
interface User {
  id: number;
  name: string;
}

const users: User[] = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' }
];

// Readonly arrays
const readonlyNumbers: readonly number[] = [1, 2, 3];
const readonlyUsers: ReadonlyArray<User> = users;
```

### Array Utility Functions with Generics

```typescript
// Generic filter
function filterBy<T, K extends keyof T>(
  array: T[],
  key: K,
  value: T[K]
): T[] {
  return array.filter(item => item[key] === value);
}

const users: User[] = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Alice' }
];

const aliceUsers = filterBy(users, 'name', 'Alice');
// User[]

// Generic map
function mapProperty<T, K extends keyof T>(
  array: T[],
  key: K
): T[K][] {
  return array.map(item => item[key]);
}

const names = mapProperty(users, 'name');
// string[]

const ids = mapProperty(users, 'id');
// number[]

// Generic find
function findBy<T, K extends keyof T>(
  array: T[],
  key: K,
  value: T[K]
): T | undefined {
  return array.find(item => item[key] === value);
}

const user = findBy(users, 'id', 2);
// User | undefined
```

### Arrays in RxJS

```typescript
import { of, from } from 'rxjs';
import { map, filter, reduce, toArray } from 'rxjs/operators';

// From array to Observable
const numbers$ = from([1, 2, 3, 4, 5]);
// Observable<number>

// Map array values
numbers$.pipe(
  map(n => n * 2)
).subscribe(console.log);

// Filter array values
numbers$.pipe(
  filter(n => n % 2 === 0)
).subscribe(console.log);

// Collect to array
numbers$.pipe(
  filter(n => n > 2),
  toArray()
).subscribe(arr => console.log(arr));
// [3, 4, 5]

// Working with object arrays
interface Product {
  id: number;
  name: string;
  price: number;
}

const products$: Observable<Product[]> = of([
  { id: 1, name: 'Laptop', price: 999 },
  { id: 2, name: 'Mouse', price: 25 },
  { id: 3, name: 'Keyboard', price: 75 }
]);

// Filter expensive products
const expensiveProducts$ = products$.pipe(
  map(products => products.filter(p => p.price > 50))
);
// Observable<Product[]>

// Extract property
const productNames$ = products$.pipe(
  map(products => products.map(p => p.name))
);
// Observable<string[]>
```

### Tuple Types

```typescript
// Tuple: fixed-length array with specific types
type Point = [number, number];
type RGB = [number, number, number];
type NameAndAge = [string, number];

const point: Point = [10, 20];
const color: RGB = [255, 128, 0];
const person: NameAndAge = ['Alice', 30];

// Tuple in Observable
import { of } from 'rxjs';

const coordinates$: Observable<Point> = of([10, 20]);

coordinates$.subscribe(([x, y]) => {
  console.log(`X: ${x}, Y: ${y}`);
});

// Tuple with optional elements
type Tuple = [string, number?];
const tuple1: Tuple = ['hello'];
const tuple2: Tuple = ['hello', 42];

// Rest elements in tuples
type StringNumberBooleans = [string, number, ...boolean[]];
const snb: StringNumberBooleans = ['hello', 42, true, false, true];
```

---

## Combining Concepts

### Advanced RxJS Patterns with TypeScript

```typescript
import { Observable, BehaviorSubject } from 'rxjs';
import { map, filter } from 'rxjs/operators';

// Generic store with utility types
interface Entity {
  id: number;
}

class EntityStore<T extends Entity> {
  private entities$ = new BehaviorSubject<ReadonlyArray<T>>([]);

  // Get all entities
  getAll(): Observable<ReadonlyArray<T>> {
    return this.entities$.asObservable();
  }

  // Get by ID
  getById(id: number): Observable<T | undefined> {
    return this.entities$.pipe(
      map(entities => entities.find(e => e.id === id))
    );
  }

  // Add entity
  add(entity: Omit<T, 'id'>): void {
    const newEntity = {
      ...entity,
      id: Date.now()
    } as T;

    this.entities$.next([
      ...this.entities$.value,
      newEntity
    ]);
  }

  // Update entity
  update(id: number, updates: Partial<T>): void {
    const entities = this.entities$.value.map(entity =>
      entity.id === id ? { ...entity, ...updates } : entity
    );
    this.entities$.next(entities);
  }

  // Remove entity
  remove(id: number): void {
    const entities = this.entities$.value.filter(e => e.id !== id);
    this.entities$.next(entities);
  }
}

// Usage
interface User extends Entity {
  name: string;
  email: string;
  age: number;
}

const userStore = new EntityStore<User>();

userStore.add({
  name: 'Alice',
  email: 'alice@example.com',
  age: 30
});

userStore.getAll().subscribe(users => {
  console.log('All users:', users);
});
```

---

## Summary

| Concept | Description | Use Case |
|---------|-------------|----------|
| Generics | Type parameters for reusable code | Observable<T>, functions, classes |
| keyof | Extract keys as union type | Type-safe property access |
| readonly | Prevent mutation | Immutable data, state |
| Utility Types | Built-in type transformations | Partial updates, Pick/Omit fields |
| Array Generics | Type-safe array operations | Collections, streams |

## Conclusion

Understanding TypeScript's type system is crucial for effective RxJS development. These concepts enable you to:

- Write type-safe Observable streams
- Create reusable, generic operators and services
- Ensure compile-time safety in complex async flows
- Build maintainable, well-typed applications

---

**Module Complete!** You now have a comprehensive understanding of RxJS and TypeScript type utilities for building reactive applications.
