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
  - `observable map` - 
  - `observable.box()` - convert primitive values into an observable
  
Observable types (`observable()` is shorthand and automatically using the below APIs):
- objects: `observable.object({})`
- arrays: `observable.array([])`
  - use `toJS()` for actual JavaScript array to use with non-MobX APIs/libs
- maps: `observable.map(value)`
  - use w/ES6 Map (tracks additions/removals after creation unlike an `observable.object`
- primitives, functions, class-instances: `observable.box(value)`

*MobX applies deep observability (every property at every level of an object-tree, array, or map including additions/removals fo arrays and maps)*

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
_______
Questions:
- How is MobX parsing/reading/tracking observables inside `autorun`'s/`reaction()`'s/`when()`'s *tracking-function*?
  - "...autorun() implicitly selects all observables in its effect-function" - How?
    - Guessing the fn passed (since it's an object) is iterated over and due to the wrapping `observable` or `computed` fn it is flagged as such and as a result the tracking algorithm has its hook
- what explicitly happens when an object is wrapped via `@observable`? (custom get/set I believe)
- In *Watching the Events Go By* there's a comment about how a dev can tap into MobX's internal event system to fine-tune expensive or otherwise control specific observables. Any concrete example of when this is useful?
