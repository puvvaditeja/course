# Directives and Pipes

## Angular Directives

Directives are classes that add behavior to elements in your Angular applications. There are three types of directives in Angular:

1. **Components**: Directives with a template
2. **Structural Directives**: Change the DOM layout by adding/removing elements
3. **Attribute Directives**: Change the appearance or behavior of an element

---

## Structural Directives

Structural directives modify the structure of the DOM by adding, removing, or manipulating elements. They are prefixed with an asterisk (*).

### *ngIf - Conditional Rendering

```typescript
@Component({
  selector: 'app-user-greeting',
  template: `
    <div *ngIf="isLoggedIn">
      <h2>Welcome back, {{ username }}!</h2>
    </div>

    <div *ngIf="!isLoggedIn">
      <h2>Please log in</h2>
    </div>

    <!-- ngIf with else -->
    <div *ngIf="isLoggedIn; else loggedOut">
      <h2>Welcome, {{ username }}</h2>
    </div>
    <ng-template #loggedOut>
      <h2>Please log in</h2>
    </ng-template>

    <!-- ngIf with then and else -->
    <div *ngIf="isLoggedIn; then loggedIn else loggedOut"></div>
    <ng-template #loggedIn>
      <h2>Welcome, {{ username }}</h2>
    </ng-template>
    <ng-template #loggedOut>
      <h2>Please log in</h2>
    </ng-template>
  `
})
export class UserGreetingComponent {
  isLoggedIn: boolean = true;
  username: string = 'John Doe';
}
```

### *ngFor - List Rendering

```typescript
@Component({
  selector: 'app-product-list',
  template: `
    <!-- Basic ngFor -->
    <div *ngFor="let product of products">
      {{ product.name }} - ${{ product.price }}
    </div>

    <!-- ngFor with index -->
    <div *ngFor="let product of products; let i = index">
      {{ i + 1 }}. {{ product.name }}
    </div>

    <!-- ngFor with multiple variables -->
    <div *ngFor="let product of products;
                 let i = index;
                 let first = first;
                 let last = last;
                 let even = even;
                 let odd = odd">
      <div [class.first]="first"
           [class.last]="last"
           [class.even]="even"
           [class.odd]="odd">
        {{ i }}. {{ product.name }}
      </div>
    </div>

    <!-- ngFor with trackBy (for performance) -->
    <div *ngFor="let product of products; trackBy: trackByProductId">
      {{ product.name }}
    </div>
  `
})
export class ProductListComponent {
  products = [
    { id: 1, name: 'Laptop', price: 999 },
    { id: 2, name: 'Mouse', price: 25 },
    { id: 3, name: 'Keyboard', price: 75 }
  ];

  trackByProductId(index: number, product: any): number {
    return product.id;  // Improve performance by tracking by ID
  }
}
```

### *ngSwitch - Multiple Conditional Cases

```typescript
@Component({
  selector: 'app-status-display',
  template: `
    <div [ngSwitch]="status">
      <div *ngSwitchCase="'pending'">
        <span class="badge badge-warning">Pending</span>
      </div>
      <div *ngSwitchCase="'approved'">
        <span class="badge badge-success">Approved</span>
      </div>
      <div *ngSwitchCase="'rejected'">
        <span class="badge badge-danger">Rejected</span>
      </div>
      <div *ngSwitchDefault>
        <span class="badge badge-secondary">Unknown</span>
      </div>
    </div>

    <!-- More complex example -->
    <div [ngSwitch]="userRole">
      <app-admin-dashboard *ngSwitchCase="'admin'"></app-admin-dashboard>
      <app-user-dashboard *ngSwitchCase="'user'"></app-user-dashboard>
      <app-guest-view *ngSwitchDefault></app-guest-view>
    </div>
  `
})
export class StatusDisplayComponent {
  status: 'pending' | 'approved' | 'rejected' | 'unknown' = 'pending';
  userRole: string = 'user';
}
```

### ng-container and ng-template

```typescript
@Component({
  selector: 'app-container-demo',
  template: `
    <!-- ng-container: Grouping without extra DOM element -->
    <ng-container *ngIf="showContent">
      <h2>Title</h2>
      <p>Content</p>
      <p>More content</p>
    </ng-container>

    <!-- Multiple structural directives -->
    <ng-container *ngIf="isLoggedIn">
      <div *ngFor="let item of items">
        {{ item }}
      </div>
    </ng-container>

    <!-- ng-template: Define template for later use -->
    <ng-template #loading>
      <div class="spinner">Loading...</div>
    </ng-template>

    <div *ngIf="isLoading; else content">
      <ng-container *ngTemplateOutlet="loading"></ng-container>
    </div>
    <ng-template #content>
      <p>Content loaded!</p>
    </ng-template>
  `
})
export class ContainerDemoComponent {
  showContent: boolean = true;
  isLoggedIn: boolean = true;
  isLoading: boolean = false;
  items: string[] = ['Item 1', 'Item 2', 'Item 3'];
}
```

---

## Attribute Directives

Attribute directives change the appearance or behavior of an element, component, or another directive.

### Built-in Attribute Directives

#### ngClass

```typescript
@Component({
  selector: 'app-class-demo',
  template: `
    <!-- Single class -->
    <div [class.active]="isActive">Single class binding</div>

    <!-- Multiple classes with object -->
    <div [ngClass]="{
      'active': isActive,
      'disabled': isDisabled,
      'highlighted': isHighlighted
    }">
      Multiple class bindings
    </div>

    <!-- Classes from array -->
    <div [ngClass]="['class1', 'class2', 'class3']">
      Array of classes
    </div>

    <!-- Classes from string -->
    <div [ngClass]="classString">
      String of classes
    </div>

    <!-- Classes from method -->
    <div [ngClass]="getClasses()">
      Classes from method
    </div>
  `,
  styles: [`
    .active { background-color: green; color: white; }
    .disabled { opacity: 0.5; pointer-events: none; }
    .highlighted { border: 2px solid yellow; }
  `]
})
export class ClassDemoComponent {
  isActive: boolean = true;
  isDisabled: boolean = false;
  isHighlighted: boolean = true;
  classString: string = 'class1 class2';

  getClasses() {
    return {
      'active': this.isActive,
      'disabled': this.isDisabled
    };
  }
}
```

#### ngStyle

```typescript
@Component({
  selector: 'app-style-demo',
  template: `
    <!-- Single style -->
    <div [style.color]="textColor">Single style binding</div>

    <!-- Style with unit -->
    <div [style.font-size.px]="fontSize">Font size in pixels</div>

    <!-- Multiple styles with object -->
    <div [ngStyle]="{
      'color': textColor,
      'font-size': fontSize + 'px',
      'background-color': backgroundColor,
      'padding': '10px',
      'border-radius': '5px'
    }">
      Multiple style bindings
    </div>

    <!-- Styles from method -->
    <div [ngStyle]="getStyles()">
      Styles from method
    </div>
  `
})
export class StyleDemoComponent {
  textColor: string = 'blue';
  fontSize: number = 16;
  backgroundColor: string = '#f0f0f0';

  getStyles() {
    return {
      'color': this.textColor,
      'font-size': `${this.fontSize}px`,
      'background-color': this.backgroundColor
    };
  }
}
```

#### ngModel (Two-way Binding)

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';  // Required!

@Component({
  selector: 'app-form-demo',
  template: `
    <!-- Two-way data binding -->
    <input [(ngModel)]="username" placeholder="Enter username">
    <p>Hello, {{ username }}!</p>

    <!-- Separate property and event bindings -->
    <input [ngModel]="email"
           (ngModelChange)="onEmailChange($event)"
           placeholder="Enter email">
    <p>Email: {{ email }}</p>

    <!-- With form controls -->
    <input [(ngModel)]="password"
           type="password"
           name="password"
           #passwordInput="ngModel"
           required
           minlength="6">
    <div *ngIf="passwordInput.invalid && passwordInput.touched">
      Password must be at least 6 characters
    </div>
  `
})
export class FormDemoComponent {
  username: string = '';
  email: string = '';
  password: string = '';

  onEmailChange(value: string): void {
    this.email = value.toLowerCase();
  }
}
```

### Creating Custom Attribute Directives

#### Basic Attribute Directive

```bash
ng generate directive directives/highlight
```

```typescript
// highlight.directive.ts
import { Directive, ElementRef, HostListener, Input } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  @Input() appHighlight: string = 'yellow';  // Default color
  @Input() defaultColor: string = 'transparent';

  constructor(private el: ElementRef) {}

  ngOnInit(): void {
    this.el.nativeElement.style.backgroundColor = this.defaultColor;
  }

  @HostListener('mouseenter') onMouseEnter(): void {
    this.highlight(this.appHighlight);
  }

  @HostListener('mouseleave') onMouseLeave(): void {
    this.highlight(this.defaultColor);
  }

  private highlight(color: string): void {
    this.el.nativeElement.style.backgroundColor = color;
  }
}
```

```html
<!-- Usage -->
<p appHighlight>Highlight with default yellow</p>
<p [appHighlight]="'lightblue'">Highlight with light blue</p>
<p appHighlight="orange" defaultColor="lightgray">
  Highlight with orange, default light gray
</p>
```

#### Advanced Attribute Directive

```typescript
// tooltip.directive.ts
import {
  Directive,
  Input,
  ElementRef,
  HostListener,
  Renderer2,
  OnDestroy
} from '@angular/core';

@Directive({
  selector: '[appTooltip]'
})
export class TooltipDirective implements OnDestroy {
  @Input() appTooltip: string = '';
  @Input() tooltipPosition: 'top' | 'bottom' | 'left' | 'right' = 'top';

  private tooltipElement?: HTMLElement;

  constructor(
    private el: ElementRef,
    private renderer: Renderer2
  ) {}

  @HostListener('mouseenter') onMouseEnter(): void {
    if (!this.tooltipElement && this.appTooltip) {
      this.showTooltip();
    }
  }

  @HostListener('mouseleave') onMouseLeave(): void {
    if (this.tooltipElement) {
      this.hideTooltip();
    }
  }

  private showTooltip(): void {
    this.tooltipElement = this.renderer.createElement('div');
    this.renderer.appendChild(
      this.tooltipElement,
      this.renderer.createText(this.appTooltip)
    );

    this.renderer.addClass(this.tooltipElement, 'tooltip');
    this.renderer.addClass(this.tooltipElement, `tooltip-${this.tooltipPosition}`);

    this.renderer.appendChild(document.body, this.tooltipElement);

    this.setPosition();
  }

  private hideTooltip(): void {
    if (this.tooltipElement) {
      this.renderer.removeChild(document.body, this.tooltipElement);
      this.tooltipElement = undefined;
    }
  }

  private setPosition(): void {
    if (!this.tooltipElement) return;

    const hostPos = this.el.nativeElement.getBoundingClientRect();
    const tooltipPos = this.tooltipElement.getBoundingClientRect();

    let top: number, left: number;

    switch (this.tooltipPosition) {
      case 'top':
        top = hostPos.top - tooltipPos.height - 10;
        left = hostPos.left + (hostPos.width - tooltipPos.width) / 2;
        break;
      case 'bottom':
        top = hostPos.bottom + 10;
        left = hostPos.left + (hostPos.width - tooltipPos.width) / 2;
        break;
      case 'left':
        top = hostPos.top + (hostPos.height - tooltipPos.height) / 2;
        left = hostPos.left - tooltipPos.width - 10;
        break;
      case 'right':
        top = hostPos.top + (hostPos.height - tooltipPos.height) / 2;
        left = hostPos.right + 10;
        break;
    }

    this.renderer.setStyle(this.tooltipElement, 'top', `${top}px`);
    this.renderer.setStyle(this.tooltipElement, 'left', `${left}px`);
  }

  ngOnDestroy(): void {
    this.hideTooltip();
  }
}
```

```html
<!-- Usage -->
<button appTooltip="Click to save" tooltipPosition="top">Save</button>
<button appTooltip="Click to cancel" tooltipPosition="right">Cancel</button>
```

---

## Pipes

Pipes transform displayed values within a template. Angular provides several built-in pipes and allows you to create custom pipes.

### Built-in Pipes

#### DatePipe

```typescript
@Component({
  selector: 'app-date-demo',
  template: `
    <p>Default: {{ today | date }}</p>
    <p>Short: {{ today | date:'short' }}</p>
    <p>Medium: {{ today | date:'medium' }}</p>
    <p>Long: {{ today | date:'long' }}</p>
    <p>Full: {{ today | date:'full' }}</p>
    <p>Custom: {{ today | date:'dd/MM/yyyy' }}</p>
    <p>Custom with time: {{ today | date:'dd/MM/yyyy HH:mm:ss' }}</p>
    <p>Short date: {{ today | date:'shortDate' }}</p>
    <p>Short time: {{ today | date:'shortTime' }}</p>
  `
})
export class DateDemoComponent {
  today: Date = new Date();
}
```

#### CurrencyPipe

```typescript
@Component({
  selector: 'app-currency-demo',
  template: `
    <p>Default: {{ price | currency }}</p>
    <p>EUR: {{ price | currency:'EUR' }}</p>
    <p>GBP with symbol: {{ price | currency:'GBP':'symbol':'1.2-2' }}</p>
    <p>USD with code: {{ price | currency:'USD':'code' }}</p>
    <p>JPY: {{ price | currency:'JPY' }}</p>
  `
})
export class CurrencyDemoComponent {
  price: number = 1234.56;
}
```

#### DecimalPipe (NumberPipe)

```typescript
@Component({
  selector: 'app-number-demo',
  template: `
    <p>Default: {{ value | number }}</p>
    <p>Min 3 digits: {{ value | number:'3.1-5' }}</p>
    <p>2 decimal places: {{ value | number:'1.2-2' }}</p>
    <p>Percentage: {{ percentage | percent }}</p>
    <p>Percentage with decimals: {{ percentage | percent:'1.2-2' }}</p>
  `
})
export class NumberDemoComponent {
  value: number = 1234.56789;
  percentage: number = 0.259;
}
```

#### UpperCase, LowerCase, TitleCase

```typescript
@Component({
  selector: 'app-text-demo',
  template: `
    <p>Original: {{ text }}</p>
    <p>Uppercase: {{ text | uppercase }}</p>
    <p>Lowercase: {{ text | lowercase }}</p>
    <p>Titlecase: {{ text | titlecase }}</p>
  `
})
export class TextDemoComponent {
  text: string = 'hello WORLD from Angular';
}
```

#### SlicePipe

```typescript
@Component({
  selector: 'app-slice-demo',
  template: `
    <!-- Array slicing -->
    <p>Full array: {{ items }}</p>
    <p>First 3: {{ items | slice:0:3 }}</p>
    <p>Last 2: {{ items | slice:-2 }}</p>
    <p>From index 2: {{ items | slice:2 }}</p>

    <!-- String slicing -->
    <p>Full: {{ longText }}</p>
    <p>First 20 chars: {{ longText | slice:0:20 }}...</p>
  `
})
export class SliceDemoComponent {
  items: number[] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  longText: string = 'This is a very long text that needs to be truncated';
}
```

#### JsonPipe

```typescript
@Component({
  selector: 'app-json-demo',
  template: `
    <h3>User Object:</h3>
    <pre>{{ user | json }}</pre>
  `
})
export class JsonDemoComponent {
  user = {
    name: 'John Doe',
    email: 'john@example.com',
    address: {
      street: '123 Main St',
      city: 'New York',
      country: 'USA'
    }
  };
}
```

#### AsyncPipe

```typescript
import { Component } from '@angular/core';
import { Observable, interval } from 'rxjs';
import { map } from 'rxjs/operators';

@Component({
  selector: 'app-async-demo',
  template: `
    <!-- Automatically subscribes and unsubscribes -->
    <p>Counter: {{ counter$ | async }}</p>
    <p>Time: {{ time$ | async | date:'medium' }}</p>

    <!-- With loading state -->
    <div *ngIf="data$ | async as data; else loading">
      <h3>{{ data.title }}</h3>
      <p>{{ data.content }}</p>
    </div>
    <ng-template #loading>
      <p>Loading...</p>
    </ng-template>
  `
})
export class AsyncDemoComponent {
  counter$: Observable<number> = interval(1000);
  time$: Observable<Date> = interval(1000).pipe(
    map(() => new Date())
  );
  data$: Observable<any> = this.fetchData();

  fetchData(): Observable<any> {
    return new Observable(observer => {
      setTimeout(() => {
        observer.next({
          title: 'Data Title',
          content: 'Data content loaded'
        });
      }, 2000);
    });
  }
}
```

### Chaining Pipes

```typescript
@Component({
  selector: 'app-chain-demo',
  template: `
    <p>{{ today | date:'fullDate' | uppercase }}</p>
    <p>{{ price | currency:'USD':'symbol':'1.2-2' | lowercase }}</p>
    <p>{{ longText | slice:0:50 | titlecase }}</p>
  `
})
export class ChainDemoComponent {
  today: Date = new Date();
  price: number = 99.99;
  longText: string = 'the quick brown fox jumps over the lazy dog';
}
```

---

## Custom Pipes

### Creating a Custom Pipe

```bash
ng generate pipe pipes/truncate
```

#### Simple Custom Pipe

```typescript
// truncate.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'truncate'
})
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 50, trail: string = '...'): string {
    if (!value) return '';

    return value.length > limit
      ? value.substring(0, limit) + trail
      : value;
  }
}
```

```html
<!-- Usage -->
<p>{{ longText | truncate:20 }}</p>
<p>{{ longText | truncate:30:'---' }}</p>
```

#### Custom Pipe with Multiple Parameters

```typescript
// filter.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'filter'
})
export class FilterPipe implements PipeTransform {
  transform(items: any[], searchText: string, filterField: string): any[] {
    if (!items || !searchText) {
      return items;
    }

    searchText = searchText.toLowerCase();

    return items.filter(item => {
      return item[filterField]?.toLowerCase().includes(searchText);
    });
  }
}
```

```typescript
@Component({
  selector: 'app-filter-demo',
  template: `
    <input [(ngModel)]="searchTerm" placeholder="Search products">

    <div *ngFor="let product of products | filter:searchTerm:'name'">
      {{ product.name }} - ${{ product.price }}
    </div>
  `
})
export class FilterDemoComponent {
  searchTerm: string = '';
  products = [
    { name: 'Laptop', price: 999 },
    { name: 'Mouse', price: 25 },
    { name: 'Keyboard', price: 75 },
    { name: 'Monitor', price: 299 }
  ];
}
```

#### Impure Pipe (Use with Caution)

```typescript
// sort.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'sort',
  pure: false  // Pipe runs on every change detection cycle
})
export class SortPipe implements PipeTransform {
  transform(items: any[], field: string, order: 'asc' | 'desc' = 'asc'): any[] {
    if (!items || !field) {
      return items;
    }

    return items.sort((a, b) => {
      const aValue = a[field];
      const bValue = b[field];

      if (aValue < bValue) {
        return order === 'asc' ? -1 : 1;
      }
      if (aValue > bValue) {
        return order === 'asc' ? 1 : -1;
      }
      return 0;
    });
  }
}
```

#### Async Transform Pipe

```typescript
// safe-html.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';
import { DomSanitizer, SafeHtml } from '@angular/platform-browser';

@Pipe({
  name: 'safeHtml'
})
export class SafeHtmlPipe implements PipeTransform {
  constructor(private sanitizer: DomSanitizer) {}

  transform(value: string): SafeHtml {
    return this.sanitizer.sanitize(1, value) || '';
  }
}
```

```html
<!-- Usage -->
<div [innerHTML]="htmlContent | safeHtml"></div>
```

#### Time Ago Pipe

```typescript
// time-ago.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'timeAgo'
})
export class TimeAgoPipe implements PipeTransform {
  transform(value: Date | string): string {
    if (!value) return '';

    const date = value instanceof Date ? value : new Date(value);
    const now = new Date();
    const seconds = Math.floor((now.getTime() - date.getTime()) / 1000);

    if (seconds < 60) {
      return 'just now';
    }

    const minutes = Math.floor(seconds / 60);
    if (minutes < 60) {
      return `${minutes} minute${minutes > 1 ? 's' : ''} ago`;
    }

    const hours = Math.floor(minutes / 60);
    if (hours < 24) {
      return `${hours} hour${hours > 1 ? 's' : ''} ago`;
    }

    const days = Math.floor(hours / 24);
    if (days < 30) {
      return `${days} day${days > 1 ? 's' : ''} ago`;
    }

    const months = Math.floor(days / 30);
    if (months < 12) {
      return `${months} month${months > 1 ? 's' : ''} ago`;
    }

    const years = Math.floor(months / 12);
    return `${years} year${years > 1 ? 's' : ''} ago`;
  }
}
```

```html
<!-- Usage -->
<p>Posted {{ postDate | timeAgo }}</p>
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Structural Directives | Modify DOM structure (*ngIf, *ngFor, *ngSwitch) |
| Attribute Directives | Modify element behavior (ngClass, ngStyle, ngModel) |
| Custom Directives | Create reusable behaviors with @Directive |
| Built-in Pipes | Transform data display (date, currency, async, etc.) |
| Custom Pipes | Create custom transformations with @Pipe |
| Pure vs Impure | Pure runs on reference change, impure on every cycle |

## Next Topic

Continue to [Dependency Injection](./05-dependency-injection.md) to learn about Angular's dependency injection system.
