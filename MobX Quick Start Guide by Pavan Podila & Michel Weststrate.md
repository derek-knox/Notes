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
- maps: `observable.map(value)`
- primitives, functions, class-instances: `observable.box(value)`

*MobX applies deep observability (every property at every level of an object-tree, array, or map including additions/removals fo arrays and maps)
