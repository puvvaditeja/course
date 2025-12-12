# TypeScript Setup and Configuration

## Overview

Setting up TypeScript involves installing the compiler, configuring your project, and understanding the JavaScript runtime environment. This guide covers everything you need to get started with TypeScript development.

---

## Node.js as JavaScript Runtime

### What is Node.js?

Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine. It allows you to run JavaScript code outside the browser, making it essential for:

- Running TypeScript compiler
- Server-side JavaScript development
- Build tools and development workflows
- Package management with npm

### Why Node.js for TypeScript?

```
TypeScript Development Environment
┌─────────────────────────────────┐
│      TypeScript Code (.ts)      │
└─────────────┬───────────────────┘
              │
              v
┌─────────────────────────────────┐
│   Node.js runs TypeScript       │
│   Compiler (tsc)                │
└─────────────┬───────────────────┘
              │
              v
┌─────────────────────────────────┐
│   JavaScript Code (.js)         │
└─────────────┬───────────────────┘
              │
              v
┌─────────────────────────────────┐
│   Node.js Runtime or Browser    │
└─────────────────────────────────┘
```

### Installing Node.js

#### Check if Node.js is Installed

```bash
# Check Node.js version
node --version
# Output: v18.17.0 (or similar)

# Check npm version
npm --version
# Output: 9.6.7 (or similar)
```

#### Installation Methods

**Option 1: Official Website**
- Visit [nodejs.org](https://nodejs.org)
- Download LTS (Long Term Support) version
- Run installer

**Option 2: Using Version Manager (Recommended)**

```bash
# Using nvm (Node Version Manager) on macOS/Linux
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Install latest LTS version
nvm install --lts

# Use specific version
nvm use 18

# Using nvm on Windows (nvm-windows)
# Download from: https://github.com/coreybutler/nvm-windows
```

**Recommended Node.js Version for TypeScript:**
- Node.js 16.x or higher
- npm 8.x or higher

---

## Installing TypeScript

### Global Installation

Install TypeScript globally to use the `tsc` command anywhere:

```bash
# Install TypeScript globally
npm install -g typescript

# Verify installation
tsc --version
# Output: Version 5.3.3 (or similar)

# Check where TypeScript is installed
which tsc  # On macOS/Linux
where tsc  # On Windows
```

### Local Project Installation (Recommended)

Install TypeScript as a project dependency:

```bash
# Initialize npm project first (if not already done)
npm init -y

# Install TypeScript as dev dependency
npm install --save-dev typescript

# Install type definitions for Node.js
npm install --save-dev @types/node
```

**Why local installation?**
- Different projects can use different TypeScript versions
- Ensures consistent versions across team members
- Dependencies are tracked in `package.json`

### Using TypeScript Commands

```bash
# Global installation
tsc myfile.ts

# Local installation - use npx
npx tsc myfile.ts

# Or add script to package.json
npm run build
```

---

## TypeScript Compiler (tsc)

### Basic Compiler Usage

The TypeScript compiler (`tsc`) converts TypeScript code to JavaScript.

#### Compile a Single File

```bash
# Compile one file
tsc hello.ts

# This creates hello.js
```

**Example:**

```typescript
// hello.ts
const greeting: string = "Hello, TypeScript!";
console.log(greeting);
```

```bash
# Compile
tsc hello.ts

# Run the JavaScript output
node hello.js
# Output: Hello, TypeScript!
```

#### Compile Multiple Files

```bash
# Compile specific files
tsc file1.ts file2.ts file3.ts

# Compile all .ts files in directory
tsc *.ts
```

#### Watch Mode

Watch mode automatically recompiles when files change:

```bash
# Watch single file
tsc --watch hello.ts

# Watch all files in project
tsc --watch
```

### Compiler Options

#### Common Command-Line Options

```bash
# Specify output directory
tsc --outDir ./dist src/app.ts

# Specify target JavaScript version
tsc --target ES2020 app.ts

# Enable source maps for debugging
tsc --sourceMap app.ts

# Strict type checking
tsc --strict app.ts

# Don't emit output on errors
tsc --noEmitOnError app.ts

# Combine multiple options
tsc --outDir ./dist --target ES2020 --strict src/app.ts
```

#### JavaScript Target Versions

```bash
# ES3 (oldest, most compatible)
tsc --target ES3 app.ts

# ES5 (default, widely supported)
tsc --target ES5 app.ts

# ES6/ES2015 (modern features)
tsc --target ES2015 app.ts

# ES2020 (very modern)
tsc --target ES2020 app.ts

# ESNext (latest features)
tsc --target ESNext app.ts
```

**Example:**

```typescript
// app.ts - using modern arrow function
const add = (a: number, b: number): number => a + b;
```

```bash
# Compile to ES5
tsc --target ES5 app.ts
```

```javascript
// app.js - ES5 output
var add = function(a, b) { return a + b; };
```

### Compiler API Example

```typescript
// compile.ts - programmatic compilation
import * as ts from 'typescript';

const source = `
    const greeting: string = "Hello";
    console.log(greeting);
`;

const result = ts.transpileModule(source, {
    compilerOptions: {
        module: ts.ModuleKind.CommonJS,
        target: ts.ScriptTarget.ES2015
    }
});

console.log(result.outputText);
```

---

## TypeScript Configuration (tsconfig.json)

### Creating tsconfig.json

The `tsconfig.json` file configures TypeScript compiler options for your project.

```bash
# Generate default tsconfig.json
tsc --init

# This creates a tsconfig.json with all options (most commented out)
```

### Basic Configuration

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### Important Compiler Options

#### Target and Module

```json
{
  "compilerOptions": {
    // JavaScript version to compile to
    "target": "ES2020",

    // Module system (commonjs, es2015, esnext, etc.)
    "module": "commonjs",

    // Module resolution strategy
    "moduleResolution": "node"
  }
}
```

#### Output Configuration

```json
{
  "compilerOptions": {
    // Output directory for compiled files
    "outDir": "./dist",

    // Root directory of source files
    "rootDir": "./src",

    // Generate .d.ts declaration files
    "declaration": true,

    // Output directory for declaration files
    "declarationDir": "./types",

    // Generate source maps for debugging
    "sourceMap": true,

    // Remove comments from output
    "removeComments": true
  }
}
```

#### Strict Type Checking

```json
{
  "compilerOptions": {
    // Enable all strict type checking options
    "strict": true,

    // Individual strict options (enabled by "strict")
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true
  }
}
```

#### Additional Checks

```json
{
  "compilerOptions": {
    // Disallow unreachable code
    "allowUnreachableCode": false,

    // Disallow unused labels
    "allowUnusedLabels": false,

    // Report errors on unused local variables
    "noUnusedLocals": true,

    // Report errors on unused parameters
    "noUnusedParameters": true,

    // Report errors on implicit returns
    "noImplicitReturns": true,

    // Ensure case sensitivity in imports
    "forceConsistentCasingInFileNames": true
  }
}
```

#### Module Resolution

```json
{
  "compilerOptions": {
    // Base directory for resolving modules
    "baseUrl": "./",

    // Path mapping for module imports
    "paths": {
      "@models/*": ["src/models/*"],
      "@utils/*": ["src/utils/*"],
      "@services/*": ["src/services/*"]
    },

    // Enable importing .json files
    "resolveJsonModule": true,

    // Enable interop between CommonJS and ES Modules
    "esModuleInterop": true
  }
}
```

### Project References

```json
{
  "include": ["src/**/*"],
  "exclude": [
    "node_modules",
    "dist",
    "**/*.spec.ts",
    "**/*.test.ts"
  ]
}
```

### Complete Production Configuration

```json
{
  "compilerOptions": {
    // Language and Environment
    "target": "ES2020",
    "lib": ["ES2020"],
    "module": "commonjs",
    "moduleResolution": "node",

    // Output
    "outDir": "./dist",
    "rootDir": "./src",
    "declaration": true,
    "sourceMap": true,
    "removeComments": true,

    // Type Checking
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,

    // Interop Constraints
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "skipLibCheck": true,

    // Advanced
    "incremental": true,
    "tsBuildInfoFile": "./dist/.tsbuildinfo"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.spec.ts"]
}
```

---

## Package.json Configuration

### Initializing a Node.js Project

```bash
# Create package.json
npm init -y
```

### Basic Package.json Structure

```json
{
  "name": "my-typescript-project",
  "version": "1.0.0",
  "description": "A TypeScript project",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "scripts": {
    "build": "tsc",
    "watch": "tsc --watch",
    "start": "node dist/index.js",
    "dev": "ts-node src/index.ts"
  },
  "keywords": ["typescript"],
  "author": "Your Name",
  "license": "MIT",
  "devDependencies": {
    "@types/node": "^20.10.0",
    "typescript": "^5.3.0"
  },
  "dependencies": {}
}
```

### Common npm Scripts for TypeScript

```json
{
  "scripts": {
    // Compile TypeScript
    "build": "tsc",

    // Watch mode - recompile on changes
    "watch": "tsc --watch",

    // Clean build directory
    "clean": "rm -rf dist",

    // Clean and build
    "rebuild": "npm run clean && npm run build",

    // Run compiled JavaScript
    "start": "node dist/index.js",

    // Development with ts-node (no compilation needed)
    "dev": "ts-node src/index.ts",

    // Development with watch mode
    "dev:watch": "nodemon --exec ts-node src/index.ts",

    // Type checking without emitting files
    "type-check": "tsc --noEmit",

    // Linting
    "lint": "eslint src/**/*.ts",

    // Testing
    "test": "jest"
  }
}
```

### Installing Development Tools

```bash
# TypeScript essentials
npm install --save-dev typescript @types/node

# ts-node for running TypeScript directly
npm install --save-dev ts-node

# nodemon for auto-restart on file changes
npm install --save-dev nodemon

# ESLint for code quality
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin

# Prettier for code formatting
npm install --save-dev prettier

# Jest for testing
npm install --save-dev jest ts-jest @types/jest
```

### Using ts-node for Development

`ts-node` lets you run TypeScript directly without compiling:

```bash
# Install ts-node
npm install --save-dev ts-node

# Run TypeScript file directly
npx ts-node src/app.ts

# Or add to package.json scripts
```

**package.json with ts-node:**

```json
{
  "scripts": {
    "dev": "ts-node src/index.ts",
    "dev:watch": "nodemon --exec ts-node src/index.ts"
  },
  "devDependencies": {
    "ts-node": "^10.9.1",
    "nodemon": "^3.0.1",
    "typescript": "^5.3.0",
    "@types/node": "^20.10.0"
  }
}
```

### Type Definitions

Install type definitions for libraries:

```bash
# Express types
npm install --save-dev @types/express

# Lodash types
npm install --save-dev @types/lodash

# React types
npm install --save-dev @types/react @types/react-dom

# Jest types
npm install --save-dev @types/jest
```

---

## Complete Project Setup Example

### Step-by-Step Setup

```bash
# 1. Create project directory
mkdir my-ts-project
cd my-ts-project

# 2. Initialize npm project
npm init -y

# 3. Install TypeScript and Node types
npm install --save-dev typescript @types/node

# 4. Generate tsconfig.json
npx tsc --init

# 5. Create source directory
mkdir src

# 6. Create entry file
touch src/index.ts

# 7. Install development tools
npm install --save-dev ts-node nodemon
```

### Project Structure

```
my-ts-project/
├── src/
│   ├── index.ts
│   ├── models/
│   ├── services/
│   └── utils/
├── dist/              # Compiled output
├── node_modules/
├── package.json
├── tsconfig.json
└── .gitignore
```

### .gitignore for TypeScript

```gitignore
# Dependencies
node_modules/

# Compiled output
dist/
build/
*.js
*.js.map

# TypeScript cache
*.tsbuildinfo

# Environment variables
.env
.env.local

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db
```

### Sample Application

**src/index.ts:**

```typescript
// src/index.ts
interface User {
    id: number;
    name: string;
    email: string;
}

function greetUser(user: User): void {
    console.log(`Hello, ${user.name}!`);
    console.log(`Your email is: ${user.email}`);
}

const user: User = {
    id: 1,
    name: "Alice",
    email: "alice@example.com"
};

greetUser(user);
```

**Running the Application:**

```bash
# Development - using ts-node
npm run dev

# Production - compile and run
npm run build
npm start
```

---

## Summary

| Topic | Key Points |
|-------|------------|
| Node.js Runtime | Required for running TypeScript compiler and JavaScript code |
| Installing TypeScript | Global: `npm install -g typescript`, Local: `npm install --save-dev typescript` |
| TypeScript Compiler | `tsc` command compiles .ts to .js, supports various options |
| tsconfig.json | Central configuration file for compiler options |
| package.json | Manages dependencies, scripts, and project metadata |

## Useful Commands Reference

```bash
# Check versions
node --version
npm --version
tsc --version

# Install TypeScript
npm install -g typescript                    # Global
npm install --save-dev typescript            # Local

# Initialize configuration
npm init -y                                  # Create package.json
tsc --init                                   # Create tsconfig.json

# Compile TypeScript
tsc                                          # Compile all files
tsc app.ts                                   # Compile specific file
tsc --watch                                  # Watch mode

# Run TypeScript
ts-node app.ts                               # Run without compiling
node dist/app.js                             # Run compiled JavaScript
```

## Next Topic

Continue to [Basic Types](./03-basic-types.md) to learn about TypeScript's type system and basic type annotations.
