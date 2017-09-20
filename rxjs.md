# RXJS

- "Everything is a stream"
- Observable - asynchronous collection observed as a stream in time
  - hot - stream that has data pre and post being observed
  - cold - stream that does not have data pre and post being observed
- Observable leverages the Iterator and Observer pattern to create a best-of-both worlds object:
  - Iterator's *push*
  - Observer's *pull*
- Observables can model events, async server requests, animations, and data/arrays in a uniform way:
  - push
    - onNext() - observer's update callback
    - onError() - observer's error callback
    - onComplete() - observer's complete callback
  - pull
    - forEach() - observer's subscribtion/unsubscription hook for update, error, and completion changes
