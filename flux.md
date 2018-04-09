# flux

*Flux* is an application architecture that leverages four distinct parts to attain one-way data flow:
1. Actions
2. Dispatchers
3. Stores
4. Views

The goal of the Flux architecture is to simplify application state management via:
- one-way data flow
- simple mental model

![Flux Flow](https://facebook.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png "Flux Flow")

## redux

*Redux* is an implementation of the Flux pattern that intentionally enforces a single vs multiple store approach

### Redux Principles
*via http://redux.js.org/docs/introduction/ThreePrinciples.html*
1. *Single source of truth* - The **state** of your whole application is stored in an object tree within a single **store**.
2. *State is read-only* - The only way to change the state is to emit an *action*, an object describing what happened.
3. *Changes are made with pure functions* - To specify how the **state** tree is transformed by **actions**, you write pure **reducer**s.

## react-redux

*React-redux* utilizes two aspects:
1. one `<Provider store={store} />` component - wraps the `<App/>` and automatically binds the store reference to each component nested in `<App/>` (via `this.context.store` vs. the commonly used `this.props`/`this.state` references)
2. `connect()` API - an API for leveraging in a higher order component that maps store values and dispatch actions to a concrete component's `props`

### redux-thunk

*thunk* - a function that returns a function

An imperative approach that updates redux to understand `dispatch`ed functions as opposed to solely actions. Similar to how a `Promise` abstracts async work, so too does a thunk while additionally normalizing sync and async actions.

### redux-saga

*saga* - a generator function that can run indefinitely inside the system

A declarative approach that abstracts async work by leveraging ES6 generators to make async code look like sync code. Unlike the redux-thunk approach, redux-sage better lends itself to testing without mocks. A more accurate name for redux-saga could be redux-process-manager.

## mobx-react

Composed of:
1. Observable state
2. Actions
3. Derivations
    - Computed properties
    - Reactions
  
![MobX Flow](https://mobx.js.org/docs/flow.png "MobX Flow")
  
Similar to react-redux, you use a `<Provider storeA={storeA} />` component that wraps a component (`<Application/>` for example) to provide shared state with components. The difference from react-redux is that more than one store is a valid approach. In order for nested components to then leverage a given store, an `@inject` decorator is used resulting in a `this.props.storeA` reference in the component. Where MobX shines is its use of Observable and decorators, namely:
- *Observable state* via `@observable` - wraps a value (literal, object, array, etc) in an Observable
    - use `@observable` on values that change at runtime
- *Actions* via `@action` - explicit decoration of a function that modifies state
    - use `@action` on functions that manipulate an `@observable`
- *Computed properties* via `@computed` - explicit decoration of a function that computes a value from observables (likely implemented as an observable of observables under the hood). Additionally there is a perf win as the computed value is memoized
    - use `@computed` on funtions that derive a computed state from one or more `@observable`s
- *Reactions* via `@observer` - higher-order component pattern so a react component actually rerenders when an observable changes
    - use `@observer` on a component class whose `@observable`s, `@action`s, or `@computed`s would result in a rerender

Additional Notes:
- "MobX reacts to any an existing observable property that is read during the execution of a tracked function."
- Use a `transaction` if editing many observables at once where you know you only want an "update" to occur after all thed observable edits have been made. MobX uses this internally, but is useful depending on your use case.

## mobx-state-tree

Philosophy
> "`mobx-state-tree` is a state container that combines the simplicity and ease of mutable data with the traceability of immutable data and the reactiveness and performance of observable data."

> "React, but for data" - Daniel Earwicker

`mobx` isn't opionated about how data should be structured where `mobx-state-tree` is. With this opionated approach it supports:
- snapshots (useful for timetraveling)
- hot module reloading support (changing app source code functionality in real-time)
- clone (useful for delaying sync of drafts/edits)
- replayable actions
- middleware
- classical inheritence via `types.compose`

Implementation
- MST leverages typed models via `types.model({...})` with actions for changing read-only model data via `types.actions({...})`. The actions mutate model data, but MobX with MST ensures immutable copies or "Snapshots" are preserved automatically. This enables the above features like timetraveling and undo/redo via "Patches".
- Creating models is done via the `MyModel.create({...})` API.
- Derive data from existing state, don't duplicate data (ex. fullName as derivation of firstName and lastName). In MST this is done via `types.views({...})` which are observable read-only MobX `@computed` values which are memoized.
- The `clone` API enables editing without impacting the core model until the overwrite is confirmed. The `applySnapshot` and `getSnapthot` APIs are used in conjunction to confirm the overwrite via `applySnapshot(this.props.item, getSnapshot(this.state.clone));`.
- `types.literal` and `types.union` are powerful was to define types in MST.
- The `flow` API abstracts async work as asynchronous actions.
- The `type.identifier()` and `types.reference` APIs are a simple and powerful way to prevent duplicate data while ensuring MST automatically resolves references with what would otherwise be solved with repetitive boilerplate (for each model needing this behavior identified).
- `types.model` has various useful lifecycle hooks like `afterCreate`, `preProcessSnapshot`, and `beforeDestroy`.
    - The `afterCreate` lifecycle hook is great in coordination with `onSnapshot` to auto save when changes happen to a particular model snapshot.

## ngrx

- Store > Selector > ViewModel > View > Actions > Reducers > ∞

ngrx (redux in Angular) is a system of design patterns for one-way data-flow for SPA/RIA client apps. The system works like this:
1. A **store** is a client-side observable database, the app model, whose JSON data is initially queried from a server. It is the single source of truth for data.
2. Components then dependency inject this **store** and leverage a **selector** function to create their respective viewmodel. This viewmodel may identically reflect a **store**’s model, but instead likely combines disparate parts of the **store** to create a custom model (viewmodel). The view then accurately updates and reflects the subset of the **store**’s data.
3. The **store** may update during runtime. This is where an **action** is used. An **action** is dispatched to communicate that a **store** needs to be updated, most often as a result of a desireable viewmodel change. The view (and any other views) only update after the **store** has updated.
4. A **reducer** function consumes these **action**s and based on type, updates the **store** (or a subset of it) in an immutable way.
5. Since the **store** is observable, all the components update automatically.
