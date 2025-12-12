# Dependency Injection

## What is Dependency Injection?

Dependency Injection (DI) is a design pattern in which a class requests dependencies from external sources rather than creating them itself. Angular's DI framework provides dependencies to a class upon instantiation.

### Benefits of DI

1. **Loose Coupling**: Classes don't need to know how to create their dependencies
2. **Testability**: Easy to inject mock dependencies for testing
3. **Reusability**: Services can be shared across components
4. **Maintainability**: Centralized dependency management
5. **Flexibility**: Easy to swap implementations

### Basic DI Concept

```typescript
// Without DI (Tight Coupling)
class UserComponent {
  private userService: UserService;

  constructor() {
    this.userService = new UserService();  // Component creates dependency
  }
}

// With DI (Loose Coupling)
class UserComponent {
  constructor(private userService: UserService) {
    // Angular provides the dependency
  }
}
```

---

## DI in Angular

Angular has a hierarchical dependency injection system where each component and module can have its own injector.

### How DI Works in Angular

```
1. Define a service (dependency)
   ↓
2. Register the service with an injector
   ↓
3. Inject the service where needed
   ↓
4. Angular creates or retrieves instance
```

### Creating and Using a Service

```bash
ng generate service services/user
```

```typescript
// user.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'  // Available application-wide
})
export class UserService {
  private users: string[] = ['John', 'Jane', 'Bob'];

  getUsers(): string[] {
    return this.users;
  }

  addUser(name: string): void {
    this.users.push(name);
  }
}
```

```typescript
// user.component.ts
import { Component, OnInit } from '@angular/core';
import { UserService } from '../services/user.service';

@Component({
  selector: 'app-user',
  template: `
    <ul>
      <li *ngFor="let user of users">{{ user }}</li>
    </ul>
  `
})
export class UserComponent implements OnInit {
  users: string[] = [];

  // Inject service via constructor
  constructor(private userService: UserService) {}

  ngOnInit(): void {
    this.users = this.userService.getUsers();
  }
}
```

---

## Injector Hierarchy

Angular has a multi-level injector hierarchy that determines where and how services are provided and shared.

### Injector Levels

```
1. Root Injector (Application-wide)
   ├── Module Injector
   │   └── Component Injector
   │       └── Child Component Injector
```

### Root Injector

```typescript
// Service available to entire application
@Injectable({
  providedIn: 'root'
})
export class GlobalService {
  // Singleton instance across the app
}
```

### Module Injector

```typescript
// app.module.ts
@NgModule({
  providers: [
    ModuleLevelService  // Available to all components in this module
  ]
})
export class AppModule { }

// feature.module.ts
@NgModule({
  providers: [
    FeatureService  // Separate instance for this feature module
  ]
})
export class FeatureModule { }
```

### Component Injector

```typescript
// parent.component.ts
@Component({
  selector: 'app-parent',
  template: '<app-child></app-child>',
  providers: [ComponentService]  // New instance for this component and children
})
export class ParentComponent {
  constructor(private service: ComponentService) {
    // Receives component-level instance
  }
}

// child.component.ts
@Component({
  selector: 'app-child',
  template: '<p>Child</p>'
})
export class ChildComponent {
  constructor(private service: ComponentService) {
    // Shares parent's instance
  }
}
```

### Injector Resolution

```typescript
@Component({
  selector: 'app-demo',
  template: '<p>Demo</p>',
  providers: [LocalService]
})
export class DemoComponent {
  constructor(
    private local: LocalService,      // Finds in component injector
    private global: GlobalService     // Finds in root injector
  ) {
    // Angular searches up the injector tree
  }
}
```

---

## DI Providers

Providers tell Angular how to create instances of dependencies. There are several types of providers.

### Class Provider

```typescript
// Basic class provider
@Injectable({
  providedIn: 'root'
})
export class UserService { }

// Explicit class provider
@NgModule({
  providers: [
    { provide: UserService, useClass: UserService }
  ]
})
```

### Alternative Class Provider

```typescript
// Using a different implementation
export abstract class Logger {
  abstract log(msg: string): void;
}

export class ConsoleLogger implements Logger {
  log(msg: string): void {
    console.log(msg);
  }
}

export class FileLogger implements Logger {
  log(msg: string): void {
    // Write to file
  }
}

@NgModule({
  providers: [
    { provide: Logger, useClass: ConsoleLogger }
    // Or
    // { provide: Logger, useClass: FileLogger }
  ]
})
```

### Value Provider

```typescript
// Provide a constant value
export const API_CONFIG = {
  url: 'https://api.example.com',
  timeout: 5000
};

@NgModule({
  providers: [
    { provide: 'API_CONFIG', useValue: API_CONFIG }
  ]
})

// Usage
@Component({...})
export class MyComponent {
  constructor(@Inject('API_CONFIG') private config: any) {
    console.log(this.config.url);
  }
}
```

### Factory Provider

```typescript
// Create service based on conditions
export function userServiceFactory(isAdmin: boolean): UserService {
  return isAdmin ? new AdminUserService() : new RegularUserService();
}

@NgModule({
  providers: [
    {
      provide: UserService,
      useFactory: userServiceFactory,
      deps: [IS_ADMIN_TOKEN]
    }
  ]
})
```

### Existing Provider (Alias)

```typescript
// Create an alias
@NgModule({
  providers: [
    UserService,
    { provide: 'UserServiceAlias', useExisting: UserService }
  ]
})

// Both inject the same instance
@Component({...})
export class MyComponent {
  constructor(
    private userService: UserService,
    @Inject('UserServiceAlias') private alias: UserService
  ) {
    console.log(userService === alias);  // true
  }
}
```

---

## Services

Services are singleton objects that encapsulate business logic, data access, or other functionality that can be shared across components.

### Creating Services

```bash
ng generate service services/product
```

### Basic Service

```typescript
// product.service.ts
import { Injectable } from '@angular/core';

export interface Product {
  id: number;
  name: string;
  price: number;
}

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private products: Product[] = [
    { id: 1, name: 'Laptop', price: 999 },
    { id: 2, name: 'Mouse', price: 25 },
    { id: 3, name: 'Keyboard', price: 75 }
  ];

  getProducts(): Product[] {
    return this.products;
  }

  getProduct(id: number): Product | undefined {
    return this.products.find(p => p.id === id);
  }

  addProduct(product: Product): void {
    this.products.push(product);
  }

  updateProduct(id: number, updates: Partial<Product>): void {
    const product = this.getProduct(id);
    if (product) {
      Object.assign(product, updates);
    }
  }

  deleteProduct(id: number): void {
    const index = this.products.findIndex(p => p.id === id);
    if (index !== -1) {
      this.products.splice(index, 1);
    }
  }
}
```

### Service with HTTP

```typescript
// user.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

export interface User {
  id: number;
  name: string;
  email: string;
}

@Injectable({
  providedIn: 'root'
})
export class UserService {
  private apiUrl = 'https://api.example.com/users';

  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl);
  }

  getUser(id: number): Observable<User> {
    return this.http.get<User>(`${this.apiUrl}/${id}`);
  }

  createUser(user: User): Observable<User> {
    return this.http.post<User>(this.apiUrl, user);
  }

  updateUser(id: number, user: User): Observable<User> {
    return this.http.put<User>(`${this.apiUrl}/${id}`, user);
  }

  deleteUser(id: number): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

### Service with Dependency

```typescript
// auth.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Router } from '@angular/router';
import { Observable, BehaviorSubject } from 'rxjs';
import { tap } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private currentUserSubject = new BehaviorSubject<any>(null);
  public currentUser$ = this.currentUserSubject.asObservable();

  constructor(
    private http: HttpClient,
    private router: Router
  ) {
    // Check local storage on initialization
    const user = localStorage.getItem('currentUser');
    if (user) {
      this.currentUserSubject.next(JSON.parse(user));
    }
  }

  login(email: string, password: string): Observable<any> {
    return this.http.post<any>('/api/auth/login', { email, password })
      .pipe(
        tap(response => {
          localStorage.setItem('currentUser', JSON.stringify(response.user));
          localStorage.setItem('token', response.token);
          this.currentUserSubject.next(response.user);
        })
      );
  }

  logout(): void {
    localStorage.removeItem('currentUser');
    localStorage.removeItem('token');
    this.currentUserSubject.next(null);
    this.router.navigate(['/login']);
  }

  isLoggedIn(): boolean {
    return !!this.currentUserSubject.value;
  }

  getToken(): string | null {
    return localStorage.getItem('token');
  }
}
```

---

## Injecting Services

### Constructor Injection

```typescript
@Component({
  selector: 'app-user-list',
  template: '<div *ngFor="let user of users">{{ user.name }}</div>'
})
export class UserListComponent implements OnInit {
  users: User[] = [];

  // Most common way - constructor injection
  constructor(private userService: UserService) {}

  ngOnInit(): void {
    this.userService.getUsers().subscribe(users => {
      this.users = users;
    });
  }
}
```

### Multiple Service Injection

```typescript
@Component({
  selector: 'app-dashboard',
  templateUrl: './dashboard.component.html'
})
export class DashboardComponent implements OnInit {
  constructor(
    private userService: UserService,
    private productService: ProductService,
    private authService: AuthService,
    private router: Router
  ) {}

  ngOnInit(): void {
    if (!this.authService.isLoggedIn()) {
      this.router.navigate(['/login']);
    }
  }
}
```

### Optional Dependencies

```typescript
import { Optional } from '@angular/core';

@Component({
  selector: 'app-optional-demo',
  template: '<p>Demo</p>'
})
export class OptionalDemoComponent {
  constructor(
    @Optional() private optionalService?: OptionalService
  ) {
    if (this.optionalService) {
      // Service is available
    } else {
      // Service is not provided
    }
  }
}
```

### Self and SkipSelf Decorators

```typescript
import { Self, SkipSelf, Optional } from '@angular/core';

@Component({
  selector: 'app-parent',
  template: '<app-child></app-child>',
  providers: [DataService]
})
export class ParentComponent {
  constructor(private dataService: DataService) {
    // Gets component-level instance
  }
}

@Component({
  selector: 'app-child',
  template: '<p>Child</p>'
})
export class ChildComponent {
  constructor(
    @Self() private selfService: DataService,      // Only from this component
    @SkipSelf() private parentService: DataService // Skip this component's injector
  ) {}
}
```

---

## Service Communication

Services can be used to facilitate communication between components.

### Basic Service Communication

```typescript
// message.service.ts
import { Injectable } from '@angular/core';
import { Subject, Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class MessageService {
  private messageSubject = new Subject<string>();
  public message$: Observable<string> = this.messageSubject.asObservable();

  sendMessage(message: string): void {
    this.messageSubject.next(message);
  }
}

// sender.component.ts
@Component({
  selector: 'app-sender',
  template: '<button (click)="send()">Send Message</button>'
})
export class SenderComponent {
  constructor(private messageService: MessageService) {}

  send(): void {
    this.messageService.sendMessage('Hello from sender!');
  }
}

// receiver.component.ts
@Component({
  selector: 'app-receiver',
  template: '<p>{{ message }}</p>'
})
export class ReceiverComponent implements OnInit {
  message: string = '';

  constructor(private messageService: MessageService) {}

  ngOnInit(): void {
    this.messageService.message$.subscribe(msg => {
      this.message = msg;
    });
  }
}
```

### State Management Service

```typescript
// cart.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

export interface CartItem {
  productId: number;
  name: string;
  price: number;
  quantity: number;
}

@Injectable({
  providedIn: 'root'
})
export class CartService {
  private cartItems = new BehaviorSubject<CartItem[]>([]);
  public cartItems$: Observable<CartItem[]> = this.cartItems.asObservable();

  private totalSubject = new BehaviorSubject<number>(0);
  public total$: Observable<number> = this.totalSubject.asObservable();

  addToCart(item: CartItem): void {
    const currentItems = this.cartItems.value;
    const existingItem = currentItems.find(i => i.productId === item.productId);

    if (existingItem) {
      existingItem.quantity += item.quantity;
      this.cartItems.next([...currentItems]);
    } else {
      this.cartItems.next([...currentItems, item]);
    }

    this.calculateTotal();
  }

  removeFromCart(productId: number): void {
    const currentItems = this.cartItems.value;
    const updatedItems = currentItems.filter(i => i.productId !== productId);
    this.cartItems.next(updatedItems);
    this.calculateTotal();
  }

  updateQuantity(productId: number, quantity: number): void {
    const currentItems = this.cartItems.value;
    const item = currentItems.find(i => i.productId === productId);

    if (item) {
      item.quantity = quantity;
      this.cartItems.next([...currentItems]);
      this.calculateTotal();
    }
  }

  clearCart(): void {
    this.cartItems.next([]);
    this.totalSubject.next(0);
  }

  private calculateTotal(): void {
    const items = this.cartItems.value;
    const total = items.reduce((sum, item) => sum + (item.price * item.quantity), 0);
    this.totalSubject.next(total);
  }

  getItemCount(): number {
    return this.cartItems.value.reduce((count, item) => count + item.quantity, 0);
  }
}
```

### Using State Management Service

```typescript
// product.component.ts
@Component({
  selector: 'app-product',
  template: `
    <div class="product">
      <h3>{{ product.name }}</h3>
      <p>Price: ${{ product.price }}</p>
      <button (click)="addToCart()">Add to Cart</button>
    </div>
  `
})
export class ProductComponent {
  @Input() product!: Product;

  constructor(private cartService: CartService) {}

  addToCart(): void {
    this.cartService.addToCart({
      productId: this.product.id,
      name: this.product.name,
      price: this.product.price,
      quantity: 1
    });
  }
}

// cart.component.ts
@Component({
  selector: 'app-cart',
  template: `
    <div class="cart">
      <h2>Shopping Cart</h2>
      <div *ngFor="let item of cartItems$ | async" class="cart-item">
        <span>{{ item.name }}</span>
        <span>{{ item.quantity }} x ${{ item.price }}</span>
        <button (click)="remove(item.productId)">Remove</button>
      </div>
      <div class="total">
        Total: ${{ total$ | async }}
      </div>
    </div>
  `
})
export class CartComponent implements OnInit {
  cartItems$!: Observable<CartItem[]>;
  total$!: Observable<number>;

  constructor(private cartService: CartService) {}

  ngOnInit(): void {
    this.cartItems$ = this.cartService.cartItems$;
    this.total$ = this.cartService.total$;
  }

  remove(productId: number): void {
    this.cartService.removeFromCart(productId);
  }
}
```

---

## Advanced DI Patterns

### Injection Tokens

```typescript
// config.token.ts
import { InjectionToken } from '@angular/core';

export interface AppConfig {
  apiUrl: string;
  production: boolean;
  version: string;
}

export const APP_CONFIG = new InjectionToken<AppConfig>('app.config');

// app.module.ts
import { APP_CONFIG } from './config.token';

@NgModule({
  providers: [
    {
      provide: APP_CONFIG,
      useValue: {
        apiUrl: 'https://api.example.com',
        production: false,
        version: '1.0.0'
      }
    }
  ]
})
export class AppModule { }

// component.ts
import { Inject } from '@angular/core';
import { APP_CONFIG, AppConfig } from './config.token';

@Component({...})
export class MyComponent {
  constructor(@Inject(APP_CONFIG) private config: AppConfig) {
    console.log(this.config.apiUrl);
  }
}
```

### Multi-Provider

```typescript
// Define a token
export const LOGGER = new InjectionToken<Logger[]>('logger');

// Provide multiple implementations
@NgModule({
  providers: [
    { provide: LOGGER, useClass: ConsoleLogger, multi: true },
    { provide: LOGGER, useClass: FileLogger, multi: true },
    { provide: LOGGER, useClass: RemoteLogger, multi: true }
  ]
})

// Inject all implementations
@Component({...})
export class MyComponent {
  constructor(@Inject(LOGGER) private loggers: Logger[]) {
    this.loggers.forEach(logger => logger.log('Message'));
  }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Dependency Injection | Design pattern for providing dependencies |
| Injector Hierarchy | Root, Module, and Component injectors |
| Providers | Define how to create dependencies |
| Services | Singleton objects for shared functionality |
| providedIn | Modern way to register services |
| Service Communication | Use Observables for component communication |

## Next Topic

Continue to [Routing](./06-routing.md) to learn about Angular routing and navigation.
