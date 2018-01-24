# MobX Source Code Summary

An interactive application relies on two core aspects:
1. state
2. state changes

MobX is a state management library that simplifies interactive application programming and maintenance while increasing developer productivity. MobX works based upon three fundamental concepts (link to tweet TODO):
1. Derivations - derive state
2. Observables - track state
3. Reactions - change state

## Derivation

A derivation is anything that can be derived from the state in a pure manner. The state is fundamentally composed of all the `Observable`s (`BaseAtom`s under the hood) being tracked in a program. `Observable`s are *observed* and cause *reactions* via:
1. `autorun` and `@observe`
    - Use `autorun` if you have a function that should run automatically but that doesn't result in a new value (side effect of rerendering for example).
2. `@computed`
    - Use `computed` for everything else (deriving new values from recently changed values).

`autorun` kicks off a `Reaction` object that `schedule()`s itself to first update the `globalState.pendingReactions` followed by a `runReactions()` call.

## Observable

The `@observable` decorator or `observable()` function is an alias for `createObservable()`. Within `createObservable()` the value passed in is checked against a set of utility functions to determine the proper observable subtype to create (object, array, map, or value). Internally each of these factory methods creates a `BaseAtom`. The BaseAtom is responsible for two things (from source code comments):

1. detect when they are being _used_ and report this (using `reportObserved`). This allows mobx to make the connection between running functions and the data they used
2. they should notify mobx whenever they have _changed_. This way mobx can re-run any functions (derivations) that are using this atom.
