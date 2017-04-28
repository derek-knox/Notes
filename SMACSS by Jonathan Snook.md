# SMACSS by Jonathan Snook

Categorization
1. Base
  - "almost exclusively single ele- ment selectors but it could include attribute selectors, pseudo-class selectors, child selectors or sibling selectors"
  - element selectors
2. Layout
  - "divide the page into sections. Layouts hold one or more modules together"
  - header, footer, sidebar, etc (core layout pieces often prefixed with `l-header` or `layout-header`)
3. Module
  - "are the reusable, modular parts of our design"
  - class selectors lacking prefix (no ids or element selectors)
    - heavily prefer child class selectors vs child element selectors
    - *"Only include a selector that includes semantics. A span or div holds none. A heading has some. A class de ned on an element has plenty."*
  - exist within layout elements or other modules
4. State
  - "ways to describe how our modules or layouts will look when in a particular state. Is it hidden or expanded? Is it ac- tive or inactive? They are about describing how a module or layout looks on screens that are smaller or bigger. They are also about de- scribing how a module might look in di erent views like the home page or the inside page"
5. Theme
  - "are similar to state rules in that they describe how modules or layouts might look. Most sites don’t require a layer of theming but it is good to be aware of it"

  
