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

## mobx

Composed of:
1. Observable state
2. Actions
3. Derivations
    - Computed properties
    - Reactions
  
![MobX Flow](https://mobx.js.org/docs/flow.png "MobX Flow")
  
Similar to react-redux, you use a `<Provider storeA={storeA} />` component that wraps a component (`<Application/>` for example) to provide shared state with components. The difference from react-redux is that more than one store is a valid approach. In order for nested components to then leverage a given store, an `@inject` decorator is used resulting in a `this.props.storeA` reference in the component. Where MobX shines is its use of Observable and decorators, namely:
- *Observable state* via `@observable` - wraps a value (literal, object, array, etc) in an Observable
- *Actions* via `@action` - explicit decoration of a function that modifies state
- *Computed properties* via `@computed` - explicit decoration of a function that computes a value from observables (observable of observables)
- *Reactions* via `@observer` - higher-order component pattern so a react component actually rerenders when an observable changes

## ngrx

- Store > Selector > ViewModel > View > Actions > Reducers > ∞

ngrx (redux in Angular) is a system of design patterns for one-way data-flow for SPA/RIA client apps. The system works like this:
1. A **store** is a client-side observable database, the app model, whose JSON data is initially queried from a server. It is the single source of truth for data.
2. Components then dependency inject this **store** and leverage a **selector** function to create their respective viewmodel. This viewmodel may identically reflect a **store**’s model, but instead likely combines disparate parts of the **store** to create a custom model (viewmodel). The view then accurately updates and reflects the subset of the **store**’s data.
3. The **store** may update during runtime. This is where an **action** is used. An **action** is dispatched to communicate that a **store** needs to be updated, most often as a result of a desireable viewmodel change. The view (and any other views) only update after the **store** has updated.
4. A **reducer** function consumes these **action**s and based on type, updates the **store** (or a subset of it) in an immutable way.
5. Since the **store** is observable, all the components update automatically.
