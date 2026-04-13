---
name: react-development
description: Use when writing React 19+ components, hooks, or pages, or when writing Tailwind CSS v4+ styles, theming, or utility classes — covers current best practices, deprecated patterns, and migration from older versions
---

# React 19+ & Tailwind CSS v4+ Development

Reference skill for writing modern React and Tailwind code. Covers breaking changes, new APIs, deprecated patterns, best practices, modular design, and OOP patterns.

**Sub-files for detailed reference:**

- `react-reference.md` — React 19+ specific changes, hooks, patterns, and deprecations
- `tailwind-reference.md` — Tailwind v4+ specific changes, theming, syntax, and migration
- `documentation/` — Full fetched documentation from official sources for searching

---

## React 19+ Key Changes (Summary)

### New Hooks & APIs

| API | Purpose |
| ----- | --------- |
| `useActionState` | Manages form action state (replaces `useFormState`) |
| `useFormStatus` | Reads parent `<form>` status without prop drilling |
| `useOptimistic` | Optimistic UI updates during async mutations |
| `use` | Read promises/context in render (can be called conditionally) |
| `Actions` | Async functions in transitions for pending/error/optimistic handling |

### Breaking Changes

- **`forwardRef` no longer needed** — `ref` is now a regular prop on function components
- **`<Context>` replaces `<Context.Provider>`** — render context directly as provider
- **Ref cleanup functions** — ref callbacks can return cleanup functions; implicit returns rejected by TS
- **`propTypes` + `defaultProps` removed** for function components — use TypeScript interfaces + ES6 defaults
- **`ReactDOM.render` removed** — use `createRoot` from `react-dom/client`
- **`ReactDOM.hydrate` removed** — use `hydrateRoot` from `react-dom/client`
- **String refs removed** — use ref callbacks or `useRef`
- **Legacy Context (`contextTypes`/`getChildContext`) removed** — use `createContext`
- **`act` moved** from `react-dom/test-utils` to `react`

### Server Components & Server Actions

- Server Components render ahead of time, outside the client bundle
- `"use server"` directive marks Server Actions (NOT Server Components)
- Server Actions can be passed as props to Client Components

### Document Metadata

- `<title>`, `<link>`, `<meta>` can be rendered anywhere in components — React hoists to `<head>`

---

## Tailwind CSS v4+ Key Changes (Summary)

### Architecture Overhaul

- **No `tailwind.config.ts`** — all configuration is CSS-native via `@theme` directive
- **No `@tailwind` directives** — use `@import "tailwindcss"` instead
- **No `postcss-import` or `autoprefixer` needed** — handled automatically
- **Vite plugin** (`@tailwindcss/vite`) replaces PostCSS plugin for Vite projects
- **CLI** moved to `@tailwindcss/cli` package

### CSS-Native Configuration

```css
/* v4: All config in CSS */
@import "tailwindcss";

@theme {
  --color-primary: oklch(0.7 0.15 250);
  --color-secondary: oklch(0.6 0.12 300);
  --font-display: "Inter", sans-serif;
  --breakpoint-3xl: 120rem;
  --spacing-18: 4.5rem;
}
```

### Renamed Utilities

| v3 | v4 |
| ---- | ----- |
| `shadow-sm` | `shadow-xs` |
| `shadow` | `shadow-sm` |
| `blur-sm` | `blur-xs` |
| `blur` | `blur-sm` |
| `drop-shadow-sm` | `drop-shadow-xs` |
| `drop-shadow` | `drop-shadow-sm` |
| `rounded-sm` | `rounded-xs` |
| `rounded` | `rounded-sm` |
| `outline-none` | `outline-hidden` (for hidden outlines; `outline-none` now sets `outline-style: none`) |

### Removed Deprecated Utilities

| Removed | Replacement |
| --------- | ------------- |
| `bg-opacity-*` | `bg-black/50` (color opacity modifier) |
| `text-opacity-*` | `text-black/50` |
| `border-opacity-*` | `border-black/50` |
| `ring-opacity-*` | `ring-black/50` |
| `flex-shrink-*` | `shrink-*` |
| `flex-grow-*` | `grow-*` |
| `overflow-ellipsis` | `text-ellipsis` |
| `decoration-slice` | `box-decoration-slice` |

---

## Best Practices & Code Patterns

### React Component Architecture

**Default to Server Components** — use `'use client'` only when hooks, event handlers, or browser APIs are needed.

**Component Structure:**

```diagram
app/
  layout.tsx          # Root layout (Server Component)
  page.tsx            # Page (Server Component)
  components/
    ui/               # Reusable UI primitives (Button, Card, Input)
    features/         # Feature-specific components
    layouts/          # Layout components (Sidebar, Header)
  hooks/              # Custom hooks
  lib/                # Utilities, helpers, constants
  types/              # TypeScript interfaces and types
```

**Modular Component Design:**

```tsx
// ✅ GOOD: Small, focused, typed components in small files (ideally fewer than 300 lines)
interface CardProps {
  title: string;
  description: string;
  actions?: React.ReactNode;
  variant?: 'default' | 'elevated' | 'outlined';
}

function Card({ title, description, actions, variant = 'default' }: CardProps) {
  return (
    <article className={cardVariants[variant]}>
      <CardHeader title={title} />
      <CardBody>{description}</CardBody>
      {actions && <CardFooter>{actions}</CardFooter>}
    </article>
  );
}
```

```tsx
// ❌ BAD: Large component in monolithic style with multiple responsibilities and `any` types
function LargeComponent({ data }: { data: any }) {
  const [state, setState] = useState<any>(null);

  useEffect(() => {
    // Fetch data
    fetchData().then(setState);
  }, []);

  const handleClick = () => {
    // Handle click
  };

  return (
    <div onClick={handleClick}>
      {state ? <p>{state}</p> : <p>Loading...</p>}
    </div>
  );
}
```


### OOP Patterns in React

**Service Classes** for business logic:

```tsx
// lib/services/ArticleService.ts
class ArticleService {
  private supabase: SupabaseClient;

  constructor(supabase: SupabaseClient) {
    this.supabase = supabase;
  }

  async getById(id: string): Promise<Article | null> {
    const { data, error } = await this.supabase
      .from('articles')
      .select('*')
      .eq('id', id)
      .single();
    if (error) throw new ServiceError('Failed to fetch article', error);
    return data;
  }

  async create(input: CreateArticleInput): Promise<Article> {
    const validated = ArticleSchema.parse(input);
    const { data, error } = await this.supabase
      .from('articles')
      .insert(validated)
      .select()
      .single();
    if (error) throw new ServiceError('Failed to create article', error);
    return data;
  }
}
```

**Abstract Base Classes** for shared behavior:

```tsx
// lib/services/BaseService.ts
abstract class BaseService<T> {
  protected supabase: SupabaseClient;
  protected abstract tableName: string;

  constructor(supabase: SupabaseClient) {
    this.supabase = supabase;
  }

  async findById(id: string): Promise<T | null> {
    const { data, error } = await this.supabase
      .from(this.tableName)
      .select('*')
      .eq('id', id)
      .single();
    if (error) throw new ServiceError(`Failed to find ${this.tableName}`, error);
    return data as T;
  }
}
```

**Strategy Pattern** for interchangeable behavior:

```tsx
// lib/strategies/ImageProvider.ts
interface ImageProvider {
  generate(prompt: string): Promise<ImageResult>;
  getName(): string;
}

class GoogleImagenProvider implements ImageProvider {
  async generate(prompt: string): Promise<ImageResult> { /* ... */ }
  getName() { return 'google-imagen'; }
}

class LocalLLMProvider implements ImageProvider {
  async generate(prompt: string): Promise<ImageResult> { /* ... */ }
  getName() { return 'local-llm'; }
}

// Usage in component/hook
function useImageGeneration(provider: ImageProvider) {
  const [result, setResult] = useState<ImageResult | null>(null);
  const generate = async (prompt: string) => {
    setResult(await provider.generate(prompt));
  };
  return { result, generate };
}
```

### Deprecated Code → Modern Replacement Quick Reference

See `react-reference.md` for full React examples and `tailwind-reference.md` for full Tailwind examples.

| Deprecated | Modern Replacement |
| ------------ | ------------------- |
| `forwardRef(Component)` | `function Component({ ref })` |
| `<Context.Provider value={v}>` | `<Context value={v}>` |
| `useFormState` | `useActionState` |
| `ReactDOM.render(<App />, el)` | `createRoot(el).render(<App />)` |
| `Heading.defaultProps = {...}` | `function Heading({ text = 'hi' })` |
| `Heading.propTypes = {...}` | TypeScript interface |
| `import {act} from 'react-dom/test-utils'` | `import {act} from 'react'` |
| `tailwind.config.ts` | `@theme { }` in CSS |
| `@tailwind base/components/utilities` | `@import "tailwindcss"` |
| `bg-opacity-50` | `bg-black/50` |
| `shadow-sm` → `shadow` | `shadow-xs` → `shadow-sm` (shifted down) |

---

## Common Mistakes

| Mistake | Fix |
| --------- | ----- |
| Using `forwardRef` in new code | Just accept `ref` as a prop |
| Creating `tailwind.config.ts` | Use `@theme` directive in CSS |
| Using `@tailwind base` | Use `@import "tailwindcss"` |
| Using `bg-opacity-*` utilities | Use color opacity modifier: `bg-red-500/50` |
| Using `any` type in TypeScript | Define proper interfaces |
| Wrapping everything in `'use client'` | Default to Server Components |
| Using `useState` + `useEffect` for data | Use Server Components or `use()` with Suspense |
| Using `shadow-sm` expecting small shadow | It's now medium — use `shadow-xs` for small |
| Manual `isPending` state for forms | Use `useActionState` or `useTransition` |
| `useContext` behind conditional return | Use `use(Context)` — works after early returns |

---

## Quick Reference Links

- [React 19 Release](https://react.dev/blog/2024/12/05/react-19)
- [React 19 Upgrade Guide](https://react.dev/blog/2024/04/25/react-19-upgrade-guide)
- [Tailwind v4 Upgrade Guide](https://tailwindcss.com/docs/upgrade-guide)
- [Tailwind v4 Theme Variables](https://tailwindcss.com/docs/theme)
- [Tailwind v4 Custom Styles](https://tailwindcss.com/docs/adding-custom-styles)
- [Tailwind v4 Dark Mode](https://tailwindcss.com/docs/dark-mode)
