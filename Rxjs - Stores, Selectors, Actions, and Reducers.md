# Rxjs - Stores, Selectors, Actions, and Reducers

Rxjs is a system of design patterns for one-way data-flow for SPA/RIA client apps. The system works like this:
1. A *store* is a client-side observable database, the app model, whose JSON data is initially queried from a server. It is the single source of truth for data.
2. Components then dependency inject this *store* and leverage a *selector* function to create its viewmodel. This viewmodel may identically reflect a *store*’s model, but instead likely combines disparate parts of the *store* to create a custom model (viewmodel). The view then accurately updates and reflects the subset of the *store*’s data.
3. The *store* may update during runtime. This is where an *action* is used to properly update it so the proper components automatically update as well.
4. A *reducer* function consumes *action*s and based on type, updates the *store* (or a subset of it).
5. Since the *store* is observable, the components update automatically.
