# Angular 19 / 20 / 21 Changelog Reference

This file stores curated release notes from Angular 19, 20, and 21 to support the Angular skill.

## Angular 21 highlights

### 21.0.0

- Experimental support for the Navigation API.
- Optional keys support for `KeyValuePipe` in templates.
- Updated CLDR data to v47.
- Diagnostic added for misconfigured `@defer` triggers.
- Host bindings type checking enabled by default.
- Experimental signal forms API and `debounce()` form rule support.
- Added `FormArrayDirective` for more declarative form control arrays.
- Deprecated `ApplicationConfig` export in migration tooling.
- Router improvements: `lastSuccessfulNavigation` converted to a signal; memory leak fixes for `Navigation.abort` and `canDeactivate` guards.
- `ngOnChanges`/view transition and `AnimationCallbackEvent.animationComplete` signature changes.
- HTTP improvements: `referrerPolicy`, `integrity`, `timeout`, `transferCache`, and better fetch-style options.

### 21.0.7 / 21.0.9 / 21.1.x / 21.2.x

- Fixes for host bindings, SVG sanitization, animation state leaks, and `signal` form metadata retention.
- `ChangeDetectorRef.checkNoChanges` removal referenced in Angular 22+ preview notes.
- Language service and compiler fixes for better type hints and template diagnostics.

## Angular 20 highlights

### 20.0.0

- `NgTemplateOutlet` accepts `undefined` and optional outlet content.
- `ViewportScroller` now supports `ScrollOptions`.
- Angular core added stricter date validation and improved error handling for suspicious date patterns.
- `afterRender` renamed to `afterEveryRender` and incremental hydration APIs were stabilized.
- `HttpClient` and resource APIs improved for fetch-like options and upload progress support.
- Dynamically-created components can now listen to output events.
- Angular Node support updated: Node 20.19, 22.12, 24.0.
- Improved test tag inference and dynamic component test support.

### 20.1.0 / 20.2.0

- Parser support for new binary assignment operators and more permissive `@` parsing.
- Animation instruction support for `enter` and `leave` semantics.
- Router / HTTP improvements, including `referrerPolicy` and transfer cache options.
- Diagnostics for uninvoked interpolation functions and improved `HttpClient` fetch support.

## Angular 19 highlights

### 19.0.0

- Experimental `resource()` API introduced for async dependencies.
- Early `rxResource()` implementation and stronger signal-first runtime support.
- `standalone` default behavior flipped toward standalone-first development.
- Support for `event listener options` in renderers.
- Utility for defer block introspection added to the global `ng` object.
- Stronger migration tooling for `RouterModule.forRoot` and standalone bootstrap.

### 19.1.0 / 19.2.0

- Improvements to `signal` diagnostics and default form support.
- Better router lazy-loading behavior and default route event support.
- Fixes for `NgOptimizedImage` warnings, event replay, and application destroy lifecycle.
- Typed `Route` and router event metadata improvements.

## Angular 22 preview note

- Angular 22 preview removed Node 20 support and requires Node 22.22.0 or higher.
- `provideRoutes()` is removed; use `provideRouter()` or `ROUTES` with `provideRouter()`.
- `ChangeDetectorRef.checkNoChanges()` is no longer available.
- `getAngularLib` / `setAngularLib` are removed in favor of `getAngularJSGlobal` / `setAngularJSGlobal`.

## Sources

- [Angular changelog raw](https://raw.githubusercontent.com/angular/angular/main/CHANGELOG.md)

> The above content is distilled from Angular release notes and changelog entries for versions 19 through 21.
