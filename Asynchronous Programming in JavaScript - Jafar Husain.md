# Asynchronous Programming in JavaScript - Jafar Husain

## Observable
- "Everything is a stream" - Rob Wormald (ngrx)
- "Asynchronous arrays" - Kyle Simpson (Frontend Masters - Deep JS Foundations)
- "An Observable is like a DOM event that can accept three handlers instead of one" - Jafar Husain (Frontend Masters - Asynchronous Programming in JavaScript)
- Observable - asynchronous collection observed as a stream of changes over time
  - hot - `forEach` has been called on the observable
  - cold - `forEach` has not been called on the observable
- Observable leverages the Iterator and Observer pattern to create a best-of-both worlds object:
  - Iterator's *push* (producer)
  - Observer's *pull* (consumer)
  - An Observable is capable of denoting update, error, and completion where the typical Observer pattern doesn't have a built-in way for consuming an error or completion. This is the best-of-both worlds aspect that an Observable solves.
- Observables can model events, async server requests, animations, and data/arrays in a simplified and uniform way, this is the win. Observables push data while providing a mechanism for an observer to pull and consume the changes:
  - push
    - `next(payload)` - observer's update callback
    - `error(error)` - observer's error callback
    - `complete()` - observer's complete callback
  - pull
    - `forEach(observer)` - observer's subscribtion/unsubscription hook for update, error, and completion changes
      - `observer = { onNext: updateCb, onError: errorCb, onComplete: completeCb }`
      
## Observable of Observables Flattening Strategies

- `concatAll` - observable of observables that mitigates race conditions by ensuring async sequence is respected (similar to flattening a 2D array)
- `mergeAll` - observable of observables like `concatAll` but sequence is ignored in favor of first-come, first-served
- `switchLatest` - observable of observables like `concatAll` where dispatches of observables dispose those prior (replaces state machines)
  - "Instead of building a machine with a bunch of moving parts to compute an answer, we're going to write the answer... were' going to do this declaratively" - Jafar Husain.
- `takeUntil` - utility for composing a source observable with a stop observable that auto disposes when the stop observable dispatches (`onNext` or `onComplete`)
- n-dimensional nesting process (same process for Arrays)
  1. `map()` until you have a variable bound to every value you need
  2. `concatAll()` n-1 nested levels to flatten the desired result