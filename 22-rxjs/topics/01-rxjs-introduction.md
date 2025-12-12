# Introduction to RxJS

## What is RxJS?

RxJS (Reactive Extensions for JavaScript) is a powerful library for reactive programming using Observables. It makes it easier to compose asynchronous or callback-based code by providing a comprehensive set of operators for working with data streams.

### Key Characteristics

- **Reactive Programming**: Programming with asynchronous data streams
- **Functional**: Uses pure functions and immutable data
- **Composable**: Operators can be chained together to transform data
- **Powerful**: Handles complex asynchronous scenarios elegantly
- **Event-Driven**: Perfect for handling user interactions and real-time data
- **Framework Agnostic**: Works with any JavaScript framework or vanilla JS

### Version Information

RxJS is currently at version 7.x (as of 2024), with significant improvements in:
- Tree-shaking capabilities
- TypeScript support
- Performance optimizations
- Better error handling

---

## What is Reactive Programming?

Reactive programming is a declarative programming paradigm concerned with data streams and the propagation of change. It's about thinking in terms of streams of values over time.

### Traditional vs Reactive Programming

**Traditional Imperative Approach:**
```typescript
// Traditional click handler
let count = 0;
const button = document.getElementById('myButton');

button.addEventListener('click', () => {
  count++;
  console.log(`Clicked ${count} times`);
});
```

**Reactive Approach:**
```typescript
import { fromEvent } from 'rxjs';
import { scan, map } from 'rxjs/operators';

// Reactive click stream
const button = document.getElementById('myButton');
const clicks$ = fromEvent(button, 'click');

clicks$.pipe(
  scan(count => count + 1, 0),
  map(count => `Clicked ${count} times`)
).subscribe(message => console.log(message));
```

### Everything is a Stream

In reactive programming, you think of everything as a stream:

```typescript
import { fromEvent, interval, of } from 'rxjs';

// Mouse movements as a stream
const mouseMoves$ = fromEvent(document, 'mousemove');

// Time ticks as a stream
const timer$ = interval(1000);

// Array as a stream
const numbers$ = of(1, 2, 3, 4, 5);

// HTTP requests as a stream
const data$ = from(fetch('/api/users').then(r => r.json()));
```

### Core Principles

1. **Observable Streams**: Data flows through observable streams
2. **Operators**: Transform, filter, and combine streams
3. **Subscribers**: React to emitted values
4. **Declarative**: Describe what you want, not how to get it
5. **Asynchronous**: Handle async operations naturally

---

## Why Use RxJS?

RxJS provides elegant solutions to common programming challenges, especially in modern web applications.

### 1. Handling Complex Async Operations

**Problem: Nested Callbacks (Callback Hell)**
```typescript
// Without RxJS - Callback hell
getData(function(a) {
  getMoreData(a, function(b) {
    getMoreData(b, function(c) {
      getMoreData(c, function(d) {
        console.log(d);
      });
    });
  });
});
```

**Solution: Observable Chain**
```typescript
import { from } from 'rxjs';
import { switchMap, map } from 'rxjs/operators';

// With RxJS - Clean and readable
from(getData())
  .pipe(
    switchMap(a => getMoreData(a)),
    switchMap(b => getMoreData(b)),
    switchMap(c => getMoreData(c))
  )
  .subscribe(d => console.log(d));
```

### 2. Event Handling and User Interactions

**Search with Debounce:**
```typescript
import { fromEvent } from 'rxjs';
import { debounceTime, distinctUntilChanged, switchMap } from 'rxjs/operators';

const searchBox = document.getElementById('search');
const search$ = fromEvent(searchBox, 'input');

search$.pipe(
  debounceTime(300),                    // Wait 300ms after typing stops
  map(event => event.target.value),     // Extract the input value
  distinctUntilChanged(),               // Ignore if same as previous
  switchMap(term => searchAPI(term))    // Make API call
).subscribe(results => displayResults(results));
```

### 3. Managing Multiple Data Sources

**Combining Multiple API Calls:**
```typescript
import { forkJoin, combineLatest, zip } from 'rxjs';

// Wait for all to complete
forkJoin({
  user: getUser(userId),
  posts: getUserPosts(userId),
  comments: getUserComments(userId)
}).subscribe(({ user, posts, comments }) => {
  console.log('All data loaded:', user, posts, comments);
});

// Emit whenever any source emits
combineLatest([
  currentUser$,
  settings$,
  preferences$
]).subscribe(([user, settings, prefs]) => {
  updateUI(user, settings, prefs);
});
```

### 4. Cancellation and Error Handling

**Automatic Request Cancellation:**
```typescript
import { fromEvent } from 'rxjs';
import { switchMap, catchError } from 'rxjs/operators';
import { of } from 'rxjs';

const search$ = fromEvent(searchInput, 'input');

search$.pipe(
  debounceTime(300),
  switchMap(event =>
    searchAPI(event.target.value).pipe(
      catchError(error => {
        console.error('Search failed:', error);
        return of([]); // Return empty results on error
      })
    )
  )
).subscribe(results => displayResults(results));

// switchMap automatically cancels previous requests
// when new input arrives
```

### 5. Real-time Data Streams

**WebSocket Data Stream:**
```typescript
import { webSocket } from 'rxjs/webSocket';
import { retry, catchError } from 'rxjs/operators';

const socket$ = webSocket('ws://localhost:8080');

socket$.pipe(
  retry(3), // Retry connection 3 times
  catchError(error => {
    console.error('WebSocket error:', error);
    return of({ type: 'error', message: error.message });
  })
).subscribe(
  message => handleMessage(message),
  error => console.error(error),
  () => console.log('Connection closed')
);

// Send messages
socket$.next({ type: 'subscribe', channel: 'updates' });
```

---

## RxJS Use Cases

### Frontend Development

1. **Form Validation**
```typescript
import { fromEvent, combineLatest } from 'rxjs';
import { map, startWith } from 'rxjs/operators';

const email$ = fromEvent(emailInput, 'input').pipe(
  map(e => e.target.value),
  map(email => isValidEmail(email)),
  startWith(false)
);

const password$ = fromEvent(passwordInput, 'input').pipe(
  map(e => e.target.value),
  map(pwd => pwd.length >= 8),
  startWith(false)
);

// Enable submit button when both are valid
combineLatest([email$, password$]).pipe(
  map(([emailValid, pwdValid]) => emailValid && pwdValid)
).subscribe(isValid => {
  submitButton.disabled = !isValid;
});
```

2. **Auto-save Feature**
```typescript
import { fromEvent } from 'rxjs';
import { debounceTime, switchMap, tap } from 'rxjs/operators';

const content$ = fromEvent(editor, 'input').pipe(
  map(e => e.target.value),
  debounceTime(2000), // Wait 2 seconds after typing stops
  tap(() => showSavingIndicator()),
  switchMap(content => saveToServer(content)),
  tap(() => hideSavingIndicator())
);

content$.subscribe(
  response => console.log('Saved successfully'),
  error => console.error('Save failed:', error)
);
```

### Angular Integration

RxJS is deeply integrated into Angular:

```typescript
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { map, catchError } from 'rxjs/operators';

@Component({
  selector: 'app-users',
  template: `
    <div *ngIf="users$ | async as users">
      <div *ngFor="let user of users">{{ user.name }}</div>
    </div>
  `
})
export class UsersComponent implements OnInit {
  users$: Observable<User[]>;

  constructor(private http: HttpClient) {}

  ngOnInit() {
    this.users$ = this.http.get<User[]>('/api/users').pipe(
      map(users => users.filter(u => u.active)),
      catchError(error => {
        console.error('Failed to load users:', error);
        return of([]);
      })
    );
  }
}
```

### State Management

```typescript
import { BehaviorSubject } from 'rxjs';
import { map } from 'rxjs/operators';

interface AppState {
  user: User | null;
  loading: boolean;
  error: string | null;
}

class StateManager {
  private state$ = new BehaviorSubject<AppState>({
    user: null,
    loading: false,
    error: null
  });

  // Selectors
  user$ = this.state$.pipe(map(state => state.user));
  loading$ = this.state$.pipe(map(state => state.loading));
  error$ = this.state$.pipe(map(state => state.error));

  // Actions
  setUser(user: User) {
    this.state$.next({ ...this.state$.value, user });
  }

  setLoading(loading: boolean) {
    this.state$.next({ ...this.state$.value, loading });
  }

  setError(error: string) {
    this.state$.next({ ...this.state$.value, error });
  }
}
```

---

## RxJS vs Alternatives

### RxJS vs Promises

| Feature | Promises | RxJS Observables |
|---------|----------|------------------|
| Values | Single value | Multiple values over time |
| Cancellable | No | Yes |
| Lazy | No (eager) | Yes (lazy evaluation) |
| Operators | Limited (then, catch) | Rich set (100+ operators) |
| Error Handling | catch() | catchError(), retry(), etc. |
| Use Case | Single async operation | Streams, events, complex flows |

**Example Comparison:**
```typescript
// Promise - Single value
const promise = fetch('/api/user/1')
  .then(response => response.json())
  .then(user => console.log(user));

// Observable - Can emit multiple values
const observable$ = interval(1000).pipe(
  take(5),
  map(i => `Tick ${i}`)
);
observable$.subscribe(value => console.log(value));
// Outputs: Tick 0, Tick 1, Tick 2, Tick 3, Tick 4
```

### RxJS vs Async/Await

```typescript
// Async/Await - Sequential
async function loadData() {
  const user = await fetchUser();
  const posts = await fetchPosts(user.id);
  const comments = await fetchComments(posts);
  return { user, posts, comments };
}

// RxJS - Parallel and composable
const data$ = fetchUser().pipe(
  switchMap(user => forkJoin({
    user: of(user),
    posts: fetchPosts(user.id),
    comments: fetchComments(user.id)
  }))
);
```

### RxJS vs Event Listeners

```typescript
// Traditional Event Listener
let clicks = 0;
button.addEventListener('click', () => {
  clicks++;
  if (clicks >= 3) {
    console.log('Clicked 3 times');
    // How do we remove the listener?
  }
});

// RxJS - Declarative and self-cleaning
fromEvent(button, 'click').pipe(
  scan(count => count + 1, 0),
  filter(count => count >= 3),
  take(1) // Automatically unsubscribes after first emission
).subscribe(() => console.log('Clicked 3 times'));
```

---

## Getting Started with RxJS

### Installation

```bash
# Using npm
npm install rxjs

# Using yarn
yarn add rxjs

# Using pnpm
pnpm add rxjs
```

### Basic Imports

```typescript
// Importing creation functions
import { of, from, interval, fromEvent } from 'rxjs';

// Importing operators
import { map, filter, debounceTime, switchMap } from 'rxjs/operators';

// Complete example
import { of } from 'rxjs';
import { map, filter } from 'rxjs/operators';

const numbers$ = of(1, 2, 3, 4, 5);

numbers$.pipe(
  filter(n => n % 2 === 0),
  map(n => n * 10)
).subscribe(result => console.log(result));
// Output: 20, 40
```

### Project Structure

```typescript
// services/data.service.ts
import { Observable } from 'rxjs';
import { ajax } from 'rxjs/ajax';
import { map, catchError } from 'rxjs/operators';

export class DataService {
  getUsers(): Observable<User[]> {
    return ajax.getJSON<User[]>('/api/users').pipe(
      catchError(error => {
        console.error('Error loading users:', error);
        return of([]);
      })
    );
  }
}
```

---

## Key Concepts Preview

### 1. Observable
A lazy Push collection of multiple values over time.

```typescript
import { Observable } from 'rxjs';

const observable$ = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.complete();
});
```

### 2. Observer
A consumer of values delivered by an Observable.

```typescript
const observer = {
  next: (value) => console.log('Next:', value),
  error: (error) => console.error('Error:', error),
  complete: () => console.log('Complete!')
};

observable$.subscribe(observer);
```

### 3. Operators
Functions that enable sophisticated manipulation of collections.

```typescript
import { of } from 'rxjs';
import { map, filter, reduce } from 'rxjs/operators';

of(1, 2, 3, 4, 5).pipe(
  filter(x => x % 2 === 0),
  map(x => x * 10),
  reduce((acc, x) => acc + x, 0)
).subscribe(result => console.log(result)); // 60
```

### 4. Subscription
Represents the execution of an Observable.

```typescript
const subscription = observable$.subscribe(value => console.log(value));

// Later: clean up
subscription.unsubscribe();
```

---

## Best Practices

### 1. Naming Convention
Use `$` suffix for Observable variables:
```typescript
const user$ = getUser();
const clicks$ = fromEvent(button, 'click');
const data$ = this.http.get('/api/data');
```

### 2. Always Unsubscribe
Prevent memory leaks by unsubscribing:
```typescript
// In Angular
export class MyComponent implements OnInit, OnDestroy {
  private subscription: Subscription;

  ngOnInit() {
    this.subscription = this.data$.subscribe(data => {
      // Handle data
    });
  }

  ngOnDestroy() {
    this.subscription.unsubscribe();
  }
}

// Or use takeUntil pattern
private destroy$ = new Subject<void>();

ngOnInit() {
  this.data$.pipe(
    takeUntil(this.destroy$)
  ).subscribe(data => {
    // Handle data
  });
}

ngOnDestroy() {
  this.destroy$.next();
  this.destroy$.complete();
}
```

### 3. Error Handling
Always handle errors to prevent stream termination:
```typescript
observable$.pipe(
  catchError(error => {
    console.error('Error occurred:', error);
    return of(defaultValue); // Return fallback value
  })
).subscribe(value => console.log(value));
```

### 4. Use Pipeable Operators
Modern RxJS uses pipeable operators:
```typescript
// Good - Pipeable operators
source$.pipe(
  map(x => x * 2),
  filter(x => x > 10)
).subscribe();

// Avoid - Old chaining style (deprecated)
source$
  .map(x => x * 2)
  .filter(x => x > 10)
  .subscribe();
```

---

## Common Pitfalls

### 1. Not Unsubscribing
```typescript
// Bad - Memory leak
ngOnInit() {
  this.http.get('/api/data').subscribe(data => {
    this.data = data;
  });
}

// Good - Proper cleanup
private destroy$ = new Subject<void>();

ngOnInit() {
  this.http.get('/api/data').pipe(
    takeUntil(this.destroy$)
  ).subscribe(data => {
    this.data = data;
  });
}

ngOnDestroy() {
  this.destroy$.next();
  this.destroy$.complete();
}
```

### 2. Subscribing Multiple Times
```typescript
// Bad - Multiple subscriptions create multiple HTTP calls
const users$ = this.http.get('/api/users');
users$.subscribe(users => this.users = users);
users$.subscribe(users => this.count = users.length);

// Good - Share the subscription
const users$ = this.http.get('/api/users').pipe(share());
users$.subscribe(users => this.users = users);
users$.subscribe(users => this.count = users.length);
```

### 3. Nested Subscriptions
```typescript
// Bad - Nested subscriptions
this.getUser().subscribe(user => {
  this.getPosts(user.id).subscribe(posts => {
    this.getComments(posts[0].id).subscribe(comments => {
      // Nested callback hell
    });
  });
});

// Good - Use higher-order operators
this.getUser().pipe(
  switchMap(user => this.getPosts(user.id)),
  switchMap(posts => this.getComments(posts[0].id))
).subscribe(comments => {
  // Clean and readable
});
```

---

## Summary

| Concept | Description |
|---------|-------------|
| RxJS | Library for reactive programming with Observables |
| Reactive Programming | Programming with asynchronous data streams |
| Why RxJS | Elegant async handling, powerful operators, composability |
| Use Cases | Events, API calls, real-time data, state management |
| Best Practices | Use $ suffix, unsubscribe, handle errors, use pipe() |

## Next Topic

Continue to [Observables](./02-observables.md) to learn about creating and working with Observables in depth.
