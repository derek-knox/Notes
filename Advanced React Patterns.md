# Compound Component

An approach for sharing state between 2+ components where the user of the component does not need to know about that internal state

- leverages `static` properties (`On`, `Off`, and `Button` below) for rendering sub (or compound) components of the root component
- the root component's `Render` function leverages `React.Children.map` and `React.CloneElement` to provide the root component state to its sub component children (for conditional rendering)
- example:
```jsx
<Toggle on={onToggle}>
  <Toggle.On>The button is on</Toggle.On>
  <Toggle.Off>The button is off</Toggle.Off>
  <Toggle.Button></Toggle.Button>
</Toggle>
```

# Flexible Compound Component

- a variation on the Compound Component pattern where React's context API is leveraged so all sub components do not need to be adjacent
- static property sub components are wrapped (`<ToggleContext.Consumer>...</ToggleContext.Consumer>`)
- the root component is now wrapped (`<ToggleContext.Provider>...</ToggleContext.Provider>`)
- example:
```jsx
<Toggle on={onToggle}>
  <Toggle.On>The button is on</Toggle.On>
  <Toggle.Off>The button is off</Toggle.Off>
  <div>
    <Toggle.Button></Toggle.Button>
  </div>
</Toggle>
```

# Render Prop Component

- an approach for giving the user of a component rendering control
- a technique for sharing code between React components using a prop whose value is a function
- a render prop is a function prop that a component uses to know what to render

```jsx
<Toggle onToggle={onToggle}>
  {({on, toggle}) => (
    <div>
      {on ? 'The button is on' : 'The button is off'}
      <Switch on={on} onClick={toggle} />
      <hr/>
      <button aria-label='custom-button' onClick={toggle}>
        {on ? 'On' : 'Off'}
      </button>
    </div>
  )}
</Toggle>
```

- Since the `children` prop (sometimes used as `render`) is implied, its value can instead be placed directly as a child (like the example above). This is preferred to placing the function as the value to a `children` or `render` property.
- The *Prop Getters* pattern is useful with Render Props to enable multiple functions to be called for the same action (as opposed to getting overridden). For example an `onClick` could be used internally to the component but also for the user of the component.

# State Initializer

- an approach for initialiing state but also providing a mechanism for restting a component to its initial state
  - simply cache an `initialState` object and provide a way to trigger restting the state to `initialState`
  - when first initializing, simply leverage the passed in initialState props but fallback to the `initialState` for values not passed in

# Provider 

- an approach for mitigating "prop drilling" (passing props multiple levels just so a deep component can consume shared state) that leverages a context (shared state), provider (wrapper component), and consumer (any nested component desiring to consume the context shared state)

# HOC (Higher Order Component)

- a function that takes arguments (object, component, etc.) and then returns a new component using the arguments (Render Props are used in the newly returned component)
