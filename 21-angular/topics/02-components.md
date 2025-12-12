# Angular Components

## What are Components?

Components are the fundamental building blocks of Angular applications. A component controls a patch of screen called a view through its associated template, and encapsulates the view's logic and data.

### Component Structure

Every Angular component consists of:

1. **TypeScript Class**: Contains the component logic and data
2. **HTML Template**: Defines the component's view
3. **CSS Styles**: Defines the component's appearance
4. **Metadata**: Configuration through the `@Component` decorator

```typescript
// user-profile.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-user-profile',
  templateUrl: './user-profile.component.html',
  styleUrls: ['./user-profile.component.css']
})
export class UserProfileComponent {
  username: string = 'John Doe';
  email: string = 'john@example.com';

  updateProfile(): void {
    console.log('Profile updated');
  }
}
```

---

## @Component Decorator

The `@Component` decorator marks a class as an Angular component and provides configuration metadata.

### Essential Properties

```typescript
import { Component } from '@angular/core';

@Component({
  // Component selector (how to use it in templates)
  selector: 'app-product',

  // Template options (use one)
  template: '<h1>{{ title }}</h1>',  // Inline template
  // OR
  templateUrl: './product.component.html',  // External template

  // Style options
  styles: ['h1 { color: blue; }'],  // Inline styles
  // OR
  styleUrls: ['./product.component.css'],  // External styles

  // Change detection strategy
  changeDetection: ChangeDetectionStrategy.OnPush,

  // View encapsulation
  encapsulation: ViewEncapsulation.Emulated
})
export class ProductComponent {
  title: string = 'Product Details';
}
```

### Selector Types

```typescript
// Element selector (most common)
@Component({
  selector: 'app-header'
})
// Usage: <app-header></app-header>

// Attribute selector
@Component({
  selector: '[app-highlight]'
})
// Usage: <div app-highlight></div>

// Class selector
@Component({
  selector: '.app-widget'
})
// Usage: <div class="app-widget"></div>
```

### Template Options

```typescript
// Inline template (for simple components)
@Component({
  selector: 'app-greeting',
  template: `
    <div class="greeting">
      <h1>Hello {{ name }}!</h1>
      <p>Welcome to Angular</p>
    </div>
  `
})

// External template (recommended for complex components)
@Component({
  selector: 'app-dashboard',
  templateUrl: './dashboard.component.html'
})
```

---

## Component Lifecycle

Angular creates, updates, and destroys components as users navigate through the application. You can tap into key moments in this lifecycle using lifecycle hook methods.

### Lifecycle Hook Sequence

```
1. constructor()         → Class instantiation
2. ngOnChanges()        → Input properties change
3. ngOnInit()           → Component initialization
4. ngDoCheck()          → Custom change detection
5. ngAfterContentInit() → Content projection initialized
6. ngAfterContentChecked() → Content projection checked
7. ngAfterViewInit()    → View initialization complete
8. ngAfterViewChecked() → View checked
9. ngOnDestroy()        → Before component is destroyed
```

### Lifecycle Hook Examples

```typescript
import {
  Component,
  OnInit,
  OnChanges,
  OnDestroy,
  AfterViewInit,
  SimpleChanges,
  Input
} from '@angular/core';

@Component({
  selector: 'app-lifecycle-demo',
  template: `
    <div>
      <h2>{{ title }}</h2>
      <p>Count: {{ count }}</p>
    </div>
  `
})
export class LifecycleDemoComponent implements OnInit, OnChanges, OnDestroy, AfterViewInit {
  @Input() title: string = '';
  count: number = 0;
  private timer: any;

  // Called when class is instantiated
  constructor() {
    console.log('1. Constructor called');
  }

  // Called when input properties change
  ngOnChanges(changes: SimpleChanges): void {
    console.log('2. ngOnChanges called', changes);
    if (changes['title']) {
      console.log('Title changed from', changes['title'].previousValue,
                  'to', changes['title'].currentValue);
    }
  }

  // Called once after the first ngOnChanges
  ngOnInit(): void {
    console.log('3. ngOnInit called');
    // Perfect for initialization logic
    this.timer = setInterval(() => {
      this.count++;
    }, 1000);
  }

  // Called after view is fully initialized
  ngAfterViewInit(): void {
    console.log('4. ngAfterViewInit called');
    // Access child components and DOM elements here
  }

  // Called before component is destroyed
  ngOnDestroy(): void {
    console.log('5. ngOnDestroy called');
    // Cleanup: unsubscribe, clear timers, etc.
    if (this.timer) {
      clearInterval(this.timer);
    }
  }
}
```

### Common Use Cases for Lifecycle Hooks

#### ngOnInit - Initialization
```typescript
@Component({
  selector: 'app-user-list',
  template: '<div *ngFor="let user of users">{{ user.name }}</div>'
})
export class UserListComponent implements OnInit {
  users: User[] = [];

  constructor(private userService: UserService) {}

  ngOnInit(): void {
    // Fetch data after component is initialized
    this.userService.getUsers().subscribe(users => {
      this.users = users;
    });
  }
}
```

#### ngOnDestroy - Cleanup
```typescript
@Component({
  selector: 'app-real-time-data',
  template: '<div>{{ data }}</div>'
})
export class RealTimeDataComponent implements OnInit, OnDestroy {
  data: string = '';
  private subscription?: Subscription;

  constructor(private dataService: DataService) {}

  ngOnInit(): void {
    this.subscription = this.dataService.getData()
      .subscribe(data => this.data = data);
  }

  ngOnDestroy(): void {
    // Prevent memory leaks
    this.subscription?.unsubscribe();
  }
}
```

#### ngOnChanges - React to Input Changes
```typescript
@Component({
  selector: 'app-countdown',
  template: '<div>{{ timeRemaining }}</div>'
})
export class CountdownComponent implements OnChanges {
  @Input() seconds: number = 0;
  timeRemaining: number = 0;

  ngOnChanges(changes: SimpleChanges): void {
    if (changes['seconds']) {
      this.timeRemaining = changes['seconds'].currentValue;
      this.startCountdown();
    }
  }

  private startCountdown(): void {
    // Countdown logic
  }
}
```

---

## Component Styles

Angular provides multiple ways to style components with encapsulation options to prevent style leakage.

### Styling Methods

#### 1. External Stylesheet
```typescript
@Component({
  selector: 'app-card',
  templateUrl: './card.component.html',
  styleUrls: ['./card.component.css']  // Can include multiple files
})
```

```css
/* card.component.css */
.card {
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 16px;
  margin: 8px;
}

.card-title {
  font-size: 1.5rem;
  font-weight: bold;
}
```

#### 2. Inline Styles
```typescript
@Component({
  selector: 'app-alert',
  template: '<div class="alert">{{ message }}</div>',
  styles: [`
    .alert {
      padding: 12px 20px;
      border-radius: 4px;
      background-color: #f8f9fa;
      color: #333;
    }
  `]
})
```

#### 3. Template Inline Styles
```html
<div [style.color]="textColor" [style.font-size.px]="fontSize">
  Dynamic styling
</div>

<div [ngStyle]="{
  'color': isActive ? 'green' : 'red',
  'font-weight': isImportant ? 'bold' : 'normal'
}">
  Multiple dynamic styles
</div>
```

#### 4. CSS Classes
```html
<!-- Static class -->
<div class="card primary">Content</div>

<!-- Dynamic class binding -->
<div [class.active]="isActive">Content</div>

<!-- Multiple dynamic classes -->
<div [ngClass]="{
  'active': isActive,
  'disabled': isDisabled,
  'highlighted': isHighlighted
}">
  Content
</div>
```

### View Encapsulation

Angular provides three encapsulation strategies:

```typescript
import { Component, ViewEncapsulation } from '@angular/core';

// 1. Emulated (default) - Scopes styles to component
@Component({
  selector: 'app-emulated',
  templateUrl: './emulated.component.html',
  styleUrls: ['./emulated.component.css'],
  encapsulation: ViewEncapsulation.Emulated
})

// 2. None - Styles are global
@Component({
  selector: 'app-none',
  templateUrl: './none.component.html',
  styleUrls: ['./none.component.css'],
  encapsulation: ViewEncapsulation.None
})

// 3. ShadowDom - Uses native Shadow DOM
@Component({
  selector: 'app-shadow',
  templateUrl: './shadow.component.html',
  styleUrls: ['./shadow.component.css'],
  encapsulation: ViewEncapsulation.ShadowDom
})
```

### Special Selectors

```css
/* :host - Style the component element itself */
:host {
  display: block;
  border: 1px solid black;
}

/* :host with condition */
:host(.active) {
  border-color: blue;
}

/* :host-context - Style based on ancestor */
:host-context(.dark-theme) {
  background-color: #333;
  color: white;
}

/* ::ng-deep - Pierce encapsulation (deprecated, use with caution) */
::ng-deep .child-component {
  color: red;
}
```

---

## Change Detection

Change detection is the mechanism by which Angular keeps the view in sync with the component state.

### How Change Detection Works

```typescript
@Component({
  selector: 'app-counter',
  template: `
    <div>
      <p>Count: {{ count }}</p>
      <button (click)="increment()">Increment</button>
    </div>
  `
})
export class CounterComponent {
  count: number = 0;

  increment(): void {
    this.count++;  // Angular detects this change and updates the view
  }
}
```

### Change Detection Strategies

#### Default Strategy
```typescript
import { Component, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-default',
  template: '<div>{{ data }}</div>',
  changeDetection: ChangeDetectionStrategy.Default  // Checks on every event
})
export class DefaultComponent {
  data: string = 'Hello';
}
```

#### OnPush Strategy (Optimized)
```typescript
@Component({
  selector: 'app-optimized',
  template: '<div>{{ user.name }}</div>',
  changeDetection: ChangeDetectionStrategy.OnPush  // Only checks when inputs change
})
export class OptimizedComponent {
  @Input() user!: User;
}
```

### Manual Change Detection

```typescript
import { Component, ChangeDetectorRef } from '@angular/core';

@Component({
  selector: 'app-manual',
  template: '<div>{{ data }}</div>',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ManualComponent implements OnInit {
  data: string = '';

  constructor(private cdr: ChangeDetectorRef) {}

  ngOnInit(): void {
    // Async operation that Angular won't detect
    setTimeout(() => {
      this.data = 'Updated';
      this.cdr.markForCheck();  // Manually trigger change detection
    }, 1000);
  }

  // Other useful methods
  forceUpdate(): void {
    this.cdr.detectChanges();  // Run change detection immediately
  }

  detachChangeDetection(): void {
    this.cdr.detach();  // Detach from change detection tree
  }

  reattachChangeDetection(): void {
    this.cdr.reattach();  // Reattach to change detection tree
  }
}
```

---

## Dynamic Components

Dynamic components are created programmatically at runtime rather than being declared in templates.

### Creating Dynamic Components

```typescript
import {
  Component,
  ViewChild,
  ViewContainerRef,
  ComponentRef,
  Type
} from '@angular/core';
import { AlertComponent } from './alert.component';

@Component({
  selector: 'app-dynamic-host',
  template: `
    <div>
      <button (click)="loadComponent()">Load Dynamic Component</button>
      <ng-container #dynamicContainer></ng-container>
    </div>
  `
})
export class DynamicHostComponent {
  @ViewChild('dynamicContainer', { read: ViewContainerRef })
  container!: ViewContainerRef;

  private componentRef?: ComponentRef<AlertComponent>;

  loadComponent(): void {
    // Clear existing components
    this.container.clear();

    // Create component
    this.componentRef = this.container.createComponent(AlertComponent);

    // Set input properties
    this.componentRef.instance.message = 'Dynamic alert!';
    this.componentRef.instance.type = 'warning';

    // Subscribe to outputs
    this.componentRef.instance.closed.subscribe(() => {
      console.log('Alert closed');
      this.destroyComponent();
    });
  }

  destroyComponent(): void {
    this.componentRef?.destroy();
  }
}
```

### Dynamic Component with Data

```typescript
// alert.component.ts
@Component({
  selector: 'app-alert',
  template: `
    <div class="alert alert-{{ type }}">
      {{ message }}
      <button (click)="close()">×</button>
    </div>
  `
})
export class AlertComponent {
  @Input() message: string = '';
  @Input() type: 'success' | 'warning' | 'error' = 'success';
  @Output() closed = new EventEmitter<void>();

  close(): void {
    this.closed.emit();
  }
}
```

### Dynamic Component Service

```typescript
// dynamic-component.service.ts
import { Injectable, ViewContainerRef, ComponentRef } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class DynamicComponentService {
  createComponent<T>(
    container: ViewContainerRef,
    component: Type<T>,
    inputs?: Partial<T>
  ): ComponentRef<T> {
    container.clear();
    const componentRef = container.createComponent(component);

    if (inputs) {
      Object.assign(componentRef.instance, inputs);
    }

    return componentRef;
  }
}
```

---

## Event Emitters

Event emitters allow child components to communicate with parent components through custom events.

### Basic Event Emitter

```typescript
// child.component.ts
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `
    <button (click)="sendMessage()">Send Message to Parent</button>
  `
})
export class ChildComponent {
  @Output() messageEvent = new EventEmitter<string>();

  sendMessage(): void {
    this.messageEvent.emit('Hello from child!');
  }
}
```

```typescript
// parent.component.ts
@Component({
  selector: 'app-parent',
  template: `
    <app-child (messageEvent)="receiveMessage($event)"></app-child>
    <p>Message from child: {{ message }}</p>
  `
})
export class ParentComponent {
  message: string = '';

  receiveMessage(msg: string): void {
    this.message = msg;
  }
}
```

### Event Emitter with Complex Data

```typescript
// Product interface
interface Product {
  id: number;
  name: string;
  price: number;
}

// product-item.component.ts
@Component({
  selector: 'app-product-item',
  template: `
    <div class="product">
      <h3>{{ product.name }}</h3>
      <p>Price: ${{ product.price }}</p>
      <button (click)="addToCart()">Add to Cart</button>
    </div>
  `
})
export class ProductItemComponent {
  @Input() product!: Product;
  @Output() productAdded = new EventEmitter<Product>();

  addToCart(): void {
    this.productAdded.emit(this.product);
  }
}

// product-list.component.ts
@Component({
  selector: 'app-product-list',
  template: `
    <app-product-item
      *ngFor="let product of products"
      [product]="product"
      (productAdded)="onProductAdded($event)">
    </app-product-item>
  `
})
export class ProductListComponent {
  products: Product[] = [
    { id: 1, name: 'Laptop', price: 999 },
    { id: 2, name: 'Mouse', price: 25 }
  ];

  onProductAdded(product: Product): void {
    console.log('Added to cart:', product);
  }
}
```

---

## Sharing Data Between Components

### 1. Parent to Child (@Input)

```typescript
// child.component.ts
@Component({
  selector: 'app-child',
  template: '<p>{{ childMessage }}</p>'
})
export class ChildComponent {
  @Input() childMessage: string = '';
}

// parent.component.ts
@Component({
  selector: 'app-parent',
  template: '<app-child [childMessage]="parentMessage"></app-child>'
})
export class ParentComponent {
  parentMessage: string = 'Message from parent';
}
```

### 2. Child to Parent (@Output)

```typescript
// child.component.ts
@Component({
  selector: 'app-child',
  template: '<button (click)="sendMessage()">Send</button>'
})
export class ChildComponent {
  @Output() messageToParent = new EventEmitter<string>();

  sendMessage(): void {
    this.messageToParent.emit('Message from child');
  }
}

// parent.component.ts
@Component({
  selector: 'app-parent',
  template: `
    <app-child (messageToParent)="receiveMessage($event)"></app-child>
    <p>{{ message }}</p>
  `
})
export class ParentComponent {
  message: string = '';

  receiveMessage(msg: string): void {
    this.message = msg;
  }
}
```

### 3. Parent Accessing Child (@ViewChild)

```typescript
// child.component.ts
@Component({
  selector: 'app-child',
  template: '<p>{{ message }}</p>'
})
export class ChildComponent {
  message: string = 'Default message';

  updateMessage(newMessage: string): void {
    this.message = newMessage;
  }
}

// parent.component.ts
@Component({
  selector: 'app-parent',
  template: `
    <app-child></app-child>
    <button (click)="changeChildMessage()">Update Child</button>
  `
})
export class ParentComponent implements AfterViewInit {
  @ViewChild(ChildComponent) child!: ChildComponent;

  ngAfterViewInit(): void {
    // Access child component after view initialization
    console.log(this.child.message);
  }

  changeChildMessage(): void {
    this.child.updateMessage('Message from parent via ViewChild');
  }
}
```

### 4. Sibling Components (via Service)

```typescript
// data.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class DataService {
  private messageSource = new BehaviorSubject<string>('Default message');
  currentMessage: Observable<string> = this.messageSource.asObservable();

  changeMessage(message: string): void {
    this.messageSource.next(message);
  }
}

// sibling1.component.ts
@Component({
  selector: 'app-sibling1',
  template: '<button (click)="newMessage()">Send to Sibling</button>'
})
export class Sibling1Component {
  constructor(private dataService: DataService) {}

  newMessage(): void {
    this.dataService.changeMessage('Message from Sibling 1');
  }
}

// sibling2.component.ts
@Component({
  selector: 'app-sibling2',
  template: '<p>{{ message }}</p>'
})
export class Sibling2Component implements OnInit {
  message: string = '';

  constructor(private dataService: DataService) {}

  ngOnInit(): void {
    this.dataService.currentMessage.subscribe(message => {
      this.message = message;
    });
  }
}
```

### 5. Unrelated Components (via Service)

```typescript
// notification.service.ts
@Injectable({
  providedIn: 'root'
})
export class NotificationService {
  private notificationSubject = new Subject<string>();
  notifications$ = this.notificationSubject.asObservable();

  notify(message: string): void {
    this.notificationSubject.next(message);
  }
}

// any-component.ts
@Component({
  selector: 'app-any',
  template: '<button (click)="sendNotification()">Notify</button>'
})
export class AnyComponent {
  constructor(private notificationService: NotificationService) {}

  sendNotification(): void {
    this.notificationService.notify('Important notification!');
  }
}

// notification-display.component.ts
@Component({
  selector: 'app-notification-display',
  template: `
    <div *ngFor="let notification of notifications" class="alert">
      {{ notification }}
    </div>
  `
})
export class NotificationDisplayComponent implements OnInit {
  notifications: string[] = [];

  constructor(private notificationService: NotificationService) {}

  ngOnInit(): void {
    this.notificationService.notifications$.subscribe(notification => {
      this.notifications.push(notification);
    });
  }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Components | Building blocks with template, styles, and logic |
| @Component | Decorator that defines component metadata |
| Lifecycle | Hooks for initialization, changes, and cleanup |
| Styles | Scoped by default, multiple styling approaches |
| Change Detection | Automatic view updates, optimizable with OnPush |
| Dynamic Components | Programmatically created at runtime |
| Event Emitters | Custom events for child-to-parent communication |
| Data Sharing | Multiple patterns for component communication |

## Next Topic

Continue to [Modules](./03-modules.md) to learn about organizing Angular applications with NgModules.
