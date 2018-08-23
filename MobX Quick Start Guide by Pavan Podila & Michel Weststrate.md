# MobX Quick Start Guide by Pavan Podila & Michel Weststrate

## Introduction to State Management

MobX is a reactive state management library with uni-directional data flow and an elegant and small API.
- UI = `fn(state)`
- VirtualDOM = `fn(props, state)`

State Changes
- State > UI > Actions > (repeat)
- State > Side Effect > Actions > (repeat)

MobX Use
- `observable` - function that wraps any data and automatically wires up change tracking on its properties
- `observer` - a listener of `observable` changes (aka *reactions*)
  1. `autorun`
  1. `reaction`
  1. `when`
- `actions` - a function wrapper for `observable` mutations that's transactional (`observer`s are notified when the function completes as more than one `observable` may update within it)

## Observables, Actions, and Reactions

The MobX Triad = `observable`s, `action`s, and reactions

### Observables
`Observable` objects only track the properties provided in the initial value given. You need to use *observable maps* for dynamic property tracking. Additionally, tracking JavaScript primitives require *box*ing though explicitly doing so is rare.
  - `observable map` - useful for tracking dynamically added/removed properties
  - `observable.box()` - convert primitive values into an observable
  
Observable types (`observable()` is shorthand and automatically uses the below APIs):
- objects: `observable.object({})`
- arrays: `observable.array([])`
  - use `toJS()` for actual JavaScript array to use with non-MobX APIs/libs
- maps: `observable.map(value)`
  - use w/ES6 Map (tracks additions/removals after creation unlike an `observable.object`)
- primitives, functions, class-instances: `observable.box(value)`

*MobX applies deep observability (every property at every level of an object-tree, array, or map including additions/removals for arrays and maps)*

Computed `observable`s are automatically tracked `observable`s where it *devives its value* from other `observable`s. This ensures the explicitly tracked state is minimal where combinations of existing `observable` state can be *derived*, thus producing these computed `observable`s (aka *derivations*).
  - An example computed `observable` could be `fullName` where only the `firstName` and `lastName` `observable`s explicitly are tracked. Any change to `firstName` and `lastName` also results in the automatic and tracked change of `fullName`
  - This approach enables a developer to model a variety of scenarios with ease
  - Nested computeds work too where MobX internally builds an optimized dependency tree with proper caches via memoization unlike JavaScript's eagerly evaluated `get` properties
  
  *The `@decorator` sytnax (`@observable`, `@computed`, & `@action`) can be used in MobX with JavaScript classes vs the function API for more succint authoring*

### Actions
Actions provide declarative names (function names decorated w/`@action`) to the operations that mutate state.
  - Using `action`s boosts performance as it acts as an *atomic transaction* (all `observable`s within the `action` fire their change events upon the `action`s completion vs incrementally line-by-line)
  - Use the `configure()` function to set `enforeActions` option to true so MobX throws an error if you mutate an `observable` outside of an `action`
  - `@action.bound` pre-binds the `this` to the class instance

### Reactions
*Reaction*s are the side-effect causing behaviors of `observable` changes. They are expressed via:
- `autorun()` - runs the passed *tracking function* immediately and on every change to its dependent `observable`s
- `reaction()` - takes a *tracking function* like `autorun()` but waits for changes in its `observable`s and compares the result against the previous *tracking function*'s return value. If different, an *effect function* runs. This is a more optimized and finer controlled run side-effect.
  - `mobx-react`'s `observer()` function enables a React component's `render()` to be the *effect function* of a `reaction()`.
- `when()` - when its *predicate function* returns true, it executes the *effect-function* and then automatically disposes the side-effect
  - a one argument version of `when()` returns a `promise`
  
*The `autorun()`, `reaction()`, and `when()` functions return a *disposer function* for manual cancellation of the side-effect*

## A React App with MobX

- `runInAction()` is a utility function useful for the result of an `async` operation to be leveraged when updating `observable`s
- `flow()` is another utility function but is an alternative to `runInAction()` that takes a `generator` function whose mutations following `yield` are automatically wrapped with `action()`
- `observer()` of `mobx-react` creates a *higher-order-component (HOC)* when wrapping a React component. Internally, `observer()` keeps track of the component's dependent `observable`s and calls the component's `render` method when any of them changes (where the `render()` is the `reaction()` *effect function*).
  - It's best practice to start out with a monolithic component and once working, gradually decompose it into other `observer` sub-components. This ensures only specific sub-components are rerendered as opposed to the entire monolithic component. MobX already granularly is tracking the `observable`s and thus can ensure React only rerenders the sub-component that truly has changed.
- `@inject` of `mobx-react` is how you bind a specific `observable` store to a React component
  - `mobx-react`'s `Provider` `store` prop (you can name this prop anything) is how you bind store(s) to your entire React application. Internally, the React Context propogates the prop value to any component wrapped with the aforementioned `@inject`. The component can then access it via `this.props`.
  ```
  ReactDOM.render(
      <Provider store={store}>
          <App />
      </Provider>,
      document.getElementById('root'),
  );
  ```

## Crafting the Observable Tree

- The `@observable` is shorthand for `@observable.deep` where deep observation stops at objects with a constructor or prototype (as they're expected to then have their own `observable` properties)
- `@observable.shallow` is most useful for arrays and maps where only the length/size, additions, and removals are of side-effect interest
- `@observable.ref` is useful if only the value assigned (and not data in the structure itself) is of side-effect interest
- `@observable.struct` tracks property values vs property reference to validate side-effect execution
  - `{ x: 0, y: 0 }` and `{ x: 0, y: 0 }` are two distinct objects, but they're structurally the same
- The `extendObservable()` API allows runtime property additions to preexisting `observable`s
  - `extendObservable({}, object) === observable.object() === observable()` so `extendObservable` is the superset
  - `extendObservable` is required if dynamically adding `action`s or `computed`s is required as `observable` map can only track dynamically added/removed state carrying properties
- Caching and automatic clean-up (GC) are the main reasons MobX recommends liberal usage of `computed`s

*Observable State = Core (mutable) State + Derived (read-only) State*

## Derivations, Actions, and Reactions

### Deverivations
- `computed`s can have a setter which can then explicitly update its observables in one shot (automatically wraped in an `action`

### Actions
- An `@action` function, under the hood, is wrapped by low-level `untracked()` and `transaction()` calls
  - `untracked()` - prevents tracking of `observable`-`observer` relationships inside mutating function
  - `transaction()` - batches, coerces notifications on same `observable`, and dispatches the minimal notification set at `action` end
  - `action = untracked(transaction(allowStateChanges(true, <mutating-function>)));`
  - nested actions ensure notifications only go out after outermost action execution completes
- the `runInAction()` utility ensures `async`/`await` accounts for `observable` mutations properly
- the `flow()` utility is useful if multiple `await`s become a PIA
  - MobX provides a `cancel()` API to quit the `flow`
  
### Reactions
- `autorun()` & `reaction()` can take a second arg `options` object
  - `delay` option - acts as a debouncer for frequent changing `observable`s

### MobX Reaction Rules:
- Always dereference (read) observables during the execution of the *tracking-function*. Dereferencing is the key to establishing the MobX tracker.
- Tracking only happens in the synchronously executing code of the *tracking-function*
- Only `observable`s that already exist will be tracked
- One exception to the previous rule is for `observable map`s where a dynamic key is also tracked
  - MobX 5 can track *not-yet-existing* properties for all objects created using the `observable()` API
  
## Handling Real-World Use Cases

- React components that are `@observer`s read the `@observable`s in the `render()` to set up tracking. Changes to an `@observable` results in a re-`render()`
- `mobx-react`'s `<Observer>` component is a declarative shortcut to manually creating a React component class that uses `@observer`, `@inject`, and consumes `@observable`s as `props` in the `render()` function
  
## Special API for Special Cases

- MobX `observable`s track their observed status via:
  - `onBecomeObserved` and `onBecomeUnobserved` APIs
  - These APIs are great for external use when an `observable`s initial value has expensive set-up or tear-down
- MobX doesn't update an `observable` value instantly when mutated, it instead uses an internal `intercept()` API to keep, modify, or discard the proposed mutation. The proposed mutation (change argument object) has four properties:
  1. `type` - Either add, delete, or update
  2. `object` - The object *on which the change happened*
  3. `newValue` - Proposed new value (when `type` is add or update)
  4. `oldValue` - Previous value (when `type` is delete or update)
- MobX's `observe()` API is the counterpart to `intercept()`
- MobX's `trace()` API allows you to learn why a computed property, reaction, or component render is being invoked

## Exploring `mobx-utils` and `mobx-state-tree`
`mobx-utils`
- the `fromPromise()` API which returns a MobX aware and decorated promise has a `case()` API that makes conditional pending/fulfilled/rejected React `render()` calls more readable
- the `lazyObservable()` API defers expensive operations until needed
- the `now()` API which treats *time* as an `observable` that updates at the given interval (1000ms default)
- the `createViewModel()` API is ideal for creating a cloned model for editing where form submission confirms the cloned model changes should be reflected in the root model (easy undo for a group of property edits). This allows you to treat the entire editing process as a single transaction.
- the `queueProcessor()` allows an operation to automatically be performed on each element addition to an array

`mobx-state-tree`
- `mobx-state-tree` enforces typed `observable` models, enforces mutations in `action`s, and resultingly enables middleware features like logging, time traveling, undo/redo, database sync, etc out-of-the-box. MST introduces strict structure for clearly defining state (`observable's`), mutations (`action`s), and derivations (`computed`s).
```
const Todo = types
  .model(/**/)      // observables
  .actions(/**/)    // actions
  .views(/**/)      // computeds
```
- the `types.maybe()` API for defining an `observable` property type provides the type (`type.string` for example) with built-in runtime type-safety for validating input. Setting a model's value explicitly as opposed to the `type.xxx` type provides that value as the default if non is set during instantiation.
- stores are *higher-order types*
  - a recommended approach for starting out is to encode the server-fed JSON in MST models and then to *fatten* the model by applying more rigid typing, attaching actions, and attaching views
- Some domain models are bi-directional or associative and are less condusive to a tree structure, MST accounts for this via:
  - foreign key pattern (database unique key)
  - the `getRoot` API in combination with the fact that each MST node knows its own location in the tree allows navigating to the proper model in the tree when referencing another model by the foreign key pattern
  - think of the MST tree as a *file system for state*
  - `types.identifier()` is a built-in API for this foreign key pattern in MST
  
MST out-of-the-box features
- Snapshots - are immutable versions of the state-tree in memory retrievable via the `getSnapshot()` API. This makes quickly serializing a breeze. Since each MST node is a state-tree itself, serializing subtrees is just as easy. Combined with the  `applySnapshot()` API, MST allows easy time-traveling and wholistic or granular undo/redo
- Patches - are efficient snapshots for real-time changes and frequent server-client communication. They're used via `onPatch` and `applyPatch`.
- Logging and authentication are other features availabe as MST's strict `action` use guarentees middleware (logging, authenticating, time-travel, and undo/redo) is possible. Check `mst-middlewares` package.

## MobX Internals

MobX is architected in three layers (each built upon the previous):
  1. `Atom`s
      - atomic unit of the observable dependency tree
      - keeps track of its observers but not the value itself
  1. `ObservableValue`, `ComputedValue`, and Devivations
      - `ObservableValue` extends `Atom` and provides value storage (and core implementation of boxed `Observable`s)
      - Derivations and Reactions are the *observers* of `Atom`s. They respond to `Atom` changes and schedule reactions.
      - `ComputedValue` builds on the derivations and acts as an `Observable`
  1. `Observable`{`Object`, `Array`, `Map`} and APIs
      - Built on top of `ObservableValue` and represent properties and values
      - API layer of MobX

`Observable` vs `Observer`
- `ObservableValue` = `Observable` only
- `Reaction` = `Observer` only
- `ComputedValue` = Both `Observable` and `Observer`
    
### `Atom`
- At runtime MobX creates a backing dependency tree where each node is an `Atom`
- Two purposes:
  1. Notify when *read* (via `reportObserved()`)
  1. Notify when *changed* (via `reportChanged()`)
- Tracks its observers
```
class Atom {
  observers = [];
  reportObserved(){}
  reportChanged(){}
  /* ... among other housekeeping properties ... */
}
```
- `myObservable[$mobx].values.get('someProperty')` accesses the `observable` property's backing `Atom` but `getAtom(observableThing, observableThingProperty)` is a better API
- Though rare, the `createAtom(name, onBecomeObserved, onBecomeUnobserved)` API may be of use but is used internally in MobX
- an `Atom` becomes active in the reactive system due to an internal:
  - `autorun` ->
  - `atomInstance.get()` ->
  - `atomInstance.reportObserved()` and returns value for `get()` ->
  - `atomInstance.onBecomeObserved()` ->
  - passed in observe callback from `Atom` instance instantiation executes ->
  - something happens resulting in `atomInstance.reportChanged()` ->
  - changed value propagates value to all `observers` ->
  - function passed to `autorun` re-executes
- `Atom` becomes inactive in the reactive system due to an internal:
  - `disposer` function of `autorun` reaction executes ->
  - `atomInstance.reportUnobserved()` ->
  - `atomInstance.onBecomeUnobserved()` ->
  - passed in unobserve callback executes (external to MobX clean-up hook)
  
### `ObservableValue`
- Extends `Atom` but also:
  - stores the `observable` value
  - provides hooks for intercepting a value change
  - provides hooks for observing the value
```
class ObservableValue extends Atom {
  value;
  
  get() { /* ... */ this.reportObserved(); }
  set(value) { /* Interceptor passthrough */ this.value = value; this.reportChanged(); }
  
  intercept(handler){}
  observe(listener, fireImmediately){}
}
```
  - the inherited `reportObserved()` and `reportChanged()` enable participation of the reactivity system
  - the `intercept()` and `observed()` act as *event emmitters* and aren't affected by transactions so they fire immediately vs being batched
  - `ObservableBox`, `ObservableObject`, `ObservableArray`, and `ObservableMap` extend `ObservableValue`
    - `ObservableBox` just returns the actual `ObservableValue` :)
    
### `ComputedValue`
- Does not subclass `Atom` but is similar to `ObservableValue` excluding the `intercept()` ability
```
class ComputedValue {
  get() { /* ... */ reportObserved(this); /* ... */ }
  set(value) { /* ... rarely used ... */ }
  
  observe(listener, fireImmediately){}
}
```
- the *value computation* for a `ComputedValue` is called a derivation (aka side effect of a computation)
- `ComputedValue` is the only node in the dependency tree this is both an `Observable` and `Observer`
- `ComputedValue` optimizations
  - Only computed if more than one `Observer`
  - Once computed, the value is cached
  - Only a dependent `Observable`'s `reportChanged()` causes a re-computation
  - The value can be dependent on other `ComputedValue`s and so no computation occurs unless its immediate dependent children have changed
  
### Derivations
- `Observable`s (`ObservableValue` & `ComputedValue`) are the yin and reactions are the yang
- derivation and reaction are used interchangably and this is where tracking happens, they are *side effects*:
  - derivation = new value as side effect
  - reaction = new computation as side effect
- MobX uses a `globalState` to keep reference of the currently executing derivation or reaction
  - When a reaction runs:
    - all `Observable`s that fire `reportObserved()` are tagged to this reaction
    - Each `Observable` also tracks all its `Observer`s (reactions)
- `Action`s create transaction boundaries so all change notifications are only fired after completion. They can be nested too where only when the top most `Action` (transaction) completes do the tracked change notifications actually propagate (aka a batch)
  - Batch Steps
    - queued up reactions execute
    - cycle begins
    - tracked `Observable`s link (tagged) with executing derivation
    - add any newly discovered `Observable`s
    - queue any new reactions found during batch
    - if no batches, MobX determines the state stable, otherwise the cycle restarts
- reactions *pull values* from state and fire up the reactive process

### API Layer
`ObservableObject`
  - key-value pair backed by an `ObservableValue`
  - every computed propery is backed by a `ComputedValue`
  - its `keys()` method is backed by an `Atom` (useful when reaction needs to iterate) and when read results in `reportObserved()`
  - an added or removed key of `keys()` results in the `Atom`'s `reportChanged()` so connected reactions re-execute
`ObservableArray`
  - each indexed value is backed by `ObservableValue`
  - `length` property is backed by an `Atom` where reads and writes of `length` result in `reportObserved` and `reportChanged` respectively on the `Atom`
  - for Array read APIs like `map`, `reduce`, `filter`, etc. run the backing `Atom`'s `reportObserved()` executes
  - for Array write APIs like `splice`, `push`, `pop`, `shift`, etc run the backing `Atom`'s `reportChanged()` executes so connected reactions re-execute
`ObservableMap`
  - key-value pair backed by an `ObservableValue`
  - like `ObservableObject` it has an `Atom` for its `keys()` and when read results in `reportObserved()`
  - an added or removed key of `keys()` results in the `Atom`'s `reportChanged()` so connected reactions re-execute
  
These MobX collections (objects, arrays, and maps) are simply collections of `Observable` boxes (`ObservableValue`) organized as the data structure requires
  - these data structures naturally expose `intercept()` and `observe()` for more granular use
  
### Transparent Functional Reactive Programming
- *Transparent* - declarative syntax for connecting `Observer`s to `Observable`s with no explicit wiring in favor of the decorator syntax or its `observable()` function equivalent. This enables the autonomous functioning of the reactive system as the usage of an `Observable` inside a reaction (`Observer`) is enough for automatic wiring.
- *Functional* - leverages the power of functional programming by applying transformations to input data as output data where transformations are automatically applied due to the implicit `Observable` and `Observer` connections
- *Reactive* - the complexity of wiring and efficiency is handled by MobX's internal state tree management, optimizations, and deep knowledge of dependencies so reactions occur only when needed (no polling or excessive eventing)
- *Programming* - value oriented programming (VOP) is concernced with value change, dependencies, and propagation so you can focus on the *what* not the *how* which is counter to event oriented programming (EOP) that's concernced with event streams for change notification (reporting what happened but lacking dependency knowledge)
  - MobX's VOP relies on events internally and abstracts this otherwise explicit event wiring and notification in favor of the declarative `Observable` and `Observer` APIs for establishing connections

---

## Conclusion/Summary Notes:
MobX is a state management library that kicks ass for three core reasons:
  1. MobX abstracts away the otherwise explicit eventing and observing of changes in an app's runtime lifecycle
  1. Deep internal understanding of the state-tree enables selective and efficient UI re-renders
  1. minimal API so it is simple to understand, memorize, and implement

Two mental models that MobX promotes are:
  1. An app's UI is a function of its state (`UI = fn(state)`, functional programmers rejoice)
  1. Core state should be minimal, you're encouraged to think in terms of derived state (which MobX can further optimize)
    
All this means that you can use a tiny API in conjunction with traditional mutations and callbacks to automatically wire up efficient change tracking and subsequent observervable side-effects (like snappy UI updates).

---

## Questions:
1. How is MobX parsing/reading/tracking observables inside `autorun`'s/`reaction()`'s/`when()`'s *tracking-function*?
  - "...autorun() implicitly selects all observables in its effect-function" - How? What code implementation is enabling and making this happen?
    - Guessing the fn passed (since it's an object) is iterated over and due to the wrapping `observable` or `computed` fn it is flagged as such and as a result the tracking algorithm has its hook
1. What explicitly happens when an object is wrapped via `@observable`? (custom get/set I believe)
1. Any specific checklist or notes regarding "dereferencing" for ensuring `render()` calls? I read this book after making Reticle Designer and I ran into a few times where I was perplexed as to why a render update wasn't being registered.
1. The solution below works, but I was really trying to focus on `computed`/derivations and I feel like I'm missing a better (MobXish) way. Ultimately, I felt that knowing about a change in one or more objects was a *derivation*. Is there a better way to achieve the below?
```
...
this.controls.forEach((control) => {
  observe(control.settings, (change) => this.onSettingsChange({ control, change }));
});
...
@action.bound onSettingsChange(payload) {
  this.updateControlInFocus(payload.control);
}
...
```
1. C# version? What would need to be ported to make the most minimal implementation work in Unity? 
  - Thinking solely in the context or 2+ GameObjects each observing a store value (DataManager) where any store value change is automatically reflected in each GameObject. Maybe a first class hook into `Update()`? Custom `ObservableUpdate`?
1. In *Watching the Events Go By* there's a comment about how a dev can tap into MobX's internal event system to fine-tune expensive or otherwise control specific observables. Any common concrete examples of when this is useful?
