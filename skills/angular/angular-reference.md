# Angular Reference

This document contains targeted Angular 20+ and 21+ guidance, migration pointers, and important patterns.

## Angular 20+ migration highlights

### Router and providers

- Use `provideRouter([...])` inside `bootstrapApplication()` or `AppModule`.
- If you need a route provider token collection, use `ROUTES` as a multi-token.
- Avoid `provideRoutes()`; it is removed in Angular 22+.

### HttpClient updates

- Prefer `HttpClient` with typed request/response pairs.
- Use fetch-like options when available, such as `referrerPolicy`, `integrity`, and `timeout` support.
- Always `catch` and normalize HTTP errors before throwing.

### Template improvements

- `NgTemplateOutlet` now accepts `undefined` for optional outlet content.
- Avoid deprecated template constructs and rely on strict template type checking.

### Signals and forms

- `signal()` and signal-based forms are more stable in Angular 20/21.
- `Effect` timing changed in Angular 19/20; avoid writing effects with side effects outside initialization.
- Use `SignalFormsConfig` as readonly where supported.

## Angular 21+ important updates

### Diagnostic and type behavior

- Host binding type checking is now stricter by default.
- Angular provides better diagnostics for misconfigured `@defer` triggers.
- `SimpleChanges` can be generic in newer versions.

### Experimental APIs

- Experimental `Navigation API` support is available in Angular 21.
- `KeyValuePipe` optional keys support improves template typing.
- `NgComponentOutlet` and dynamic components get better `Input` binding support.

### Runtime and zone changes

- Angular is moving toward optional zone.js and zone-less internal behavior.
- When targeting Angular 22+, Node 20 is no longer supported; use Node 22.22.0 or later.

## App structure patterns

### Core / Shared / Features

- `core/` for app-wide services, interceptors, guards, and configuration.
- `shared/` for reusable UI primitives, pipes, directives, and models.
- `features/` for domain feature folders, each with its own component, page, template, and service.

### Type organization

- Shared domain types: `src/app/types/` or `src/app/shared/types/`.
- Component-specific interfaces can live next to their component files only if they are not reused.
- Avoid large `types.ts` catch-all files; keep types grouped by domain.

### Example folder layout

```
src/app/
  app.component.ts
  app.component.html
  app.component.css
  app.config.ts
  core/
    auth.service.ts
    http.interceptor.ts
  features/
    dashboard/
      dashboard.component.ts
      dashboard.component.html
      dashboard.component.css
      dashboard.service.ts
    articles/
      article-list.component.ts
      article-item.component.ts
      article.service.ts
  shared/
    ui/
      button.component.ts
      card.component.ts
    models/
      user.ts
      article.ts
```

## DI and service design

### OOP service classes

- Create service classes with injected dependencies and explicit public methods.
- Avoid sprawling utility objects with function properties.

Example:

```typescript
@Injectable({ providedIn: 'root' })
export class ArticleService {
  constructor(private readonly http: HttpClient) {}

  async fetchArticle(id: string): Promise<Article> {
    try {
      return await this.http.get<Article>(`/api/articles/${id}`).toPromise();
    } catch (error) {
      throw new Error('Unable to fetch article');
    }
  }
}
```

### Error handling

- Use `try/catch` around `await` operations.
- Throw specific errors when a service cannot recover.
- Let components decide how to render error state rather than swallowing errors in services.

## Template and style separation

### Recommended component style

- `component.ts` for logic
- `component.html` for markup
- `component.css` / `component.scss` for styles
- Keep template expressions simple and type-safe

### Best practice for templates

- Avoid long inline expressions.
- Prefer `trackBy` functions in `ngFor` when rendering lists.
- Keep event handler methods on the component class.

## Important deprecations and replacements

- `provideRoutes()` -> `provideRouter()` / `ROUTES`
- `ChangeDetectorRef.checkNoChanges()` removed in Angular 22+.
- `getAngularLib` / `setAngularLib` -> `getAngularJSGlobal` / `setAngularJSGlobal`
- Avoid deprecated global migration APIs and older `@angular/core` legacy helpers.
