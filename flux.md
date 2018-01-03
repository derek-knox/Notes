# flux

*Flux* is an application architecture for one-way data flow that leverages four distinct parts:
1. Actions
2. Dispatchers
3. Stores
4. Views

The goal of the Flux architecture is to simplify application state management via:
- one-way data flow
- simple mental model

![Flux Diagram](https://facebook.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png "Flux Diagram")

## redux

*Redux* is an implementation of the Flux pattern that intentionally enforces a single vs multiple store approach

### Redux Principles
*via http://redux.js.org/docs/introduction/ThreePrinciples.html*
1. *Single source of truth* - The **state** of your whole application is stored in an object tree within a single **store**.
2. *State is read-only* - The only way to change the state is to emit an *action*, an object describing what happened.
3. *Changes are made with pure functions* - To specify how the **state** tree is transformed by **actions**, you write pure **reducer**s.

## ngrx

- Store > Selector > ViewModel > View > Actions > Reducers > ∞

ngrx (redux in Angular) is a system of design patterns for one-way data-flow for SPA/RIA client apps. The system works like this:
1. A **store** is a client-side observable database, the app model, whose JSON data is initially queried from a server. It is the single source of truth for data.
2. Components then dependency inject this **store** and leverage a **selector** function to create their respective viewmodel. This viewmodel may identically reflect a **store**’s model, but instead likely combines disparate parts of the **store** to create a custom model (viewmodel). The view then accurately updates and reflects the subset of the **store**’s data.
3. The **store** may update during runtime. This is where an **action** is used. An **action** is dispatched to communicate that a **store** needs to be updated, most often as a result of a desireable viewmodel change. The view (and any other views) only update after the **store** has updated.
4. A **reducer** function consumes these **action**s and based on type, updates the **store** (or a subset of it) in an immutable way.
5. Since the **store** is observable, all the components update automatically.
