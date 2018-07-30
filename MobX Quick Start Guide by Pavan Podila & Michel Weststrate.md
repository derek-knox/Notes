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
