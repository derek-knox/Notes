# Asynchronous Programming in JavaScript

- "Everything is a stream" - Rob Wormald (ngrx)
- "Asynchronous arrays" - Kyle Simpson (Frontend Masters - Deep JS Foundations)
- "An Observable is like a DOM event that can accept three handlers instead of one" - Jafar Husain (Frontend Masters - Advanced Asynchronous JavaScript)
- Observable - asynchronous collection observed as a stream over time
  - hot - stream that has data pre and post being observed
  - cold - stream that does not have data pre and post being observed
- Observable leverages the Iterator and Observer pattern to create a best-of-both worlds object:
  - Iterator's *push* (producer)
  - Observer's *pull* (consumer)
  - An Observable is capable of denoting update, error, and completion where the typical Observer pattern doesn't have a built-in way for consuming an error or completion. This is the best-of-both worlds aspect that an Observable solves.
- Observables can model events, async server requests, animations, and data/arrays in a simplified and uniform way, this is the win. Observables can both push and pull:
  - push
    - `onNext(payload)` - observer's update callback
    - `onError(error)` - observer's error callback
    - `onComplete()` - observer's complete callback
  - pull
    - `forEach(observer)` - observer's subscribtion/unsubscription hook for update, error, and completion changes
      - `observer = { onNext: updateCb, onError: errorCb, onComplete: completeCb }`
