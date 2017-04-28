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
  - "In a case where a state rule is made for a specific module, the state class name should include the module name in it. The state rule should also reside with the module rules and not with the rest of the global state rules."
  - state augments and overrides all other styles (so `!important` is valid for states but use sparingly if at all)
    1. State styles can apply to layout and/or module styles
    2. State styles indicate a JavaScript dependency.
        1. JavaScript (client js - user interaction or timing)
        2. psudo-class (browser js - built-in)
        3. media query (browser js - built-in)
  - "With SMACSS, the intent is to keep the styles that pertain to a specific module with the rest of the module. That means that instead of having a single break point, either in a main CSS file or in a separate media query style sheet, place media queries around the module states" - if using a preprocessor use Nesting
    ```
    .tab {
      background-color: purple;
      color: white;
    }
    .is-tab-active {
      background-color: white;
      color: black;
    }
    ```

### Theme
  - "are similar to state rules in that they describe how modules or layouts might look. Most sites don’t require a layer of theming but it is good to be aware of it"
    ```
    /* in module-name.css */
    .mod {
      border: 1px solid;
    }
    /* in theme.css */
    .mod {
      border-color: blue;
    }
    ```
  - "For more extensive theming, using a `theme-` prefix for specific theme components will make it easier to apply them to more elements on the page"
    ```
    /* in theme.css */
    .theme-border {
      border-color: purple;
    }
    .theme-background {
      background: linear-gradient( ... );
    }
    ```
    
## General Notes

- Be conscious of the depth selectors are applied, keep this minimal. Ideally, leverage modules and module sub-classes to keep things lighter and more open to being reused elsewhere in the page/app as opposed to having a long chain.
- always increase semantics and decrease reliance on specific HTML
- File guidelines:
  - Place all Base rules into their own file.
  - Depending on the type of layouts you have, either place all of them into a single file or major layouts into separate files.
  - Put each module into its own file.
  - Depending on size of project, place sub-modules into their own file.
  - Place global states into their own file.
  - Place layout and module states, including media queries that affect those layouts and modules, into the module files.
- It is preferred to make a module for certain base elements when they serve a specific purpose (`<table>`, `<button>`, `<input>`) as opposed to being defined in the base file
- CSS property order
  1. Box
  2. Border
  3. Background
  4. Text
  5. Other
