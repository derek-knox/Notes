# MobX Source Code Summary

Derivation / Observable / Reaction (link to tweet TODO)

## Observable

The `@observable` decorator or `observable()` function is an alias to `createObservable()`. Within `createObservable()` the value passed in is checked against a set of utility functions to determine the proper observable subtype to create (object, array, map, or value). Internally each of these factory methods creates a `BaseAtom`. The BaseAtom is responsible for two things (from source code comments):

1. detect when they are being _used_ and report this (using reportObserved). This allows mobx to make the connection between running functions and the data they used
2. they should notify mobx whenever they have _changed_. This way mobx can re-run any functions (derivations) that are using this atom.

## Derivation

Autorun (and I'm assuming @observe) kicks off a `Reaction` object that `schedule()`s itself to first update the `globalState.pendingReactions` followed by a `runReactions()` call....
