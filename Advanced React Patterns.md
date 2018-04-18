# Compound Component

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
