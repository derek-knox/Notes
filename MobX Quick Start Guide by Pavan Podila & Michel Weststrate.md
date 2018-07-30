# MobX Quick Start Guide by Pavan Podila & Michel Weststrate

## Introduction to State Management

MobX is a reactive uni-directional state management library with an elegant and small API.
- UI = `fn(state)`
- VirtualDOM = `fn(props, state)`

State Changes
- State > UI > Actions > (repeat)
- State > Side Effect > Actions > (repeat)

MobX Use
- `observable` - function that wraps any data and automatically wires up change tracking on its properties
- `observer` - a listener of `observable` changes (aka *reactions*)
  1. `autorun` - an API accepting a *tracking function* that autoruns when any of its nested `observable`s changes
  1. `reaction` - 
  1. `when` - 
- `actions` - a function wrapper for `observable` mutations that's transactional (`observer`s are notified when the function completes as more than one `observable` may update within it)
