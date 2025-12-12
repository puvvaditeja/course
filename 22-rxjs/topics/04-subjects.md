# Subjects

## What is a Subject?

A Subject is a special type of Observable that allows values to be multicasted to many Observers. While plain Observables are unicast (each subscribed Observer owns an independent execution), Subjects are multicast.

### Key Characteristics

- **Both Observable and Observer**: Can subscribe to Observables and emit values
- **Multicast**: Share a single execution among multiple subscribers
- **Hot**: Starts emitting values regardless of subscriptions
- **Manual Control**: You can manually call next(), error(), and complete()
- **State Management**: Useful for managing application state

### Subject vs Observable

```typescript
import { Subject, Observable } from 'rxjs';

// Regular Observable (Unicast)
const observable$ = new Observable(subscriber => {
  console.log('Observable executed');
  subscriber.next(Math.random());
});

observable$.subscribe(value => console.log('Sub 1:', value));
observable$.subscribe(value => console.log('Sub 2:', value));
// Output:
// Observable executed
// Sub 1: 0.123...
// Observable executed
// Sub 2: 0.456...
// Two different random numbers!

// Subject (Multicast)
const subject$ = new Subject<number>();

subject$.subscribe(value => console.log('Sub 1:', value));
subject$.subscribe(value => console.log('Sub 2:', value));

subject$.next(Math.random());
// Output:
// Sub 1: 0.789...
// Sub 2: 0.789...
// Same random number for both!
```

---

## Basic Subject

The basic Subject emits values to all current subscribers.

### Creating and Using a Subject

```typescript
import { Subject } from 'rxjs';

const subject$ = new Subject<number>();

// Subscribe before emitting
subject$.subscribe({
  next: value => console.log('Observer A:', value),
  error: err => console.error('Observer A error:', err),
  complete: () => console.log('Observer A complete')
});

// Emit values
subject$.next(1);
subject$.next(2);

// Second subscriber
subject$.subscribe({
  next: value => console.log('Observer B:', value),
  complete: () => console.log('Observer B complete')
});

subject$.next(3);
subject$.complete();

// Output:
// Observer A: 1
// Observer A: 2
// Observer A: 3
// Observer B: 3
// Observer A complete
// Observer B complete
```

### Late Subscribers Miss Earlier Values

```typescript
import { Subject } from 'rxjs';

const subject$ = new Subject<string>();

subject$.next('Hello');
subject$.next('World');

// This subscriber misses "Hello" and "World"
subject$.subscribe(value => console.log('Late subscriber:', value));

subject$.next('RxJS');
// Output: Late subscriber: RxJS
```

### Subject as Observer

Subjects can act as Observers and subscribe to Observables:

```typescript
import { Subject, interval } from 'rxjs';
import { take } from 'rxjs/operators';

const subject$ = new Subject<number>();

// Multiple subscribers to the subject
subject$.subscribe(value => console.log('Sub 1:', value));
subject$.subscribe(value => console.log('Sub 2:', value));

// Subject subscribes to an Observable
const source$ = interval(1000).pipe(take(3));
source$.subscribe(subject$);

// Output (every second):
// Sub 1: 0
// Sub 2: 0
// Sub 1: 1
// Sub 2: 1
// Sub 1: 2
// Sub 2: 2
```

### Practical Example: Event Bus

```typescript
import { Subject } from 'rxjs';
import { filter } from 'rxjs/operators';

interface AppEvent {
  type: string;
  payload: any;
}

class EventBus {
  private eventSubject$ = new Subject<AppEvent>();

  // Emit an event
  emit(event: AppEvent): void {
    this.eventSubject$.next(event);
  }

  // Subscribe to specific event types
  on(eventType: string) {
    return this.eventSubject$.pipe(
      filter(event => event.type === eventType)
    );
  }

  // Subscribe to all events
  onAll() {
    return this.eventSubject$.asObservable();
  }
}

// Usage
const eventBus = new EventBus();

// Component A listens for user events
eventBus.on('USER_LOGGED_IN').subscribe(event => {
  console.log('User logged in:', event.payload);
});

// Component B listens for data events
eventBus.on('DATA_UPDATED').subscribe(event => {
  console.log('Data updated:', event.payload);
});

// Emit events from anywhere
eventBus.emit({ type: 'USER_LOGGED_IN', payload: { userId: 123 } });
eventBus.emit({ type: 'DATA_UPDATED', payload: { records: 50 } });
```

---

## BehaviorSubject

BehaviorSubject requires an initial value and always emits the most recent value to new subscribers.

### Key Features

- **Initial Value**: Must be initialized with a value
- **Current Value**: Always has a current value (accessible via `.value`)
- **Replays Last**: New subscribers immediately receive the last emitted value
- **State Storage**: Perfect for representing current state

### Creating and Using BehaviorSubject

```typescript
import { BehaviorSubject } from 'rxjs';

// Initialize with a value
const subject$ = new BehaviorSubject<number>(0);

// First subscriber gets initial value
subject$.subscribe(value => console.log('Sub 1:', value));
// Output: Sub 1: 0

subject$.next(1);
subject$.next(2);
// Output:
// Sub 1: 1
// Sub 1: 2

// New subscriber gets current value (2) immediately
subject$.subscribe(value => console.log('Sub 2:', value));
// Output: Sub 2: 2

subject$.next(3);
// Output:
// Sub 1: 3
// Sub 2: 3

// Access current value synchronously
console.log('Current value:', subject$.value);
// Output: Current value: 3
```

### State Management with BehaviorSubject

```typescript
import { BehaviorSubject } from 'rxjs';
import { map } from 'rxjs/operators';

interface User {
  id: number;
  name: string;
  email: string;
}

class UserService {
  private currentUserSubject$ = new BehaviorSubject<User | null>(null);

  // Public Observable (read-only)
  currentUser$ = this.currentUserSubject$.asObservable();

  // Derived observables
  isLoggedIn$ = this.currentUserSubject$.pipe(
    map(user => user !== null)
  );

  userName$ = this.currentUserSubject$.pipe(
    map(user => user?.name ?? 'Guest')
  );

  // Get current value
  get currentUser(): User | null {
    return this.currentUserSubject$.value;
  }

  // Update user
  setUser(user: User): void {
    this.currentUserSubject$.next(user);
  }

  // Logout
  logout(): void {
    this.currentUserSubject$.next(null);
  }
}

// Usage
const userService = new UserService();

userService.currentUser$.subscribe(user => {
  console.log('Current user:', user);
});

userService.isLoggedIn$.subscribe(loggedIn => {
  console.log('Logged in:', loggedIn);
});

userService.setUser({ id: 1, name: 'Alice', email: 'alice@example.com' });
// Output:
// Current user: { id: 1, name: 'Alice', email: 'alice@example.com' }
// Logged in: true

userService.logout();
// Output:
// Current user: null
// Logged in: false
```

### Form State Management

```typescript
import { BehaviorSubject, combineLatest } from 'rxjs';
import { map } from 'rxjs/operators';

interface FormState {
  email: string;
  password: string;
  rememberMe: boolean;
}

class LoginForm {
  private formState$ = new BehaviorSubject<FormState>({
    email: '',
    password: '',
    rememberMe: false
  });

  // Selectors
  email$ = this.formState$.pipe(map(state => state.email));
  password$ = this.formState$.pipe(map(state => state.password));
  rememberMe$ = this.formState$.pipe(map(state => state.rememberMe));

  // Validation
  isEmailValid$ = this.email$.pipe(
    map(email => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email))
  );

  isPasswordValid$ = this.password$.pipe(
    map(password => password.length >= 8)
  );

  isFormValid$ = combineLatest([
    this.isEmailValid$,
    this.isPasswordValid$
  ]).pipe(
    map(([emailValid, passwordValid]) => emailValid && passwordValid)
  );

  // Actions
  updateEmail(email: string): void {
    this.formState$.next({ ...this.formState$.value, email });
  }

  updatePassword(password: string): void {
    this.formState$.next({ ...this.formState$.value, password });
  }

  toggleRememberMe(): void {
    this.formState$.next({
      ...this.formState$.value,
      rememberMe: !this.formState$.value.rememberMe
    });
  }

  reset(): void {
    this.formState$.next({
      email: '',
      password: '',
      rememberMe: false
    });
  }
}

// Usage
const loginForm = new LoginForm();

loginForm.isFormValid$.subscribe(valid => {
  console.log('Form valid:', valid);
});

loginForm.updateEmail('alice@example.com');
loginForm.updatePassword('SecurePass123');
// Output: Form valid: true
```

---

## ReplaySubject

ReplaySubject records multiple values and replays them to new subscribers.

### Key Features

- **Buffer**: Stores a specified number of values
- **Time Window**: Can specify how long to buffer values
- **Replay**: New subscribers receive buffered values
- **History**: Maintains history of emissions

### Creating and Using ReplaySubject

```typescript
import { ReplaySubject } from 'rxjs';

// Replay last 3 values
const subject$ = new ReplaySubject<number>(3);

subject$.next(1);
subject$.next(2);
subject$.next(3);
subject$.next(4);

// New subscriber gets last 3 values
subject$.subscribe(value => console.log('Sub 1:', value));
// Output:
// Sub 1: 2
// Sub 1: 3
// Sub 1: 4

subject$.next(5);
// Output: Sub 1: 5

// Another new subscriber
subject$.subscribe(value => console.log('Sub 2:', value));
// Output:
// Sub 2: 3
// Sub 2: 4
// Sub 2: 5
```

### Time-Based Buffer

```typescript
import { ReplaySubject } from 'rxjs';

// Replay values from last 1000ms
const subject$ = new ReplaySubject<number>(100, 1000);

subject$.next(1);

setTimeout(() => subject$.next(2), 500);
setTimeout(() => subject$.next(3), 1000);

// Subscribe after 1500ms
setTimeout(() => {
  subject$.subscribe(value => console.log('Late subscriber:', value));
  // Receives only values from last 1000ms (values 2 and 3)
}, 1500);
```

### Caching API Responses

```typescript
import { ReplaySubject } from 'rxjs';
import { ajax } from 'rxjs/ajax';

class DataService {
  private cache$ = new ReplaySubject<User[]>(1);
  private loaded = false;

  getUsers() {
    if (!this.loaded) {
      ajax.getJSON<User[]>('/api/users').subscribe(
        users => {
          this.cache$.next(users);
          this.loaded = true;
        },
        error => this.cache$.error(error)
      );
    }

    return this.cache$.asObservable();
  }
}

// Usage
const service = new DataService();

// First call - makes HTTP request
service.getUsers().subscribe(users => console.log('Component 1:', users));

// Second call - gets cached result
service.getUsers().subscribe(users => console.log('Component 2:', users));
// No additional HTTP request!
```

### Activity Log

```typescript
import { ReplaySubject } from 'rxjs';

interface Activity {
  timestamp: Date;
  action: string;
  user: string;
}

class ActivityLogger {
  // Keep last 50 activities
  private activitySubject$ = new ReplaySubject<Activity>(50);

  logActivity(action: string, user: string): void {
    this.activitySubject$.next({
      timestamp: new Date(),
      action,
      user
    });
  }

  getActivityLog() {
    return this.activitySubject$.asObservable();
  }
}

// Usage
const logger = new ActivityLogger();

logger.logActivity('Login', 'Alice');
logger.logActivity('View Dashboard', 'Alice');
logger.logActivity('Edit Profile', 'Alice');

// New component gets recent activity history
logger.getActivityLog().subscribe(activity => {
  console.log(`${activity.timestamp}: ${activity.user} - ${activity.action}`);
});
```

---

## AsyncSubject

AsyncSubject only emits the last value when it completes.

### Key Features

- **Last Value Only**: Emits only the final value
- **On Complete**: Only emits when complete() is called
- **Promise-like**: Similar to Promise behavior
- **Single Value**: Good for operations that produce a single result

### Creating and Using AsyncSubject

```typescript
import { AsyncSubject } from 'rxjs';

const subject$ = new AsyncSubject<number>();

subject$.subscribe(value => console.log('Sub 1:', value));

subject$.next(1);
subject$.next(2);
subject$.next(3);
subject$.next(4);

// New subscriber before completion
subject$.subscribe(value => console.log('Sub 2:', value));

// Nothing emitted yet...

subject$.complete();
// Output:
// Sub 1: 4
// Sub 2: 4
// Both receive only the last value (4)
```

### Without Completion

```typescript
import { AsyncSubject } from 'rxjs';

const subject$ = new AsyncSubject<number>();

subject$.subscribe(value => console.log('Value:', value));

subject$.next(1);
subject$.next(2);
subject$.next(3);

// Without calling complete(), subscribers never receive any values
// No output!
```

### Async Operation Result

```typescript
import { AsyncSubject } from 'rxjs';

class ComputationService {
  performHeavyComputation(): AsyncSubject<number> {
    const result$ = new AsyncSubject<number>();

    // Simulate heavy computation
    setTimeout(() => {
      let sum = 0;
      for (let i = 0; i < 1000000; i++) {
        sum += i;
      }
      result$.next(sum);
      result$.complete();
    }, 2000);

    return result$;
  }
}

// Usage
const service = new ComputationService();
const computation$ = service.performHeavyComputation();

computation$.subscribe(result => {
  console.log('Computation result:', result);
});

// Another subscriber
setTimeout(() => {
  computation$.subscribe(result => {
    console.log('Late subscriber got result:', result);
  });
}, 3000);
// Late subscriber still gets the result
```

---

## Subject Comparison

### Feature Matrix

| Feature | Subject | BehaviorSubject | ReplaySubject | AsyncSubject |
|---------|---------|----------------|---------------|--------------|
| Initial value | No | Yes (required) | No | No |
| Replay to new subscribers | No | Last value | N values | Last value (on complete) |
| Current value access | No | Yes (.value) | No | No |
| Emits before complete | Yes | Yes | Yes | No |
| Buffer size | 0 | 1 | Configurable | 1 |
| Use case | Event bus | State management | History/Cache | Single async result |

### Side-by-Side Comparison

```typescript
import { Subject, BehaviorSubject, ReplaySubject, AsyncSubject } from 'rxjs';

console.log('=== Subject ===');
const subject$ = new Subject<number>();
subject$.subscribe(v => console.log('  Early:', v));
subject$.next(1);
subject$.next(2);
subject$.subscribe(v => console.log('  Late:', v));
subject$.next(3);
// Output:
// Early: 1
// Early: 2
// Early: 3
// Late: 3

console.log('\n=== BehaviorSubject ===');
const behavior$ = new BehaviorSubject<number>(0);
behavior$.subscribe(v => console.log('  Early:', v));
behavior$.next(1);
behavior$.next(2);
behavior$.subscribe(v => console.log('  Late:', v));
behavior$.next(3);
// Output:
// Early: 0
// Early: 1
// Early: 2
// Late: 2
// Early: 3
// Late: 3

console.log('\n=== ReplaySubject(2) ===');
const replay$ = new ReplaySubject<number>(2);
replay$.subscribe(v => console.log('  Early:', v));
replay$.next(1);
replay$.next(2);
replay$.next(3);
replay$.subscribe(v => console.log('  Late:', v));
replay$.next(4);
// Output:
// Early: 1
// Early: 2
// Early: 3
// Late: 2
// Late: 3
// Early: 4
// Late: 4

console.log('\n=== AsyncSubject ===');
const async$ = new AsyncSubject<number>();
async$.subscribe(v => console.log('  Early:', v));
async$.next(1);
async$.next(2);
async$.subscribe(v => console.log('  Late:', v));
async$.next(3);
async$.complete();
// Output:
// Early: 3
// Late: 3
```

---

## Best Practices

### 1. Expose Observable, Keep Subject Private

```typescript
import { Subject } from 'rxjs';

class DataService {
  // Private subject
  private dataSubject$ = new Subject<any>();

  // Public observable (read-only)
  data$ = this.dataSubject$.asObservable();

  // Public method to emit values
  updateData(data: any): void {
    this.dataSubject$.next(data);
  }
}

// Consumers can only subscribe, not emit
const service = new DataService();
service.data$.subscribe(data => console.log(data));
// service.data$.next(data); // Error! Not allowed
```

### 2. Complete Subjects to Prevent Memory Leaks

```typescript
import { Subject } from 'rxjs';

class MyComponent {
  private destroy$ = new Subject<void>();
  private dataSubject$ = new Subject<any>();

  ngOnInit() {
    this.dataService.getData()
      .pipe(takeUntil(this.destroy$))
      .subscribe(data => this.handleData(data));
  }

  ngOnDestroy() {
    // Complete subjects
    this.destroy$.next();
    this.destroy$.complete();
    this.dataSubject$.complete();
  }
}
```

### 3. Choose the Right Subject Type

```typescript
// Event bus - Use Subject
const eventBus$ = new Subject<Event>();

// Current state - Use BehaviorSubject
const currentUser$ = new BehaviorSubject<User | null>(null);

// Recent history - Use ReplaySubject
const recentNotifications$ = new ReplaySubject<Notification>(5);

// Single async result - Use AsyncSubject
const computationResult$ = new AsyncSubject<number>();
```

### 4. Avoid Imperative subscribe() Calls

```typescript
// Bad - Imperative
class BadComponent {
  users: User[] = [];

  constructor(private userService: UserService) {
    this.userService.getUsers().subscribe(users => {
      this.users = users;
    });
  }
}

// Good - Declarative (Angular)
class GoodComponent {
  users$ = this.userService.getUsers();

  constructor(private userService: UserService) {}
}

// Template: <div *ngFor="let user of users$ | async">
```

---

## Advanced Patterns

### State Store Pattern

```typescript
import { BehaviorSubject, Observable } from 'rxjs';
import { map } from 'rxjs/operators';

interface AppState {
  user: User | null;
  cart: CartItem[];
  loading: boolean;
}

class Store {
  private state$ = new BehaviorSubject<AppState>({
    user: null,
    cart: [],
    loading: false
  });

  // Selectors
  select<K>(selector: (state: AppState) => K): Observable<K> {
    return this.state$.pipe(
      map(selector)
    );
  }

  // Get current state
  getState(): AppState {
    return this.state$.value;
  }

  // Update state
  setState(partialState: Partial<AppState>): void {
    this.state$.next({
      ...this.state$.value,
      ...partialState
    });
  }
}

// Usage
const store = new Store();

// Select user
const user$ = store.select(state => state.user);
user$.subscribe(user => console.log('User:', user));

// Select cart count
const cartCount$ = store.select(state => state.cart.length);
cartCount$.subscribe(count => console.log('Cart items:', count));

// Update state
store.setState({ loading: true });
```

### Request Cache with ReplaySubject

```typescript
import { ReplaySubject, Observable } from 'rxjs';

class CachedDataService {
  private cache = new Map<string, ReplaySubject<any>>();

  getData(key: string, fetcher: () => Observable<any>): Observable<any> {
    if (!this.cache.has(key)) {
      const subject = new ReplaySubject(1);
      this.cache.set(key, subject);

      fetcher().subscribe(
        data => subject.next(data),
        error => subject.error(error),
        () => subject.complete()
      );
    }

    return this.cache.get(key)!.asObservable();
  }

  clearCache(key?: string): void {
    if (key) {
      this.cache.delete(key);
    } else {
      this.cache.clear();
    }
  }
}

// Usage
const service = new CachedDataService();

// First call - fetches data
service.getData('users', () => this.http.get('/api/users'))
  .subscribe(users => console.log(users));

// Second call - uses cache
service.getData('users', () => this.http.get('/api/users'))
  .subscribe(users => console.log(users)); // No HTTP request!
```

---

## Summary

| Subject Type | Key Feature | Use Case |
|-------------|-------------|----------|
| Subject | Basic multicast | Event bus, simple broadcasting |
| BehaviorSubject | Current value + initial | State management, current values |
| ReplaySubject | Buffer multiple values | History, caching, activity logs |
| AsyncSubject | Last value on complete | Single async results, promises |

## Next Topic

Continue to [TypeScript Generics](./05-typescript-generics.md) to learn about TypeScript's type system features commonly used with RxJS.
