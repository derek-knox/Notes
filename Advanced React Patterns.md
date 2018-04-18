# Compound Component

An approach for sharing state between 2+ components where the user of the component does not need to know about that internal state

- leverages `static` properties (`On`, `Off`, and `Button` below) for rendering sub (or compound) components of the root component
- the root component's `Render` function leverages `React.Children.map` and `React.CloneElement` to provide the root component state to its sub component children (for conditional rendering)
- example:
```
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
```
<Toggle on={onToggle}>
  <Toggle.On>The button is on</Toggle.On>
  <Toggle.Off>The button is off</Toggle.Off>
  <div>
    <Toggle.Button></Toggle.Button>
  </div>
</Toggle>
```
