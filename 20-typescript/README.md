# TypeScript

## Overview
TypeScript is a typed superset of JavaScript that compiles to plain JavaScript, providing better tooling and code quality. This module covers TypeScript fundamentals from basic types to advanced features like decorators.

## Learning Objectives
By the end of this module, you will be able to:
- Understand what TypeScript is and why it's valuable for modern development
- Set up and configure TypeScript projects
- Use TypeScript's type system including basic and advanced types
- Write type-safe functions with proper annotations
- Create and use classes with access modifiers
- Apply decorators for meta-programming

## Topics Covered

### 1. [TypeScript Introduction](./topics/01-typescript-introduction.md)
- What is TypeScript?
- JavaScript vs TypeScript comparison
- Benefits and use cases
- When to use TypeScript
- Real-world adoption

### 2. [Setup and Configuration](./topics/02-setup-and-configuration.md)
- Node.js as JavaScript runtime
- Installing TypeScript (global and local)
- TypeScript compiler (tsc) usage
- tsconfig.json configuration
- package.json setup and scripts
- Project structure and best practices

### 3. [Basic Types](./topics/03-basic-types.md)
- Primitive types (string, number, boolean, bigint, symbol)
- Special types (any, unknown, void, never, null, undefined)
- Object types (objects, arrays, tuples, enums)
- Union types and type narrowing
- Literal types and type assertions

### 4. [Advanced Types](./topics/04-advanced-types.md)
- Type aliases and interfaces
- Type casting and assertions
- Utility types (Partial, Required, Readonly, Pick, Omit, Record, etc.)
- Intersection types
- Mapped types and conditional types
- Template literal types

### 5. [Functions and Classes](./topics/05-functions-and-classes.md)
- Function type annotations
- Optional and default parameters
- Function overloads and generics
- Class definitions and constructors
- Inheritance and abstract classes
- Access modifiers (public, private, protected, readonly)
- Decorators (class, method, property, parameter)

## Key Concepts
TypeScript enhances JavaScript with:
- **Static Type Checking**: Catch errors at compile-time
- **Better IDE Support**: IntelliSense, auto-completion, refactoring
- **Self-Documenting Code**: Types serve as inline documentation
- **Modern JavaScript Features**: Use latest ECMAScript features
- **Gradual Adoption**: Can be added incrementally to existing projects

## Quick Start

### Installation

```bash
# Install Node.js (if not already installed)
# Download from: https://nodejs.org

# Create a new project
mkdir my-typescript-project
cd my-typescript-project

# Initialize npm
npm init -y

# Install TypeScript
npm install --save-dev typescript @types/node

# Generate tsconfig.json
npx tsc --init
```

### Hello TypeScript

```typescript
// hello.ts
interface Person {
    name: string;
    age: number;
}

function greet(person: Person): string {
    return `Hello, ${person.name}! You are ${person.age} years old.`;
}

const user: Person = {
    name: "Alice",
    age: 30
};

console.log(greet(user));
```

```bash
# Compile TypeScript
npx tsc hello.ts

# Run the JavaScript output
node hello.js
```

## Key Commands Reference

```bash
# TypeScript Compiler
tsc file.ts              # Compile single file
tsc                      # Compile all files (uses tsconfig.json)
tsc --watch              # Watch mode - recompile on changes
tsc --noEmit             # Type check without generating files

# Project Setup
npm init -y              # Initialize package.json
tsc --init               # Generate tsconfig.json
npm install --save-dev typescript

# Running TypeScript
npx ts-node file.ts      # Run TypeScript directly (requires ts-node)
node dist/file.js        # Run compiled JavaScript

# Type Definitions
npm install --save-dev @types/node       # Node.js types
npm install --save-dev @types/express    # Express types
npm install --save-dev @types/react      # React types
```

## Exercises
See the [exercises](./exercises/) directory for hands-on practice problems and solutions.

## Additional Resources

### Official Documentation
- [TypeScript Official Documentation](https://www.typescriptlang.org/docs/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [TypeScript Playground](https://www.typescriptlang.org/play)

### Learning Resources
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [TypeScript Tutorial - W3Schools](https://www.w3schools.com/typescript/)
- [TypeScript Course - FreeCodeCamp](https://www.youtube.com/watch?v=gp5H0Vw39yw)

### Tools and Libraries
- [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) - Type definitions repository
- [ts-node](https://github.com/TypeStrong/ts-node) - TypeScript execution engine for Node.js
- [TSLint](https://palantir.github.io/tslint/) / [ESLint](https://typescript-eslint.io/) - Linting tools

### Cheat Sheets
- See [resources/cheat-sheets/](../resources/cheat-sheets/) for TypeScript quick reference

## Assessment

You should be comfortable with:
- [ ] Setting up a TypeScript project with proper configuration
- [ ] Using basic types (string, number, boolean, arrays, objects)
- [ ] Understanding and applying special types (any, unknown, void, never)
- [ ] Creating and using interfaces and type aliases
- [ ] Working with union types and type narrowing
- [ ] Using utility types (Partial, Pick, Omit, Record, etc.)
- [ ] Writing type-safe functions with proper annotations
- [ ] Creating classes with inheritance and access modifiers
- [ ] Understanding when and how to use decorators

## Common TypeScript Patterns

### Configuration Object Pattern
```typescript
interface Config {
    apiUrl: string;
    timeout?: number;
    retries?: number;
}

function initialize(config: Config): void {
    const { apiUrl, timeout = 5000, retries = 3 } = config;
    // Use configuration
}
```

### Result/Error Pattern
```typescript
type Result<T, E = Error> =
    | { success: true; data: T }
    | { success: false; error: E };

function divide(a: number, b: number): Result<number> {
    if (b === 0) {
        return { success: false, error: new Error("Division by zero") };
    }
    return { success: true, data: a / b };
}
```

### Builder Pattern
```typescript
class RequestBuilder {
    private url: string = "";
    private method: string = "GET";
    private headers: Record<string, string> = {};

    setUrl(url: string): this {
        this.url = url;
        return this;
    }

    setMethod(method: string): this {
        this.method = method;
        return this;
    }

    addHeader(key: string, value: string): this {
        this.headers[key] = value;
        return this;
    }

    build() {
        return { url: this.url, method: this.method, headers: this.headers };
    }
}

const request = new RequestBuilder()
    .setUrl("https://api.example.com")
    .setMethod("POST")
    .addHeader("Content-Type", "application/json")
    .build();
```

## Next Steps

Once you've completed this module and feel confident with TypeScript, proceed to:
- Explore TypeScript with React or Angular frameworks
- Build a full-stack application with TypeScript on both frontend and backend
- Learn about advanced TypeScript features like conditional types and template literal types
- Continue to the next module in the curriculum sequence

---

**Time Estimate:** 3 days
**Difficulty:** Intermediate
**Prerequisites:** JavaScript fundamentals, basic understanding of object-oriented programming
