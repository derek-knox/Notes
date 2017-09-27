# Advanced Asynchronous Programming in JavaScript - Jafar Husain

## Observable
The goal of learning about Observable and specifically the `map()`, `filter()`, `reduce()`, `merge()`, and `zip()` operators is so you can:
1. code without loops
    - declarative (manage less state)
    - functional (mutate less state)
2. abstract the various async APIs into one API (`next()`, `error()`, `complete()`)
3. employ automatic cleanup
    - mitigate memory leaks (`take()`/`takeUntil()`)
    - error forwarding

- Observable quotes:
  - "Everything is a stream" - Rob Wormald (ngrx)
  - "Asynchronous arrays" - Kyle Simpson (Frontend Masters - Deep JS Foundations)
  - "An Observable is like a DOM event that can accept three handlers instead of one" - Jafar Husain (Frontend Masters - Asynchronous Programming in JavaScript)
- Observable - collection that arrives over time (Observable === Collection + Time)
  - cold - "no matter when you subscribe, you get the same data"
  - hot - "no matter when you subscribe, you might get different data"
- Observable leverages the Iterator and Observer pattern to create a best-of-both worlds object:
  - Iterator's *push* (producer)
  - Observer's *pull* (consumer)
  - An Observable is capable of denoting update, error, and completion where the typical Observer pattern doesn't have a built-in way for consuming an error or completion. This is the best-of-both worlds aspect that an Observable solves.
- Observables can model events, async server requests, animations, and data/arrays in a simplified and uniform way, this is the win. They enable a unified programming model for transforming any collection. Observables push data while providing a mechanism for an observer to pull and consume the changes:
  - push
    - `next(payload)` - observer's update callback
    - `error(error)` - observer's error callback
    - `complete()` - observer's complete callback
  - pull
    - `forEach(observer)` - observer's subscribtion/unsubscription hook for update, error, and completion changes
      - `observer = { next: onUpdate, error: onError, complete: onComplete }`
      
## Observable of Observables Flattening Strategies

Main three flattening strategies:
1. `concatAll` - observable of observables that mitigates race conditions by ensuring async sequence is respected (similar to flattening a 2D array)
2. `mergeAll` - observable of observables like `concatAll` but sequence is ignored in favor of first-come, first-served
3. `switchLatest` - observable of observables like `concatAll` where dispatches of observables dispose those prior (replaces state machines)
    - "Instead of building a machine with a bunch of moving parts to compute an answer, we're going to write the answer... were' going to do this declaratively" - Jafar Husain.
  
Thought process:
- n-dimensional nesting process (same process for Arrays)
  1. `map()` until you have an identifier bound to every value you need
  2. flatten by n-1 nested levels (usually with `concatAll()`)
- `concatMap()` n-1 nested with 1 `map()` as last nested operation is the functional version of a nested forEach
- `zip()` - useful flattening approach acting as a `map()` of two arrays at each index (even for len 1 arrays)

## Additional Helper Operators
- `take()` - updates n number of times (once by default) and then automatically unsubscribes
- `takeUntil()` - utility for composing a source observable with a stop observable that auto disposes when the stop observable dispatches (`next` or `complete`)
- `throttle()` - delays updates
- `retry()` - retries on error
- `doAction()` - ensure a synchronous side-effect occurs as part of an observable's `forEach()` execution
 
## Implementation Process
1. Think about any change as a collection of changes over time (a stream)
2. Abstract it to an Observable
3. Compose and operate over the stream(s)
4. Flatten the composition and implement the desired side-effect
5. Ensure proper cleanup operator is used (if applicable)
