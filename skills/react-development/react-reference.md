# React 19+ Reference Guide

Comprehensive reference for React 19.2+ changes, new APIs, best practices, and deprecated patterns.

> **Source documentation:** See `documentation/react-19-release.md` and `documentation/react-19-upgrade-guide.md` for full official docs.

---

## New Hooks & APIs

### `useActionState` (replaces `useFormState`)

Manages form submission state including pending, errors, and results.

```tsx
import { useActionState } from 'react';

function UpdateProfile() {
  const [error, submitAction, isPending] = useActionState(
    async (previousState: string | null, formData: FormData) => {
      const error = await updateProfile(formData);
      if (error) return error;
      redirect('/profile');
      return null;
    },
    null
  );

  return (
    <form action={submitAction}>
      <input type="text" name="name" required />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Saving...' : 'Save'}
      </button>
      {error && <p className="text-red-500">{error}</p>}
    </form>
  );
}
```

> **Note:** `React.useActionState` was previously called `ReactDOM.useFormState` in Canary releases. `useFormState` is deprecated.

### `useFormStatus`

Reads the parent `<form>` status without prop drilling. Must be rendered inside a `<form>`.

```tsx
import { useFormStatus } from 'react-dom';

// Reusable submit button — reads parent form's pending state
function SubmitButton({ label = 'Submit' }: { label?: string }) {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Submitting...' : label}
    </button>
  );
}

// Usage — SubmitButton automatically knows the form state
function ContactForm() {
  return (
    <form action={submitContact}>
      <input name="email" type="email" required />
      <textarea name="message" required />
      <SubmitButton label="Send Message" />
    </form>
  );
}
```

### `useOptimistic`

Shows optimistic values while async operations are in progress.

```tsx
import { useOptimistic } from 'react';

function TodoList({ todos, onAdd }: { todos: Todo[]; onAdd: (text: string) => Promise<void> }) {
  const [optimisticTodos, addOptimistic] = useOptimistic(
    todos,
    (currentTodos: Todo[], newTodo: string) => [
      ...currentTodos,
      { id: crypto.randomUUID(), text: newTodo, pending: true }
    ]
  );

  const submitAction = async (formData: FormData) => {
    const text = formData.get('text') as string;
    addOptimistic(text);
    await onAdd(text);
  };

  return (
    <>
      <form action={submitAction}>
        <input name="text" required />
        <button type="submit">Add</button>
      </form>
      <ul>
        {optimisticTodos.map(todo => (
          <li key={todo.id} className={todo.pending ? 'opacity-50' : ''}>
            {todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

### `use` API

Read promises and context in render. Unlike hooks, `use` can be called conditionally.

```tsx
import { use, Suspense } from 'react';

// Reading a promise — component suspends until resolved
function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);
  return <h1>{user.name}</h1>;
}

// Reading context conditionally — NOT possible with useContext
function ConditionalTheme({ children }: { children?: React.ReactNode }) {
  if (!children) return null;

  // This works with `use` but would violate rules of hooks with `useContext`
  const theme = use(ThemeContext);
  return <div style={{ color: theme.color }}>{children}</div>;
}

// Usage with Suspense
function Page() {
  const userPromise = fetchUser();
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}
```

> **Important:** `use` does NOT support promises created in render. Pass promises from cached sources or frameworks.

### Actions (Async Transitions)

Functions that use async transitions are called "Actions". They manage pending, error, and optimistic states automatically.

```tsx
import { useTransition } from 'react';

function SaveButton({ onSave }: { onSave: () => Promise<void> }) {
  const [isPending, startTransition] = useTransition();

  const handleClick = () => {
    startTransition(async () => {
      await onSave();
    });
  };

  return (
    <button onClick={handleClick} disabled={isPending}>
      {isPending ? 'Saving...' : 'Save'}
    </button>
  );
}
```

### `<form>` Actions

Pass functions directly to `action`/`formAction` props. Forms auto-reset on success.

```tsx
// Form with action function — auto-resets on success
function NewsletterSignup() {
  const subscribe = async (formData: FormData) => {
    'use server';
    const email = formData.get('email') as string;
    await addSubscriber(email);
  };

  return (
    <form action={subscribe}>
      <input name="email" type="email" required />
      <button type="submit">Subscribe</button>
    </form>
  );
}
```

---

## Breaking Changes in Detail

### `ref` as a Prop (No More `forwardRef`)

```tsx
// ❌ DEPRECATED: forwardRef wrapper
const MyInput = forwardRef<HTMLInputElement, InputProps>(
  function MyInput({ placeholder }, ref) {
    return <input placeholder={placeholder} ref={ref} />;
  }
);

// ✅ CURRENT: ref is just a prop
interface InputProps {
  placeholder?: string;
  ref?: React.Ref<HTMLInputElement>;
}

function MyInput({ placeholder, ref }: InputProps) {
  return <input placeholder={placeholder} ref={ref} />;
}
```

### `<Context>` as Provider

```tsx
const ThemeContext = createContext('light');

// ❌ DEPRECATED: Context.Provider
function App({ children }: { children: React.ReactNode }) {
  return (
    <ThemeContext.Provider value="dark">
      {children}
    </ThemeContext.Provider>
  );
}

// ✅ CURRENT: Context directly
function App({ children }: { children: React.ReactNode }) {
  return (
    <ThemeContext value="dark">
      {children}
    </ThemeContext>
  );
}
```

### Ref Cleanup Functions

```tsx
// ❌ OLD: Implicit return (now rejected by TypeScript)
<div ref={current => (instance = current)} />

// ✅ CURRENT: Explicit block body with optional cleanup
<div ref={current => {
  instance = current;
  // Optional: return cleanup function
  return () => {
    instance = null;
  };
}} />
```

### `useDeferredValue` with Initial Value

```tsx
function SearchResults({ query }: { query: string }) {
  // New: second arg is initialValue (renders '' first, then deferred query)
  const deferredQuery = useDeferredValue(query, '');
  return <Results query={deferredQuery} />;
}
```

### `propTypes` and `defaultProps` Removed

```tsx
// ❌ REMOVED
function Heading({ text }) { return <h1>{text}</h1>; }
Heading.propTypes = { text: PropTypes.string };
Heading.defaultProps = { text: 'Hello!' };

// ✅ CURRENT: TypeScript + ES6 defaults
interface HeadingProps {
  text?: string;
}
function Heading({ text = 'Hello!' }: HeadingProps) {
  return <h1>{text}</h1>;
}
```

### `ReactDOM.render` → `createRoot`

```tsx
// ❌ REMOVED
import { render } from 'react-dom';
render(<App />, document.getElementById('root'));

// ✅ CURRENT
import { createRoot } from 'react-dom/client';
const root = createRoot(document.getElementById('root')!);
root.render(<App />);
```

### `act` Import Change

```tsx
// ❌ REMOVED
import { act } from 'react-dom/test-utils';

// ✅ CURRENT
import { act } from 'react';
```

### Error Handling Improvements

```tsx
const root = createRoot(container, {
  // Called when error is caught by Error Boundary
  onCaughtError: (error, errorInfo) => {
    logToService('caught', error, errorInfo);
  },
  // Called when error is NOT caught by any Error Boundary
  onUncaughtError: (error, errorInfo) => {
    logToService('uncaught', error, errorInfo);
  },
  // Called when React auto-recovers from an error
  onRecoverableError: (error, errorInfo) => {
    logToService('recoverable', error, errorInfo);
  },
});
```

---

## Best Practices

### Server vs Client Components

```tsx
// Server Component (default) — no directive needed
// ✅ Data fetching, database queries, secrets
async function ArticlePage({ params }: { params: { id: string } }) {
  const article = await getArticle(params.id);
  return (
    <article>
      <h1>{article.title}</h1>
      <ArticleContent content={article.content} />
      <LikeButton articleId={article.id} /> {/* Client component */}
    </article>
  );
}

// Client Component — needs directive
'use client';
// ✅ Hooks, event handlers, browser APIs
function LikeButton({ articleId }: { articleId: string }) {
  const [liked, setLiked] = useState(false);
  return (
    <button onClick={() => setLiked(prev => !prev)}>
      {liked ? '❤️' : '🤍'}
    </button>
  );
}
```

### Custom Hooks Pattern

```tsx
// Encapsulate reusable stateful logic
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// Encapsulate data fetching + mutations
function useArticle(id: string) {
  const [article, setArticle] = useState<Article | null>(null);
  const [isPending, startTransition] = useTransition();

  useEffect(() => {
    getArticle(id).then(setArticle);
  }, [id]);

  const update = (data: Partial<Article>) => {
    startTransition(async () => {
      const updated = await updateArticle(id, data);
      setArticle(updated);
    });
  };

  return { article, update, isPending };
}
```

### Error Boundaries

```tsx
'use client';

interface ErrorBoundaryProps {
  children: React.ReactNode;
  fallback: React.ReactNode;
}

interface ErrorBoundaryState {
  hasError: boolean;
  error: Error | null;
}

class ErrorBoundary extends React.Component<ErrorBoundaryProps, ErrorBoundaryState> {
  constructor(props: ErrorBoundaryProps) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error: Error): ErrorBoundaryState {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error('Error Boundary caught:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback;
    }
    return this.props.children;
  }
}
```

---

## Document Metadata (Native)

```tsx
// React 19 natively hoists <title>, <link>, <meta> to <head>
function ProductPage({ product }: { product: Product }) {
  return (
    <>
      <title>{product.name} | MyStore</title>
      <meta name="description" content={product.description} />
      <meta property="og:title" content={product.name} />
      <link rel="canonical" href={`/products/${product.slug}`} />

      <main>
        <h1>{product.name}</h1>
        <p>{product.description}</p>
      </main>
    </>
  );
}
```

---

## Resource Preloading APIs

```tsx
import { prefetchDNS, preconnect, preload, preinit } from 'react-dom';

function CriticalResources() {
  // Eagerly load and execute script
  preinit('https://cdn.example.com/analytics.js', { as: 'script' });

  // Preload font for faster rendering
  preload('https://cdn.example.com/inter.woff2', { as: 'font' });

  // Preload stylesheet
  preload('https://cdn.example.com/styles.css', { as: 'style' });

  // DNS prefetch for external services
  prefetchDNS('https://api.example.com');

  // Preconnect to reduce latency
  preconnect('https://images.example.com');

  return null;
}
```
