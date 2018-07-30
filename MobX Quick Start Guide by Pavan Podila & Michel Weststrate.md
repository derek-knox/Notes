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
  1. `autorun` - an API accepting a *tracking function* that autoruns when any of its nested `observable`s change
  1. `reaction` - 
  1. `when` - 
- `actions` - a function wrapper for `observable` mutations that's transactional (`observer`s are notified when the function completes as more than one `observable` may update within it)

## Observables, Actions, and Reactions

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

- `runInAction()` is a utility function useful for the result of an `async` operation to be used for updating `observable`s
- `flow()` is another utility function but is an alternative to `runInAction()` that takes a `generator` function whose mutations following `yield` are automatically wrapped with `action()`
- `observer()` of `mobx-react` creates a *higher-order-component (HOC)* when wrapping a React component. Internally, `observer()` keeps track of the component's dependent `observable`s and calls the component's `render` method when any of them changes.
  - It's best practice to start out with a monolithic component and once working, gradually decompose it into other `observer` sub-components. This ensures only specific sub-components are rerendered as opposed to the entire monolithic component. MobX already granularly is tracking the `observable`s and thus can ensure React only rerenders the sub-component that truly has changed.
- `@inject` of `mobx-react` is how you bind a specific `observable` store to a React component
  - ```
  ReactDOM.render(
<Provider store={store}>
<App /> </Provider>,
       document.getElementById('root'),
   );
  ```
