# Angular Introduction

## What is Angular?

Angular is a platform and framework for building single-page client applications using HTML, CSS, and TypeScript. Developed and maintained by Google, Angular provides a comprehensive solution for building modern web applications with a focus on scalability, testability, and maintainability.

### Key Characteristics

- **Component-Based Architecture**: Build encapsulated components that manage their own state
- **TypeScript**: Built with TypeScript for better tooling and type safety
- **Dependency Injection**: Built-in DI system for better modularity and testing
- **Reactive Programming**: RxJS integration for handling asynchronous operations
- **CLI Tools**: Powerful command-line interface for scaffolding and development
- **Cross-Platform**: Build for web, mobile web, native mobile, and desktop

### Angular vs AngularJS

| Feature | AngularJS (1.x) | Angular (2+) |
|---------|-----------------|--------------|
| Architecture | MVC | Component-based |
| Language | JavaScript | TypeScript |
| Mobile Support | Limited | Excellent |
| Performance | Good | Excellent |
| Dependency Injection | Yes | Enhanced |
| Current Status | Legacy | Active Development |

---

## Setup and Installation

### Prerequisites

Before installing Angular, ensure you have the following installed:

1. **Node.js** (v16.14 or higher recommended)
2. **npm** (comes with Node.js)

#### Verify Node.js and npm Installation

```bash
# Check Node.js version
node --version
# Output: v18.x.x or higher

# Check npm version
npm --version
# Output: 9.x.x or higher
```

### Installing Angular CLI

The Angular CLI (Command Line Interface) is the official tool for initializing, developing, and maintaining Angular applications.

```bash
# Install Angular CLI globally
npm install -g @angular/cli

# Verify installation
ng version

# Output shows Angular CLI version and Node.js info
```

### Creating Your First Angular Application

```bash
# Create a new Angular application
ng new my-first-app

# You'll be prompted with options:
# ? Would you like to add Angular routing? (y/N) y
# ? Which stylesheet format would you like to use? CSS

# Navigate to the project directory
cd my-first-app

# Serve the application
ng serve

# Application runs at http://localhost:4200
```

### Alternative Installation Methods

```bash
# Create app without installing CLI globally
npx @angular/cli new my-app

# Create app with specific version
npm install -g @angular/cli@16
ng new my-app

# Create app with routing and SCSS
ng new my-app --routing --style=scss

# Create app and skip npm install
ng new my-app --skip-install
```

---

## Angular CLI

The Angular CLI is a powerful command-line tool that helps you create, manage, build, and test Angular applications.

### Common CLI Commands

#### Project Creation and Serving

```bash
# Create a new workspace and application
ng new my-app

# Serve the application with live reload
ng serve

# Serve on a different port
ng serve --port 4300

# Serve with SSL
ng serve --ssl

# Open browser automatically
ng serve --open
```

#### Generating Components and Other Files

```bash
# Generate a component
ng generate component my-component
# or shorthand:
ng g c my-component

# Generate a component in a specific folder
ng g c components/header

# Generate component without test file
ng g c my-component --skip-tests

# Generate a service
ng g service services/data

# Generate a module
ng g module features/user

# Generate a directive
ng g directive directives/highlight

# Generate a pipe
ng g pipe pipes/capitalize

# Generate a guard
ng g guard guards/auth

# Generate an interface
ng g interface models/user

# Generate a class
ng g class models/product
```

#### Building and Testing

```bash
# Build for development
ng build

# Build for production
ng build --configuration production
# or shorthand:
ng build --prod

# Run unit tests
ng test

# Run end-to-end tests
ng e2e

# Run linting
ng lint

# Update Angular and dependencies
ng update
```

#### Getting Help

```bash
# Get general help
ng help

# Get help for specific command
ng generate --help
ng build --help
```

---

## Angular Project Structure

Understanding the project structure is crucial for working effectively with Angular applications.

### Root Level Structure

```
my-angular-app/
├── .angular/               # Angular CLI cache
├── .vscode/               # VS Code settings (optional)
├── node_modules/          # Project dependencies
├── src/                   # Application source code
├── .editorconfig          # Editor configuration
├── .gitignore            # Git ignore rules
├── angular.json          # Angular CLI configuration
├── package.json          # npm dependencies and scripts
├── package-lock.json     # npm dependency lock file
├── tsconfig.json         # TypeScript configuration
├── tsconfig.app.json     # TypeScript config for app
└── tsconfig.spec.json    # TypeScript config for tests
```

### Source Directory Structure

```
src/
├── app/                   # Application code
│   ├── app.component.ts   # Root component
│   ├── app.component.html # Root component template
│   ├── app.component.css  # Root component styles
│   ├── app.component.spec.ts # Root component tests
│   ├── app.module.ts      # Root module
│   └── app-routing.module.ts # Routing module
├── assets/               # Static assets (images, fonts)
├── environments/         # Environment configurations
│   ├── environment.ts    # Development environment
│   └── environment.prod.ts # Production environment
├── index.html           # Main HTML file
├── main.ts              # Application entry point
├── styles.css           # Global styles
└── polyfills.ts         # Browser polyfills
```

### Key Files Explained

#### angular.json
```json
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "my-app": {
      "projectType": "application",
      "architect": {
        "build": {
          "builder": "@angular-devkit/build-angular:browser",
          "options": {
            "outputPath": "dist/my-app",
            "index": "src/index.html",
            "main": "src/main.ts",
            "polyfills": ["zone.js"],
            "assets": ["src/favicon.ico", "src/assets"],
            "styles": ["src/styles.css"],
            "scripts": []
          }
        }
      }
    }
  }
}
```

#### package.json
```json
{
  "name": "my-app",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint"
  },
  "private": true,
  "dependencies": {
    "@angular/animations": "^16.0.0",
    "@angular/common": "^16.0.0",
    "@angular/compiler": "^16.0.0",
    "@angular/core": "^16.0.0",
    "@angular/forms": "^16.0.0",
    "@angular/platform-browser": "^16.0.0",
    "@angular/platform-browser-dynamic": "^16.0.0",
    "@angular/router": "^16.0.0",
    "rxjs": "~7.8.0",
    "tslib": "^2.3.0",
    "zone.js": "~0.13.0"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "^16.0.0",
    "@angular/cli": "~16.0.0",
    "@angular/compiler-cli": "^16.0.0",
    "typescript": "~5.0.0"
  }
}
```

#### main.ts (Application Entry Point)
```typescript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app/app.module';

platformBrowserDynamic()
  .bootstrapModule(AppModule)
  .catch(err => console.error(err));
```

#### app.module.ts (Root Module)
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

---

## Single Page Applications (SPA)

### What is a SPA?

A Single Page Application is a web application that loads a single HTML page and dynamically updates content as the user interacts with the app, without requiring full page reloads.

### Traditional vs SPA Architecture

#### Traditional Multi-Page Application
```
User Request → Server → New HTML Page → Browser Render
    ↓
User clicks link
    ↓
New Request → Server → New HTML Page → Browser Render
```

#### Single Page Application
```
Initial Request → Server → HTML + JS Bundle
    ↓
User Interaction → Client-side Routing → Update DOM
    ↓
API Calls → Server (JSON) → Update View
```

### Advantages of SPAs

1. **Better User Experience**
   - Smoother navigation without page reloads
   - Faster interactions after initial load
   - More responsive and fluid

2. **Reduced Server Load**
   - Server only sends data (JSON), not entire pages
   - Fewer HTTP requests for navigation

3. **Caching Capabilities**
   - Can cache data locally
   - Works offline (with service workers)

4. **Code Reusability**
   - Share code between web and mobile apps
   - Component-based architecture

### Disadvantages of SPAs

1. **Initial Load Time**
   - Larger JavaScript bundle to download
   - Mitigated by lazy loading

2. **SEO Challenges**
   - Search engines may struggle with JavaScript-rendered content
   - Solved with Server-Side Rendering (SSR) or prerendering

3. **Browser History**
   - Requires managing history manually
   - Angular Router handles this

### SPA in Angular

```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { AboutComponent } from './about/about.component';
import { ContactComponent } from './contact/contact.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'contact', component: ContactComponent },
  { path: '**', redirectTo: '' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

```html
<!-- app.component.html -->
<nav>
  <a routerLink="/">Home</a>
  <a routerLink="/about">About</a>
  <a routerLink="/contact">Contact</a>
</nav>

<!-- Router outlet - where components are rendered -->
<router-outlet></router-outlet>
```

---

## Webpack

### What is Webpack?

Webpack is a static module bundler for JavaScript applications. Angular CLI uses webpack behind the scenes to bundle your application code, dependencies, and assets into optimized bundles for the browser.

### How Webpack Works in Angular

```
Source Files
    ↓
TypeScript → Webpack (transpile, bundle, optimize)
Templates  →
Styles     →
    ↓
Optimized Bundles
    ↓
main.js, polyfills.js, styles.css
```

### Webpack Features Used by Angular

#### 1. Module Bundling
```typescript
// Angular automatically bundles these imports
import { Component } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
```

#### 2. Code Splitting
```typescript
// Lazy loading automatically creates separate chunks
const routes: Routes = [
  {
    path: 'admin',
    loadChildren: () => import('./admin/admin.module')
      .then(m => m.AdminModule)
  }
];
```

#### 3. Asset Processing
```typescript
// Webpack processes these assets
@Component({
  selector: 'app-logo',
  template: '<img [src]="logoUrl">',
  styleUrls: ['./logo.component.css']
})
export class LogoComponent {
  logoUrl = 'assets/images/logo.png'; // Webpack handles this
}
```

#### 4. Development Server with Hot Module Replacement
```bash
# Webpack dev server with HMR
ng serve

# Changes are automatically reflected without full reload
```

### Build Optimization

Angular CLI configures webpack for different environments:

```bash
# Development build (no optimization)
ng build

# Production build (optimized)
ng build --configuration production
```

#### Production Optimizations

1. **Minification**: Removes whitespace and shortens variable names
2. **Tree Shaking**: Removes unused code
3. **Ahead-of-Time Compilation**: Pre-compiles templates
4. **Bundling**: Combines files to reduce HTTP requests
5. **Source Maps**: Optional for debugging production

### Webpack Configuration in Angular

While Angular CLI handles webpack configuration, you can customize it:

```json
// angular.json - build options
{
  "projects": {
    "my-app": {
      "architect": {
        "build": {
          "options": {
            "optimization": true,
            "outputHashing": "all",
            "sourceMap": false,
            "extractCss": true,
            "namedChunks": false,
            "aot": true,
            "buildOptimizer": true
          }
        }
      }
    }
  }
}
```

### Custom Webpack Configuration

For advanced scenarios, you can customize webpack:

```bash
# Install custom webpack builder
npm install @angular-builders/custom-webpack

# Create webpack.config.js for customizations
```

```javascript
// webpack.config.js (custom)
module.exports = {
  module: {
    rules: [
      {
        test: /\.custom$/,
        use: 'custom-loader'
      }
    ]
  }
};
```

---

## Node.js and npm

### Why Node.js for Angular?

Although Angular runs in the browser, Node.js is essential for the development workflow:

1. **Package Management**: npm manages dependencies
2. **Build Tools**: Angular CLI runs on Node.js
3. **Development Server**: Local development server
4. **Build Process**: Compiling, bundling, and optimization

### npm (Node Package Manager)

#### Understanding package.json

```json
{
  "name": "my-angular-app",
  "version": "1.0.0",
  "scripts": {
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test"
  },
  "dependencies": {
    "@angular/core": "^16.0.0"
  },
  "devDependencies": {
    "@angular/cli": "^16.0.0",
    "typescript": "~5.0.0"
  }
}
```

**Dependencies**:
- `dependencies`: Packages required for the application to run
- `devDependencies`: Packages only needed during development

**Version Notations**:
- `^16.0.0`: Compatible with version 16.x.x
- `~16.0.0`: Compatible with version 16.0.x
- `16.0.0`: Exact version

#### Common npm Commands

```bash
# Install all dependencies from package.json
npm install

# Install a specific package
npm install lodash

# Install a package as dev dependency
npm install --save-dev @types/lodash

# Install a specific version
npm install @angular/core@16.0.0

# Update packages
npm update

# Remove a package
npm uninstall lodash

# List installed packages
npm list

# Check for outdated packages
npm outdated

# Run scripts defined in package.json
npm run build
npm start
npm test
```

### Managing Dependencies

```bash
# Install Angular Material
npm install @angular/material @angular/cdk

# Install RxJS operators
npm install rxjs

# Install type definitions
npm install --save-dev @types/node

# Install testing utilities
npm install --save-dev @angular/core/testing
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Angular | Component-based framework for building SPAs with TypeScript |
| Angular CLI | Command-line tool for creating and managing Angular projects |
| Project Structure | Organized into modules, components, services, and routing |
| SPA | Single page that dynamically updates without full page reloads |
| Webpack | Module bundler that optimizes and bundles application code |
| Node.js & npm | Development environment and package management |

## Next Topic

Continue to [Components](./02-components.md) to learn about Angular components and their lifecycle.
