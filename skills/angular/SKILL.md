---
name: angular
description: Use when writing Angular 21+ applications with modern, modular architecture, strict TypeScript, and OOP-driven design.
---

# Angular Skill

Reference skill for building quality Angular 21+ applications. This skill emphasizes strict TypeScript, separate view/style/code files, small focused components, reusable shared interfaces, and Angular 20+ migration-aware patterns.

## Sub-files for detail

- `angular-reference.md` — Angular 20+/21+ specific architecture, migration guidance, and modern DI patterns
- `angular-good-bad.md` — concrete good/bad examples for components, services, templates, and error handling
- `documentation/angular-19-20-21-changelog.md` — curated Angular 19/20/21 release notes and change summaries

---

## Key best practices

- Prefer **standalone components** and `ApplicationConfig`-friendly bootstrap when possible.
- Keep components, templates, and styles separate: `.ts`, `.html`, `.css` / `.scss`.
- Use **small focused components** and services. Aim for files under 300 lines.
- Organize shared types in `src/app/types/` or `src/app/shared/types/`, not inline.
- Use `async/await` for asynchronous code. Do not use callbacks or unawaited promises.
- No `any` types. Use explicit named interfaces and type aliases.
- Avoid `as` type assertions. Prefer properly typed variables.
- Prefer **types/interfaces** over enums for generic constant sets.
- Use OOP-friendly service classes for business logic and data access.
- Handle all errors gracefully and surface recoverable failures.
- Favor method calls and explicit initialization over nested assignments.

---

## Angular 21+ and modern Angular changes

### What changed in Angular 20+ / 21+

- Signal-based forms are now more stable and include features like `debounce()` rules and experimental form support.
- Host binding type-checking defaults are stronger.
- `provideRoutes()` is removed; use `provideRouter()` or multi-token `ROUTES` migration.
- Angular core embraced more runtime diagnostics and stricter `ngOnChanges`/host binding behavior.
- New API-level improvements for router data, lazy-loading, and `Navigation`.
- Angular 22+ preview adds Node 22/24 minimum support and removes Node 20.

### Version-specific migration notes

- Angular 21: experimental `Navigation API`, optional keys for `KeyValuePipe`, signal forms improvements, stricter host binding types.
- Angular 20: `NgTemplateOutlet` accepts `undefined`, `ViewportScroller` supports `ScrollOptions`, `HttpClient` gets better fetch-like options, incremental hydration and animation instruction support.
- Angular 19: early `resource()` and `rxResource()` experimentation, stronger reactive signal support, and standalone-first library movement.

---

## File structure best practices

Use a layered Angular app structure that separates app shell, feature modules, shared UI, utilities, and types.

Example file layout:

- `src/app/app.component.ts`
- `src/app/app.module.ts` or `src/app/app.config.ts`
- `src/app/core/` — services, guards, interceptors, providers
- `src/app/features/` — feature folders with component, model, service, and route files
- `src/app/shared/` — reusable UI components, pipes, directives
- `src/app/types/` — shared interfaces and type aliases
- `src/app/utils/` — pure helpers and validation utilities

Use `shared/` for generic UI primitives and `core/` for app-wide services.

---

## Modular design patterns

### Separation of concerns

- Components render UI and delegate business logic to services.
- Services handle API access, state, and domain rules.
- Type files contain shared interfaces and data contracts.
- Helpers remain pure functions and avoid side effects.

### OOP-friendly patterns

Use service classes to encapsulate behavior and state:

```typescript
export class UserService {
  constructor(private readonly http: HttpClient) {}

  async getUser(userId: string): Promise<User> {
    try {
      const user = await this.http.get<User>(`/api/users/${userId}`).toPromise();
      return user;
    } catch (error) {
      throw new Error('Failed to load user');
    }
  }
}
```

Prefer class-based service methods over ad-hoc utility objects.

---

## General quality criteria

- Use strict typing everywhere.
- Prefer a single responsibility per component/service.
- Avoid deeply nested JSX-style or object assignment structures in templates and code.
- Keep reusable logic outside the component class.
- Favor `readonly`, `private`, and explicit access modifiers.
- Use error classes or typed responses for recoverable failures.

---

## Recommended patterns

- `const model: Post = response;` instead of `const model = response as Post`.
- `this.myService.load().then(...)` should become `await this.myService.load();` in async methods.
- Use type-safe Angular forms and `FormGroup` with `readonly` typed controls.

---

## Deprecated patterns to avoid

- Inline component templates/styles for feature components.
- `any`, `unknown` with silenced type assertions.
- `provideRoutes()` in router setup.
- `ChangeDetectorRef.checkNoChanges()` in Angular 22+.
- Old AngularJS-style global APIs and migration helpers.

---

## Use this skill when

- Writing Angular features with strict TypeScript.
- Migrating Angular 19/20 projects toward Angular 21+.
- Designing reusable component libraries and shared services.
- Enforcing separation of templates, styles, code, and types.

> This skill is best paired with Angular release notes from version 19–21 and modern Angular architecture guidance.
