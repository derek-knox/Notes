# SMACSS by Jonathan Snook

## Categorization
### Base
  - "almost exclusively single element selectors but it could include attribute selectors, pseudo-class selectors, child selectors or sibling selectors"
  - element selectors
### Layout
  - "divide the page into sections. Layouts hold one or more modules together"
  - header, footer, sidebar, etc (core layout pieces often prefixed with `l-header` or `layout-header`)
### Module
  - "are the reusable, modular parts of our design"
  - class selectors lacking prefix (no ids or element selectors)
    - heavily prefer child class selectors vs child element selectors
      ```
      <div class="fld">
        <span class="fld-name">Folder Name</span>
        <span class="fld-items">(32 items)</span>
      </div>
      ```
    - *"Only include a selector that includes semantics. A span or div holds none. A heading has some. A class defined on an element has plenty."*
    - "If you are changing the look of a module for usage elsewhere on the page or site, sub-class the module instead"
      ```
      .pod {
        width: 100%;
      }
      .pod input[type=text] {
        width: 50%;
      }
      .pod-constrained input[type=text] {
        width: 100%;
      }
      ```
  - exist within layout elements or other modules
### State
  - "ways to describe how our modules or layouts will look when in a particular state. Is it hidden or expanded? Is it ac- tive or inactive? They are about describing how a module or layout looks on screens that are smaller or bigger. They are also about de- scribing how a module might look in di erent views like the home page or the inside page"
  - state augments and overrides all other styles (so `!important` is valid for states)
    1. State styles can apply to layout and/or module styles
    2. State styles indicate a JavaScript dependency.
### Theme
  - "are similar to state rules in that they describe how modules or layouts might look. Most sites don’t require a layer of theming but it is good to be aware of it"
