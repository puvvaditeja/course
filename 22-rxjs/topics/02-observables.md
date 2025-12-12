# Observables

## What is an Observable?

An Observable is a lazy Push-based collection that can deliver multiple values over time. Think of it as a stream of data that you can observe and react to.

### Observable Characteristics

- **Lazy**: Observables don't execute until someone subscribes
- **Push-based**: Producer decides when to send values to consumers
- **Multiple values**: Can emit zero or more values over time
- **Asynchronous or Synchronous**: Can handle both timing models
- **Cancellable**: Subscriptions can be cancelled (unsubscribed)
- **Composable**: Can be combined and transformed with operators

### Observable vs Other Patterns

| Type | Single Value | Multiple Values |
|------|-------------|-----------------|
| **Pull** | Function | Iterator |
| **Push** | Promise | **Observable** |

```typescript
// Function (Pull, Single) - Consumer decides when to get value
const getValue = () => 42;
const value = getValue(); // Pull the value

// Promise (Push, Single) - Producer decides when to deliver value
const promise = new Promise(resolve => {
  setTimeout(() => resolve(42), 1000);
});
promise.then(value => console.log(value)); // Value is pushed

// Observable (Push, Multiple) - Producer pushes multiple values
import { Observable } from 'rxjs';

const observable$ = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  setTimeout(() => subscriber.next(4), 1000);
});

observable$.subscribe(value => console.log(value));
// Outputs: 1, 2, 3, then 4 after 1 second
```

---

## Creating Observables

### 1. Using the Observable Constructor

The most basic way to create an Observable:

```typescript
import { Observable } from 'rxjs';

const customObservable$ = new Observable<number>(subscriber => {
  // Producer logic
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);

  // Complete the Observable
  subscriber.complete();

  // Cleanup logic (optional)
  return () => {
    console.log('Cleanup: Observable unsubscribed');
  };
});

// Subscribe to the Observable
customObservable$.subscribe({
  next: value => console.log('Value:', value),
  error: err => console.error('Error:', err),
  complete: () => console.log('Complete!')
});

// Output:
// Value: 1
// Value: 2
// Value: 3
// Complete!
```

### 2. Creation Operators

RxJS provides many creation operators to simplify Observable creation:

#### of() - Emit Values in Sequence

```typescript
import { of } from 'rxjs';

// Emit a sequence of values
of(1, 2, 3, 4, 5).subscribe(value => console.log(value));
// Output: 1, 2, 3, 4, 5

// Emit objects
of(
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' }
).subscribe(user => console.log(user));

// Emit different types
of('hello', 42, true, { key: 'value' }).subscribe(console.log);
```

#### from() - Convert to Observable

```typescript
import { from } from 'rxjs';

// From an array
from([1, 2, 3, 4, 5]).subscribe(value => console.log(value));

// From a Promise
const promise = fetch('/api/users').then(r => r.json());
from(promise).subscribe(users => console.log(users));

// From an iterable
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}
from(numberGenerator()).subscribe(value => console.log(value));

// From a string (iterates characters)
from('Hello').subscribe(char => console.log(char));
// Output: H, e, l, l, o
```

#### interval() - Emit at Regular Intervals

```typescript
import { interval } from 'rxjs';
import { take } from 'rxjs/operators';

// Emit every 1000ms (1 second)
const timer$ = interval(1000).pipe(take(5));

timer$.subscribe(value => console.log(`Tick ${value}`));
// Output (every second):
// Tick 0
// Tick 1
// Tick 2
// Tick 3
// Tick 4
```

#### timer() - Emit After Delay

```typescript
import { timer } from 'rxjs';
import { take } from 'rxjs/operators';

// Emit once after 2 seconds
timer(2000).subscribe(() => console.log('2 seconds elapsed'));

// Start after 1 second, then emit every 500ms
timer(1000, 500).pipe(take(5)).subscribe(value => {
  console.log(`Value: ${value}`);
});
```

#### fromEvent() - Convert DOM Events

```typescript
import { fromEvent } from 'rxjs';

// Mouse clicks
const button = document.getElementById('myButton');
const clicks$ = fromEvent(button, 'click');

clicks$.subscribe(event => {
  console.log('Button clicked!', event);
});

// Mouse movements
const moves$ = fromEvent(document, 'mousemove');
moves$.subscribe(event => {
  console.log(`Mouse position: ${event.clientX}, ${event.clientY}`);
});

// Keyboard events
const keypress$ = fromEvent(document, 'keypress');
keypress$.subscribe(event => {
  console.log(`Key pressed: ${event.key}`);
});
```

#### range() - Emit Range of Numbers

```typescript
import { range } from 'rxjs';

// Emit numbers from 1 to 10
range(1, 10).subscribe(value => console.log(value));
// Output: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10

// Useful for iterations
range(0, 5).subscribe(i => {
  console.log(`Item ${i}`);
});
```

#### EMPTY and NEVER

```typescript
import { EMPTY, NEVER } from 'rxjs';

// EMPTY - Completes immediately without emitting
EMPTY.subscribe({
  next: () => console.log('This will not execute'),
  complete: () => console.log('Completed immediately')
});

// NEVER - Never emits, never completes
NEVER.subscribe({
  next: () => console.log('This will never execute'),
  complete: () => console.log('This will never complete')
});
```

---

## Subscribing to Observables

### The subscribe() Method

An Observable doesn't do anything until someone subscribes to it.

#### Full Observer Object

```typescript
import { of } from 'rxjs';

const observable$ = of(1, 2, 3);

observable$.subscribe({
  next: value => {
    console.log('Received value:', value);
  },
  error: error => {
    console.error('Error occurred:', error);
  },
  complete: () => {
    console.log('Observable completed');
  }
});
```

#### Shorthand Subscriptions

```typescript
import { of } from 'rxjs';

const observable$ = of(1, 2, 3);

// Just the next handler
observable$.subscribe(value => console.log(value));

// Next and error handlers
observable$.subscribe(
  value => console.log(value),
  error => console.error(error)
);

// Next, error, and complete handlers
observable$.subscribe(
  value => console.log(value),
  error => console.error(error),
  () => console.log('Complete')
);
```

### Subscription Object

The subscribe method returns a Subscription object that can be used to unsubscribe:

```typescript
import { interval } from 'rxjs';

const subscription = interval(1000).subscribe(value => {
  console.log(`Tick ${value}`);
});

// Unsubscribe after 5 seconds
setTimeout(() => {
  subscription.unsubscribe();
  console.log('Unsubscribed!');
}, 5000);
```

### Multiple Subscriptions

```typescript
import { interval } from 'rxjs';

const observable$ = interval(1000);

// First subscription
const sub1 = observable$.subscribe(value => {
  console.log(`Subscriber 1: ${value}`);
});

// Second subscription (starts its own execution)
setTimeout(() => {
  const sub2 = observable$.subscribe(value => {
    console.log(`Subscriber 2: ${value}`);
  });
}, 2500);

// Each subscription gets its own independent execution
```

### Managing Multiple Subscriptions

```typescript
import { Subscription, interval } from 'rxjs';

const parentSubscription = new Subscription();

const sub1 = interval(1000).subscribe(val => console.log(`Sub1: ${val}`));
const sub2 = interval(1500).subscribe(val => console.log(`Sub2: ${val}`));
const sub3 = interval(2000).subscribe(val => console.log(`Sub3: ${val}`));

// Add child subscriptions
parentSubscription.add(sub1);
parentSubscription.add(sub2);
parentSubscription.add(sub3);

// Unsubscribe all at once
setTimeout(() => {
  parentSubscription.unsubscribe();
  console.log('All subscriptions cancelled');
}, 10000);
```

---

## Observable Lifecycle

An Observable goes through a specific lifecycle from creation to completion.

### Lifecycle Phases

```typescript
import { Observable } from 'rxjs';

const lifecycle$ = new Observable<number>(subscriber => {
  console.log('1. Observable created (lazy - only on subscribe)');

  // Emission phase
  subscriber.next(1);
  console.log('2. Emitted value 1');

  subscriber.next(2);
  console.log('3. Emitted value 2');

  subscriber.next(3);
  console.log('4. Emitted value 3');

  // Completion phase
  subscriber.complete();
  console.log('5. Observable completed');

  // This will not be emitted (after complete)
  subscriber.next(4);

  // Cleanup function
  return () => {
    console.log('6. Cleanup executed');
  };
});

console.log('Before subscription');

const subscription = lifecycle$.subscribe({
  next: value => console.log(`   Received: ${value}`),
  error: err => console.error(`   Error: ${err}`),
  complete: () => console.log('   Complete notification received')
});

console.log('After subscription');

// Trigger cleanup
subscription.unsubscribe();
```

### Error Handling in Lifecycle

```typescript
import { Observable } from 'rxjs';

const errorObservable$ = new Observable<number>(subscriber => {
  subscriber.next(1);
  subscriber.next(2);

  // Error terminates the Observable
  subscriber.error(new Error('Something went wrong!'));

  // These will not execute (after error)
  subscriber.next(3);
  subscriber.complete();

  return () => {
    console.log('Cleanup after error');
  };
});

errorObservable$.subscribe({
  next: value => console.log(`Value: ${value}`),
  error: error => console.error(`Error caught: ${error.message}`),
  complete: () => console.log('This will not execute after error')
});
```

### Completion and Cleanup

```typescript
import { Observable } from 'rxjs';

const withCleanup$ = new Observable<number>(subscriber => {
  let count = 0;

  // Set up an interval
  const intervalId = setInterval(() => {
    subscriber.next(count++);

    if (count === 5) {
      subscriber.complete();
    }
  }, 1000);

  // Cleanup function - called on unsubscribe or complete
  return () => {
    clearInterval(intervalId);
    console.log('Interval cleared');
  };
});

const subscription = withCleanup$.subscribe({
  next: value => console.log(`Value: ${value}`),
  complete: () => console.log('Completed')
});

// Or manually unsubscribe before completion
// setTimeout(() => subscription.unsubscribe(), 2500);
```

---

## Hot vs Cold Observables

Understanding the difference between hot and cold Observables is crucial for managing data flow.

### Cold Observables

A **cold Observable** creates a new producer for each subscriber. The data producer is created inside the Observable.

```typescript
import { Observable } from 'rxjs';

// Cold Observable - Each subscriber gets its own execution
const cold$ = new Observable<number>(subscriber => {
  console.log('Producer started');

  const random = Math.random();
  subscriber.next(random);
  subscriber.next(random * 2);
  subscriber.complete();
});

console.log('Subscriber 1:');
cold$.subscribe(value => console.log(`  Value: ${value}`));

console.log('Subscriber 2:');
cold$.subscribe(value => console.log(`  Value: ${value}`));

// Output shows each subscriber gets different random numbers
// Producer started
// Subscriber 1:
//   Value: 0.123...
//   Value: 0.246...
// Producer started
// Subscriber 2:
//   Value: 0.789...
//   Value: 1.578...
```

### Cold Observable Examples

```typescript
import { ajax } from 'rxjs/ajax';
import { interval } from 'rxjs';

// HTTP requests are cold
const users$ = ajax.getJSON('/api/users');

// Each subscription makes a new HTTP request
users$.subscribe(users => console.log('Sub 1:', users));
users$.subscribe(users => console.log('Sub 2:', users));
// Two separate HTTP requests!

// interval is cold
const timer$ = interval(1000);

// Each subscriber gets its own timer
timer$.subscribe(val => console.log('Timer 1:', val));
setTimeout(() => {
  timer$.subscribe(val => console.log('Timer 2:', val));
}, 2500);
// Timer 2 starts from 0, not from where Timer 1 is
```

### Hot Observables

A **hot Observable** shares a single producer among all subscribers. The producer exists outside the Observable.

```typescript
import { fromEvent } from 'rxjs';

// Hot Observable - All subscribers share the same source
const button = document.getElementById('myButton');
const clicks$ = fromEvent(button, 'click');

// All subscribers get the same click events
clicks$.subscribe(() => console.log('Subscriber 1: Clicked'));
clicks$.subscribe(() => console.log('Subscriber 2: Clicked'));

// When button is clicked, both subscribers are notified
```

### Converting Cold to Hot

Use operators to convert cold Observables to hot:

#### Using share()

```typescript
import { interval } from 'rxjs';
import { share, take } from 'rxjs/operators';

// Cold Observable
const cold$ = interval(1000).pipe(take(5));

// Convert to hot using share()
const hot$ = cold$.pipe(share());

// First subscriber
hot$.subscribe(value => console.log(`Sub 1: ${value}`));

// Second subscriber (after 2.5 seconds)
setTimeout(() => {
  hot$.subscribe(value => console.log(`Sub 2: ${value}`));
}, 2500);

// Both subscribers share the same execution
// Sub 2 joins in-progress and misses earlier values
```

#### Using shareReplay()

```typescript
import { interval } from 'rxjs';
import { shareReplay, take } from 'rxjs/operators';

const cold$ = interval(1000).pipe(take(5));

// Share and replay last 2 values to new subscribers
const hot$ = cold$.pipe(shareReplay(2));

hot$.subscribe(value => console.log(`Sub 1: ${value}`));

// New subscriber gets last 2 values immediately
setTimeout(() => {
  hot$.subscribe(value => console.log(`Sub 2: ${value}`));
}, 3500);

// Sub 2 receives values 1 and 2 immediately (replayed)
// Then continues with values 3 and 4
```

### Comparison Table

| Aspect | Cold Observable | Hot Observable |
|--------|----------------|----------------|
| Producer | Created per subscription | Shared among subscriptions |
| Execution | Starts when subscribed | Already running |
| Data | Unicast (1-to-1) | Multicast (1-to-many) |
| Examples | HTTP calls, timers, ranges | DOM events, WebSockets |
| Late subscribers | Get all values from start | Miss earlier values |
| Use case | Independent data streams | Shared event sources |

### Practical Example: API Call

```typescript
import { ajax } from 'rxjs/ajax';
import { shareReplay, tap } from 'rxjs/operators';

// Without sharing (cold) - Multiple API calls
const users$ = ajax.getJSON<User[]>('/api/users');

users$.subscribe(users => console.log('Component 1:', users.length));
users$.subscribe(users => console.log('Component 2:', users.length));
// Makes 2 separate HTTP requests!

// With sharing (hot) - Single API call
const sharedUsers$ = ajax.getJSON<User[]>('/api/users').pipe(
  tap(() => console.log('API call made')),
  shareReplay(1) // Cache and replay the result
);

sharedUsers$.subscribe(users => console.log('Component 1:', users.length));
sharedUsers$.subscribe(users => console.log('Component 2:', users.length));
// Makes only 1 HTTP request, result is shared
```

---

## Observable Patterns

### 1. Unsubscribe Pattern

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Subscription } from 'rxjs';

export class MyComponent implements OnInit, OnDestroy {
  private subscription: Subscription;

  ngOnInit() {
    this.subscription = this.dataService.getData().subscribe(
      data => this.handleData(data)
    );
  }

  ngOnDestroy() {
    if (this.subscription) {
      this.subscription.unsubscribe();
    }
  }
}
```

### 2. TakeUntil Pattern

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Subject } from 'rxjs';
import { takeUntil } from 'rxjs/operators';

export class MyComponent implements OnInit, OnDestroy {
  private destroy$ = new Subject<void>();

  ngOnInit() {
    this.dataService.getData().pipe(
      takeUntil(this.destroy$)
    ).subscribe(data => this.handleData(data));

    this.anotherService.getOtherData().pipe(
      takeUntil(this.destroy$)
    ).subscribe(data => this.handleOtherData(data));
  }

  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

### 3. Async Pipe Pattern (Angular)

```typescript
import { Component } from '@angular/core';
import { Observable } from 'rxjs';

@Component({
  selector: 'app-users',
  template: `
    <div *ngIf="users$ | async as users">
      <div *ngFor="let user of users">
        {{ user.name }}
      </div>
    </div>
  `
})
export class UsersComponent {
  users$: Observable<User[]>;

  constructor(private userService: UserService) {
    this.users$ = this.userService.getUsers();
  }

  // No need for ngOnDestroy - async pipe handles unsubscription
}
```

### 4. Error Recovery Pattern

```typescript
import { of } from 'rxjs';
import { catchError, retry } from 'rxjs/operators';

getData().pipe(
  retry(3), // Retry up to 3 times
  catchError(error => {
    console.error('Error after retries:', error);
    return of([]); // Return fallback value
  })
).subscribe(data => console.log(data));
```

---

## Summary

| Concept | Description |
|---------|-------------|
| Observable | Lazy, push-based collection of multiple values over time |
| Creation | Constructor, of(), from(), interval(), fromEvent(), etc. |
| Subscription | Execute Observable and receive values via subscribe() |
| Lifecycle | Creation -> Emission -> Completion/Error -> Cleanup |
| Cold Observable | New producer per subscriber (HTTP, timers) |
| Hot Observable | Shared producer (DOM events, WebSockets) |
| Unsubscription | Critical for preventing memory leaks |

## Next Topic

Continue to [Operators](./03-operators.md) to learn about transforming and combining Observables.
