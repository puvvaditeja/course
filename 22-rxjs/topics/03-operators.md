# RxJS Operators

## What are Operators?

Operators are functions that enable sophisticated manipulation of Observable data streams. They are the essential tools for transforming, filtering, combining, and managing Observables in a functional, declarative way.

### Key Characteristics

- **Pure Functions**: Don't modify the source Observable
- **Return New Observables**: Enable chaining
- **Composable**: Can be combined in a pipeline
- **Lazy**: Only execute when subscribed
- **Type-Safe**: Full TypeScript support

### Using the pipe() Method

Modern RxJS uses the `pipe()` method to chain operators:

```typescript
import { of } from 'rxjs';
import { map, filter, tap } from 'rxjs/operators';

const source$ = of(1, 2, 3, 4, 5);

const result$ = source$.pipe(
  filter(x => x % 2 === 0),    // Keep only even numbers
  map(x => x * 10),             // Multiply by 10
  tap(x => console.log(x))      // Side effect: log value
);

result$.subscribe(value => console.log('Final:', value));
// Output:
// 20
// Final: 20
// 40
// Final: 40
```

---

## Transformation Operators

### map()

Transforms each emitted value using a projection function.

```typescript
import { of } from 'rxjs';
import { map } from 'rxjs/operators';

// Simple transformation
of(1, 2, 3, 4, 5).pipe(
  map(x => x * 2)
).subscribe(result => console.log(result));
// Output: 2, 4, 6, 8, 10

// Object transformation
interface User {
  id: number;
  firstName: string;
  lastName: string;
}

of(
  { id: 1, firstName: 'John', lastName: 'Doe' },
  { id: 2, firstName: 'Jane', lastName: 'Smith' }
).pipe(
  map(user => ({
    id: user.id,
    fullName: `${user.firstName} ${user.lastName}`
  }))
).subscribe(user => console.log(user));
// Output:
// { id: 1, fullName: 'John Doe' }
// { id: 2, fullName: 'Jane Smith' }

// With index
of('a', 'b', 'c').pipe(
  map((value, index) => `${index}: ${value}`)
).subscribe(console.log);
// Output: 0: a, 1: b, 2: c
```

### pluck()

Extract a nested property from each emitted object.

```typescript
import { of } from 'rxjs';
import { pluck } from 'rxjs/operators';

interface User {
  id: number;
  name: string;
  address: {
    city: string;
    country: string;
  };
}

const users$ = of(
  { id: 1, name: 'John', address: { city: 'New York', country: 'USA' } },
  { id: 2, name: 'Jane', address: { city: 'London', country: 'UK' } }
);

// Extract name
users$.pipe(
  pluck('name')
).subscribe(name => console.log(name));
// Output: John, Jane

// Extract nested property
users$.pipe(
  pluck('address', 'city')
).subscribe(city => console.log(city));
// Output: New York, London
```

### mapTo()

Maps every emission to a constant value.

```typescript
import { fromEvent } from 'rxjs';
import { mapTo, scan } from 'rxjs/operators';

const button = document.getElementById('myButton');

// Count clicks
fromEvent(button, 'click').pipe(
  mapTo(1),
  scan((acc, value) => acc + value, 0)
).subscribe(count => {
  console.log(`Clicked ${count} times`);
});
```

---

## Filtering Operators

### filter()

Emit only values that pass a predicate function.

```typescript
import { of } from 'rxjs';
import { filter } from 'rxjs/operators';

// Filter numbers
of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10).pipe(
  filter(x => x % 2 === 0)
).subscribe(even => console.log(even));
// Output: 2, 4, 6, 8, 10

// Filter objects
interface Product {
  id: number;
  name: string;
  price: number;
  inStock: boolean;
}

const products$ = of(
  { id: 1, name: 'Laptop', price: 999, inStock: true },
  { id: 2, name: 'Mouse', price: 25, inStock: false },
  { id: 3, name: 'Keyboard', price: 75, inStock: true }
);

products$.pipe(
  filter(product => product.inStock && product.price < 100)
).subscribe(product => console.log(product.name));
// Output: Keyboard

// With index
of(10, 20, 30, 40, 50).pipe(
  filter((value, index) => index % 2 === 0)
).subscribe(console.log);
// Output: 10, 30, 50
```

### take()

Emit only the first n values.

```typescript
import { interval } from 'rxjs';
import { take } from 'rxjs/operators';

// Take first 5 values
interval(1000).pipe(
  take(5)
).subscribe(
  value => console.log(value),
  err => console.error(err),
  () => console.log('Complete!')
);
// Output: 0, 1, 2, 3, 4, Complete!
```

### takeWhile()

Emit values while a condition is true.

```typescript
import { of } from 'rxjs';
import { takeWhile } from 'rxjs/operators';

of(1, 2, 3, 4, 5, 6, 7, 8, 9).pipe(
  takeWhile(x => x < 5)
).subscribe(console.log);
// Output: 1, 2, 3, 4

// Include the last value that failed the predicate
of(1, 2, 3, 4, 5, 6, 7, 8, 9).pipe(
  takeWhile(x => x < 5, true)
).subscribe(console.log);
// Output: 1, 2, 3, 4, 5
```

### takeUntil()

Emit values until another Observable emits.

```typescript
import { interval, timer } from 'rxjs';
import { takeUntil } from 'rxjs/operators';

// Stop after 5 seconds
const source$ = interval(1000);
const notifier$ = timer(5000);

source$.pipe(
  takeUntil(notifier$)
).subscribe(
  value => console.log(value),
  err => console.error(err),
  () => console.log('Stopped!')
);
// Output: 0, 1, 2, 3, Stopped!
```

### skip()

Skip the first n values.

```typescript
import { of } from 'rxjs';
import { skip } from 'rxjs/operators';

of(1, 2, 3, 4, 5).pipe(
  skip(2)
).subscribe(console.log);
// Output: 3, 4, 5
```

### distinct()

Emit only unique values.

```typescript
import { of } from 'rxjs';
import { distinct } from 'rxjs/operators';

of(1, 2, 2, 3, 1, 4, 5, 3).pipe(
  distinct()
).subscribe(console.log);
// Output: 1, 2, 3, 4, 5

// Distinct by property
interface Person {
  id: number;
  name: string;
}

of(
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' },
  { id: 1, name: 'John Doe' }
).pipe(
  distinct(person => person.id)
).subscribe(console.log);
// Output: {id: 1, name: 'John'}, {id: 2, name: 'Jane'}
```

### distinctUntilChanged()

Emit only when the value changes from the previous one.

```typescript
import { of } from 'rxjs';
import { distinctUntilChanged } from 'rxjs/operators';

of(1, 1, 2, 2, 3, 3, 3, 4, 5, 5).pipe(
  distinctUntilChanged()
).subscribe(console.log);
// Output: 1, 2, 3, 4, 5
```

### debounceTime()

Emit a value after a specified time has passed without another emission.

```typescript
import { fromEvent } from 'rxjs';
import { debounceTime, map } from 'rxjs/operators';

// Search input with debounce
const searchInput = document.getElementById('search') as HTMLInputElement;

fromEvent(searchInput, 'input').pipe(
  map(event => (event.target as HTMLInputElement).value),
  debounceTime(300) // Wait 300ms after user stops typing
).subscribe(searchTerm => {
  console.log('Search for:', searchTerm);
  // Make API call here
});
```

### throttleTime()

Emit a value, then ignore subsequent values for a specified duration.

```typescript
import { fromEvent } from 'rxjs';
import { throttleTime } from 'rxjs/operators';

// Prevent button spam
const button = document.getElementById('saveButton');

fromEvent(button, 'click').pipe(
  throttleTime(2000) // Ignore clicks for 2 seconds after a click
).subscribe(() => {
  console.log('Save action triggered');
  // Perform save operation
});
```

---

## Higher-Order Operators

These operators work with Observables that emit other Observables.

### switchMap()

Switch to a new inner Observable, canceling the previous one.

```typescript
import { fromEvent, of } from 'rxjs';
import { switchMap, ajax } from 'rxjs';
import { debounceTime, distinctUntilChanged } from 'rxjs/operators';

// Autocomplete search
const searchInput = document.getElementById('search') as HTMLInputElement;

fromEvent(searchInput, 'input').pipe(
  debounceTime(300),
  map(event => (event.target as HTMLInputElement).value),
  distinctUntilChanged(),
  switchMap(searchTerm => {
    // Cancel previous search if new one starts
    return ajax.getJSON(`/api/search?q=${searchTerm}`);
  })
).subscribe(results => {
  console.log('Search results:', results);
});
```

**Use switchMap when:**
- You want to cancel previous inner Observables
- Only the latest result matters (searches, autocomplete)
- User interactions that can be cancelled

### mergeMap() / flatMap()

Merge all inner Observables as they complete.

```typescript
import { of, delay } from 'rxjs';
import { mergeMap } from 'rxjs/operators';

// Process multiple items in parallel
const processItem = (item: number) => {
  return of(`Processed ${item}`).pipe(delay(1000));
};

of(1, 2, 3).pipe(
  mergeMap(item => processItem(item))
).subscribe(result => console.log(result));
// All items process in parallel
// Output (after ~1 second): Processed 1, Processed 2, Processed 3
```

**Use mergeMap when:**
- You want to process items in parallel
- Order doesn't matter
- All inner Observables should complete

```typescript
// Save multiple items
const items = [1, 2, 3, 4, 5];

from(items).pipe(
  mergeMap(item => saveToServer(item), 3) // Max 3 concurrent requests
).subscribe(
  result => console.log('Saved:', result),
  error => console.error('Error:', error),
  () => console.log('All items saved')
);
```

### concatMap()

Concatenate inner Observables in order.

```typescript
import { of, delay } from 'rxjs';
import { concatMap } from 'rxjs/operators';

// Process items sequentially
const processItem = (item: number) => {
  return of(`Processed ${item}`).pipe(delay(1000));
};

of(1, 2, 3).pipe(
  concatMap(item => processItem(item))
).subscribe(result => console.log(result));
// Items process one after another
// Output: Processed 1 (after 1s), Processed 2 (after 2s), Processed 3 (after 3s)
```

**Use concatMap when:**
- Order matters
- You need sequential processing
- Each operation must complete before the next starts

```typescript
// Sequential API calls
from([userId1, userId2, userId3]).pipe(
  concatMap(userId => {
    // Each call waits for previous to complete
    return this.http.post('/api/process', { userId });
  })
).subscribe(result => console.log('Processed:', result));
```

### exhaustMap()

Ignore new emissions while an inner Observable is active.

```typescript
import { fromEvent } from 'rxjs';
import { exhaustMap, delay } from 'rxjs/operators';

// Prevent duplicate form submissions
const submitButton = document.getElementById('submit');

fromEvent(submitButton, 'click').pipe(
  exhaustMap(() => {
    // Ignore additional clicks while request is pending
    return this.http.post('/api/submit', formData).pipe(
      delay(2000) // Simulating slow network
    );
  })
).subscribe(result => {
  console.log('Form submitted:', result);
});
```

**Use exhaustMap when:**
- You want to ignore emissions during an active inner Observable
- Preventing duplicate operations (form submissions, logins)
- First-come-first-served scenarios

### Comparison of Higher-Order Operators

```typescript
import { interval } from 'rxjs';
import { take, map, switchMap, mergeMap, concatMap, exhaustMap } from 'rxjs/operators';

const source$ = interval(1000).pipe(take(3));
const inner$ = (x: number) => interval(750).pipe(
  take(3),
  map(i => `${x}-${i}`)
);

// switchMap - Cancels previous
source$.pipe(switchMap(x => inner$(x))).subscribe(console.log);
// Output: 0-0, 1-0, 2-0, 2-1, 2-2

// mergeMap - All in parallel
source$.pipe(mergeMap(x => inner$(x))).subscribe(console.log);
// Output: 0-0, 0-1, 1-0, 0-2, 1-1, 2-0, 1-2, 2-1, 2-2

// concatMap - Sequential
source$.pipe(concatMap(x => inner$(x))).subscribe(console.log);
// Output: 0-0, 0-1, 0-2, 1-0, 1-1, 1-2, 2-0, 2-1, 2-2

// exhaustMap - Ignores new while active
source$.pipe(exhaustMap(x => inner$(x))).subscribe(console.log);
// Output: 0-0, 0-1, 0-2
```

---

## Combination Operators

### combineLatest()

Combine multiple Observables; emit when any emits (after all have emitted at least once).

```typescript
import { combineLatest, of, timer } from 'rxjs';
import { map } from 'rxjs/operators';

const age$ = of(25, 26, 27);
const name$ = of('Alice', 'Bob');
const active$ = of(true, false);

combineLatest([age$, name$, active$]).subscribe(
  ([age, name, active]) => {
    console.log({ age, name, active });
  }
);
// Output (last combination):
// { age: 27, name: 'Bob', active: false }

// Practical example: Form validation
const email$ = emailInput.valueChanges;
const password$ = passwordInput.valueChanges;
const terms$ = termsCheckbox.valueChanges;

combineLatest([email$, password$, terms$]).pipe(
  map(([email, password, terms]) => {
    return isValidEmail(email) && password.length >= 8 && terms;
  })
).subscribe(isValid => {
  submitButton.disabled = !isValid;
});
```

### forkJoin()

Wait for all Observables to complete, then emit the last value from each.

```typescript
import { forkJoin } from 'rxjs';
import { ajax } from 'rxjs/ajax';

// Load multiple resources in parallel
forkJoin({
  user: ajax.getJSON('/api/user/1'),
  posts: ajax.getJSON('/api/posts'),
  comments: ajax.getJSON('/api/comments')
}).subscribe(({ user, posts, comments }) => {
  console.log('All data loaded:', { user, posts, comments });
});

// Array syntax
forkJoin([
  getUser(1),
  getUser(2),
  getUser(3)
]).subscribe(users => {
  console.log('All users loaded:', users);
});
```

### merge()

Merge multiple Observables into one.

```typescript
import { merge, fromEvent } from 'rxjs';
import { mapTo } from 'rxjs/operators';

const clicks$ = fromEvent(document, 'click').pipe(mapTo('click'));
const hovers$ = fromEvent(document, 'mouseover').pipe(mapTo('hover'));
const keypresses$ = fromEvent(document, 'keypress').pipe(mapTo('keypress'));

merge(clicks$, hovers$, keypresses$).subscribe(event => {
  console.log('Event occurred:', event);
});
```

### concat()

Concatenate Observables in sequence.

```typescript
import { concat, of } from 'rxjs';
import { delay } from 'rxjs/operators';

const first$ = of(1, 2, 3).pipe(delay(1000));
const second$ = of(4, 5, 6).pipe(delay(1000));
const third$ = of(7, 8, 9).pipe(delay(1000));

concat(first$, second$, third$).subscribe(console.log);
// Output: 1, 2, 3 (after 1s), 4, 5, 6 (after 2s), 7, 8, 9 (after 3s)
```

### withLatestFrom()

Combine source with latest from other Observables.

```typescript
import { fromEvent, interval } from 'rxjs';
import { withLatestFrom, map } from 'rxjs/operators';

const clicks$ = fromEvent(document, 'click');
const timer$ = interval(1000);

clicks$.pipe(
  withLatestFrom(timer$),
  map(([clickEvent, timerValue]) => timerValue)
).subscribe(value => {
  console.log(`Clicked at timer value: ${value}`);
});
```

---

## Utility Operators

### tap()

Perform side effects without modifying the stream.

```typescript
import { of } from 'rxjs';
import { tap, map } from 'rxjs/operators';

of(1, 2, 3, 4, 5).pipe(
  tap(value => console.log('Before map:', value)),
  map(x => x * 2),
  tap(value => console.log('After map:', value))
).subscribe(result => console.log('Final:', result));

// Logging HTTP requests
this.http.get('/api/users').pipe(
  tap(() => console.log('Request started')),
  tap(users => console.log('Received users:', users)),
  tap(() => console.log('Request completed'))
).subscribe();
```

### delay()

Delay emissions by a specified time.

```typescript
import { of } from 'rxjs';
import { delay } from 'rxjs/operators';

of('Hello', 'World').pipe(
  delay(2000) // Delay by 2 seconds
).subscribe(console.log);
```

### timeout()

Error if Observable doesn't emit within specified time.

```typescript
import { of } from 'rxjs';
import { delay, timeout, catchError } from 'rxjs/operators';

of('Data').pipe(
  delay(3000),
  timeout(2000), // Error if takes longer than 2 seconds
  catchError(error => {
    console.error('Timeout!', error);
    return of('Default value');
  })
).subscribe(console.log);
```

### retry()

Retry an Observable sequence on error.

```typescript
import { ajax } from 'rxjs/ajax';
import { retry, catchError } from 'rxjs/operators';
import { of } from 'rxjs';

ajax.getJSON('/api/unreliable-endpoint').pipe(
  retry(3), // Retry up to 3 times
  catchError(error => {
    console.error('Failed after 3 retries:', error);
    return of({ error: 'Service unavailable' });
  })
).subscribe(data => console.log(data));
```

### catchError()

Handle errors gracefully.

```typescript
import { of, throwError } from 'rxjs';
import { catchError, map } from 'rxjs/operators';

const source$ = throwError(() => new Error('Oops!'));

source$.pipe(
  catchError(error => {
    console.error('Error caught:', error.message);
    return of('Fallback value');
  })
).subscribe(value => console.log(value));
// Output: Fallback value
```

### finalize()

Execute code when Observable completes or errors.

```typescript
import { of } from 'rxjs';
import { finalize } from 'rxjs/operators';

of(1, 2, 3).pipe(
  finalize(() => console.log('Cleanup: Observable completed or errored'))
).subscribe(
  value => console.log(value),
  error => console.error(error),
  () => console.log('Complete')
);
// Output: 1, 2, 3, Complete, Cleanup: Observable completed or errored
```

---

## Common Operator Patterns

### 1. Search with Debounce

```typescript
import { fromEvent } from 'rxjs';
import { debounceTime, distinctUntilChanged, switchMap, catchError } from 'rxjs/operators';

const searchInput = document.getElementById('search') as HTMLInputElement;

fromEvent(searchInput, 'input').pipe(
  map(event => (event.target as HTMLInputElement).value),
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(term => searchAPI(term).pipe(
    catchError(error => {
      console.error('Search error:', error);
      return of([]);
    })
  ))
).subscribe(results => displayResults(results));
```

### 2. Infinite Scroll

```typescript
import { fromEvent } from 'rxjs';
import { throttleTime, map, filter, switchMap } from 'rxjs/operators';

fromEvent(window, 'scroll').pipe(
  throttleTime(200),
  map(() => {
    const scrollHeight = document.documentElement.scrollHeight;
    const scrollTop = document.documentElement.scrollTop;
    const clientHeight = document.documentElement.clientHeight;
    return scrollHeight - scrollTop - clientHeight;
  }),
  filter(distanceToBottom => distanceToBottom < 100),
  switchMap(() => loadMoreItems())
).subscribe(items => appendItems(items));
```

### 3. Polling

```typescript
import { interval } from 'rxjs';
import { switchMap, retry, catchError } from 'rxjs/operators';

// Poll every 5 seconds
interval(5000).pipe(
  switchMap(() => fetchData().pipe(
    retry(3),
    catchError(error => {
      console.error('Polling error:', error);
      return of(null);
    })
  )),
  filter(data => data !== null)
).subscribe(data => updateUI(data));
```

### 4. Type-ahead with Minimum Characters

```typescript
import { fromEvent } from 'rxjs';
import { debounceTime, distinctUntilChanged, filter, switchMap } from 'rxjs/operators';

fromEvent(searchInput, 'input').pipe(
  map(event => (event.target as HTMLInputElement).value),
  debounceTime(300),
  distinctUntilChanged(),
  filter(term => term.length >= 3),
  switchMap(term => searchAPI(term))
).subscribe(results => displayResults(results));
```

---

## Summary

| Operator Type | Common Operators | Use Case |
|--------------|------------------|----------|
| Transformation | map, pluck, mapTo | Transform values |
| Filtering | filter, take, skip, debounce, throttle | Filter stream |
| Higher-Order | switchMap, mergeMap, concatMap, exhaustMap | Handle nested Observables |
| Combination | combineLatest, forkJoin, merge, concat | Combine streams |
| Utility | tap, delay, timeout, retry, catchError | Side effects and control |

## Next Topic

Continue to [Subjects](./04-subjects.md) to learn about special types of Observables that can multicast.
