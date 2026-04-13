---
name: typescript
description: 'Write production-grade TypeScript code. Use for TypeScript 5.9+ projects — covers strict mode practices, modular patterns, type safety, OOP designs, and TypeScript 6.0+ breaking changes. Enforce no `any` types, proper error handling, and modern async/await patterns.'
---

# TypeScript Development

A comprehensive guide to writing modern, maintainable TypeScript code with strict type safety, modular design patterns, and current best practices. This skill ensures type-safe implementations that leverage TypeScript's full potential while avoiding common pitfalls.

## When to Use

- Writing new TypeScript modules, utilities, or components
- Designing interfaces and type hierarchies for reusable code
- Implementing OOP patterns (classes, inheritance, composition)
- Migrating code to TypeScript 6.0+ (breaking changes + deprecations)
- Refactoring existing code to eliminate type errors and improve maintainability
- Setting up `tsconfig.json` for new projects
- Designing APIs with proper error handling and async patterns

## Core Principles

### Type Safety: No Compromises

- **No `any` types**: Ever. Period. Use proper types, union types, generics, or `unknown` with type narrowing.
- **No `as` assertions**: Types must be structurally correct without assertions. Example: `const user: User = userData;` (not `as User`).
- **Explicit types**: Provide explicit type annotations for function parameters and return types. Avoid relying solely on inference for public APIs.
- **Inline types only for simple cases**: Simple function signatures or object literals can use inline types. Complex or reused types must be separate interfaces/type aliases.

### Code Quality Standards

- **Strict mode**: Always enable TypeScript strict mode and all related compiler options.
- **2-space indentation**: Consistent formatting across all files.
- **Error handling**: No silent failures. All errors must be caught, logged, or propagated thoughtfully.
- **Async/await**: Use async/await, never callbacks or promise chains without proper context.
- **Comments on "why"**: Code shows *what* happens; comments explain *why*.
- **Modular design**: Separate concerns, avoid monolithic files; keep components focused.

### Type Organization

- **Shared types in separate files**: Define shared interfaces/types in dedicated type files or in the file that instantiates them.
- **Prefer types/interfaces over enums**: For constants, statuses, and fixed sets, use `type` with `as const` or `interface`.
- **Descriptive names**: Type names should clearly indicate their purpose (`UserResponse`, `PaginatedResult`, `CacheConfig`).

## Project Structure

### Recommended Layout

```diagram
src/
├── types/                    # Shared type definitions
│   ├── index.ts             # Export all types
│   ├── user.ts              # User-related types
│   └── api.ts               # API response types
├── lib/                      # Utility functions and helpers
│   ├── api.ts               # API client utilities
│   ├── validation.ts        # Input validation
│   └── logger.ts            # Logging utilities
├── services/                 # Business logic services
│   ├── userService.ts       # User business logic
│   └── authService.ts       # Authentication logic
├── components/              # React components (if applicable)
├── hooks/                    # React hooks (if applicable)
└── config/                   # Configuration files
```

### Naming Conventions

| Entity | Pattern | Example |
|--------|---------|---------|
| Type/Interface | PascalCase | `UserProfile`, `ApiResponse` |
| Variable/Function | camelCase | `getUserData()`, `isActive` |
| Constant | UPPER_SNAKE_CASE | `MAX_RETRIES`, `DEFAULT_TIMEOUT` |
| File (type) | kebab-case.ts | `user-profile.ts` |
| File (function/utility) | kebab-case.ts | `api-client.ts` |
| File (component) | PascalCase.tsx | `UserCard.tsx` |

## Key Development Patterns

### Type Hierarchies and Interfaces

**Good: Clear, composable interface hierarchy**

```typescript
// types/base.ts
interface BaseEntity {
  id: string;
  createdAt: Date;
  updatedAt: Date;
}

interface Timestamps {
  createdAt: Date;
  updatedAt: Date;
}

// types/user.ts
interface User extends BaseEntity {
  name: string;
  email: string;
  role: UserRole;
  isActive: boolean;
}

type UserRole = 'admin' | 'user' | 'guest';

// ❌ Avoid: Inline types when they're reused
function getUser(id: string): { id: string; name: string; email: string } {
  // ...
}

// ✅ Good: Extract to named type
function getUser(id: string): User {
  // ...
}
```

### Async/Await and Error Handling

**Good: Proper error handling with async/await**

```typescript
// ✅ Good
async function fetchUserData(userId: string): Promise<User> {
  try {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: Failed to fetch user`);
    }
    const data = await response.json();
    return User.parse(data); // Validate against schema
  } catch (error) {
    logger.error('Failed to fetch user', { userId, error });
    throw new Error('Unable to retrieve user data');
  }
}

// ❌ Avoid: Silent failures or bare promises
async function fetchUserData(userId: string): Promise<User> {
  const response = await fetch(`/api/users/${userId}`);
  const data = await response.json(); // No error handling!
  return data;
}

// ❌ Avoid: Callbacks
function fetchUserData(userId: string, callback: (err: Error | null, data?: User) => void) {
  fetch(`/api/users/${userId}`)
    .then(r => r.json())
    .then(data => callback(null, data))
    .catch(err => callback(err));
}
```

### Union Types and Type Narrowing

**Good: Prefer discriminated unions over broad unions**

```typescript
// ✅ Good: Discriminated union (type-safe narrowing)
type Result<T> = 
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error }
  | { status: 'pending' };

function processResult<T>(result: Result<T>): void {
  if (result.status === 'success') {
    console.log(result.data); // TypeScript knows data exists here
  } else if (result.status === 'error') {
    console.error(result.error);
  }
}

// ❌ Avoid: Union without discrimination
type Result<T> = T | Error | null; // Unclear, hard to narrow
```

### Generic Functions and Type Parameters

**Good: Constrained generics with explicit boundaries**

```typescript
// ✅ Good: Generic with meaningful constraints
interface Identifiable {
  id: string;
}

function createMap<T extends Identifiable>(items: T[]): Map<string, T> {
  const map = new Map<string, T>();
  items.forEach(item => map.set(item.id, item));
  return map;
}

// ✅ Good: Generic with default type parameter
function paginate<T = unknown>(items: T[], page: number, size: number): T[] {
  const start = (page - 1) * size;
  return items.slice(start, start + size);
}

// ❌ Avoid: Unbounded generics or using `any`
function createMap<T>(items: T[]): Map<any, T> { // Bad: any in key type
  // ...
}

function paginate(items: any[], page: number, size: number): any[] {
  // ...
}
```

### OOP: Classes and Inheritance

**Good: Proper class design with clear responsibilities**

```typescript
// ✅ Good: Encapsulation with private fields and methods
abstract class Service {
  protected logger: Logger;
  private cache: Map<string, unknown>;

  constructor(logger: Logger) {
    this.logger = logger;
    this.cache = new Map();
  }

  protected getFromCache<T>(key: string): T | undefined {
    return this.cache.get(key) as T | undefined;
  }

  protected setCache<T>(key: string, value: T): void {
    this.cache.set(key, value);
  }

  abstract execute(): Promise<void>;
}

// ✅ Good: Composition over inheritance where appropriate
interface UserRepo {
  find(id: string): Promise<User>;
  save(user: User): Promise<void>;
}

class UserService {
  constructor(private repo: UserRepo, private logger: Logger) {}

  async getUser(id: string): Promise<User> {
    const user = await this.repo.find(id);
    if (!user) {
      throw new Error(`User ${id} not found`);
    }
    return user;
  }
}

// ❌ Avoid: Over-inheritance hierarchies
class BaseService extends Logger {}
class UserService extends BaseService {} // Conflates concerns
```

### Immutability and Constants

**Good: Use `as const` for literal types**

```typescript
// ✅ Good: Strongly typed constants
const ROLES = ['admin', 'user', 'guest'] as const;
type Role = (typeof ROLES)[number]; // Extracts 'admin' | 'user' | 'guest'

const CONFIG = {
  maxRetries: 3,
  timeout: 5000,
  endpoints: {
    users: '/api/users',
    posts: '/api/posts',
  },
} as const;

// Type is: { readonly maxRetries: 3; readonly timeout: 5000; readonly endpoints: {...} }
type EndpointKey = keyof typeof CONFIG.endpoints; // 'users' | 'posts'

// ❌ Avoid: Mutable constants without `as const`
const ROLES = ['admin', 'user', 'guest']; // Type is string[], loses specificity
const CONFIG = { maxRetries: 3, timeout: 5000 }; // Type is { maxRetries: number; ... }
```

## TypeScript Compiler Configuration (tsconfig.json)

### Recommended Modern Setup

```json
{
  "compilerOptions": {
    "target": "es2025",
    "module": "esnext",
    "lib": ["es2025"],
    "jsx": "react-jsx",
    "moduleResolution": "bundler",
    "strict": true,
    "exactOptionalPropertyTypes": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noImplicitOverride": true,
    "noImplicitAny": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noPropertyAccessFromIndexSignature": true,
    "noUncheckedSideEffectImports": true,
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "isolatedModules": true,
    "verbatimModuleSyntax": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist", "**/*.spec.ts", "**/*.test.ts"]
}
```

### Quick Reference for Common Options

| Option | Purpose | Recommended |
|--------|---------|-------------|
| `strict` | Enables all strict type-checking options | `true` |
| `exactOptionalPropertyTypes` | Enforce exact optional property types | `true` |
| `noUncheckedIndexedAccess` | Prevent unsafe index access | `true` |
| `noImplicitAny` | Error on implicit `any` | `true` |
| `verbatimModuleSyntax` | Preserve import/export syntax | `true` |
| `isolatedModules` | Each file can be transpiled independently | `true` |
| `moduleResolution` | Module resolution strategy (`bundler`, `nodenext`) | `bundler` |
| `allowSyntheticDefaultImports` | Allow default imports from CommonJS | `true` |
| `esModuleInterop` | Compatibility with CommonJS modules | `true` |

## TypeScript 6.0+ Breaking Changes and Migration

### Major Breaking Changes

**1. `strict` is now `true` by default**
- Set `"strict": false` if you need the old behavior
- Strongly recommended to resolve errors instead

**2. `types` now defaults to `[]` (empty array)**
- You must explicitly list required `@types` packages
- Performance improvement of 20-50% in build times

```json
{
  "compilerOptions": {
    "types": ["node", "jest"]
  }
}
```

**3. `rootDir` now defaults to `.` (tsconfig.json directory)**
- If sources are in `./src`, explicitly set `"rootDir": "./src"`

**4. Target `es5` is deprecated**
- Minimum target is now `es2015`
- Use an external transpiler if you need ES5 output

**5. `module` resolution changes**
- `node` (node10) is deprecated → Use `nodenext` or `bundler`
- `classic` is removed
- `amd`, `umd`, `systemjs` are removed → Use ESM

**6. `baseUrl` is deprecated**
- Use `paths` mappings instead for import aliasing

```json
{
  "compilerOptions": {
    "paths": {
      "@app/*": ["./src/app/*"],
      "@lib/*": ["./src/lib/*"]
    }
  }
}
```

### Deprecations (Set `ignoreDeprecations: "6.0"` to suppress)

- `--downlevelIteration` (only works with es5, which is deprecated)
- `--outFile` (use bundlers instead)
- `module` keyword for namespaces (use `namespace`)
- `asserts` keyword in imports (use `with` instead)
- `--alwaysStrict false` (strict mode is always enforced)

### Import/Export Updates

**Import Attributes (new standard)**

```typescript
// ❌ Old: import assertions
import config from './config.json' assert { type: 'json' };

// ✅ New: import attributes
import config from './config.json' with { type: 'json' };
```

## Deprecated Code Patterns and Replacements

### Pattern: `any` Type

```typescript
// ❌ Bad
function process(data: any): void {
  console.log(data.name); // No type checking
}

// ✅ Good
function process(data: unknown): void {
  if (typeof data === 'object' && data !== null && 'name' in data) {
    console.log(data.name);
  }
}

// ✅ Better: Use specific types
interface DataPayload {
  name: string;
}

function process(data: DataPayload): void {
  console.log(data.name);
}
```

### Pattern: Optional Properties Without Proper Typing

```typescript
// ❌ Bad: Unclear if property should exist
interface User {
  id: string;
  email?: string; // Can be string or undefined
}

// ✅ Good: Explicit about presence
interface User {
  id: string;
  email: string | undefined; // Clearer intent
}

// ✅ Or: Make it actually optional
interface UserOptional {
  id: string;
  email?: string; // May not be present
}
```

### Pattern: Namespace Syntax for Modules

```typescript
// ❌ Old (deprecated in 6.0)
module Foo {
  export const bar = 10;
}

// ✅ New: Use namespace
namespace Foo {
  export const bar = 10;
}

// ✅ Better: Use module syntax for actual modules
// foo.ts
export const bar = 10;

// other.ts
import { bar } from './foo';
```

### Pattern: Callback-Based Async

```typescript
// ❌ Bad: Callback hell
function fetchData(id: string, callback: (err: Error | null, data?: User) => void): void {
  API.get(`/users/${id}`, (err, response) => {
    if (err) {
      callback(err);
      return;
    }
    callback(null, response);
  });
}

// ✅ Good: Promise-based
function fetchData(id: string): Promise<User> {
  return API.get(`/users/${id}`);
}

// ✅ Better: Async/await
async function fetchData(id: string): Promise<User> {
  const response = await API.get(`/users/${id}`);
  return response;
}
```

### Pattern: Type Assertions Instead of Proper Typing

```typescript
// ❌ Bad: Using `as` to force types
const user = API.getUser() as User;
const count = (items.length as any) as number;

// ✅ Good: Proper typing ensures correctness
function getUser(): User {
  // Type is verified at return
}

const count: number = items.length; // Already a number
```

## Testing and Validation

### Type Validation Patterns

```typescript
// ✅ Good: Use validation libraries for runtime type checking
import { z } from 'zod';

const userSchema = z.object({
  id: z.string(),
  email: z.string().email(),
  role: z.enum(['admin', 'user']),
});

type User = z.infer<typeof userSchema>;

// Runtime validation
function processUserData(data: unknown): User {
  return userSchema.parse(data); // Throws if invalid
}
```

### Error Types

```typescript
// ✅ Good: Custom error types
class ValidationError extends Error {
  constructor(public field: string, message: string) {
    super(message);
    this.name = 'ValidationError';
  }
}

class NotFoundError extends Error {
  constructor(public resource: string, id: string) {
    super(`${resource} with id ${id} not found`);
    this.name = 'NotFoundError';
  }
}

// Usage
try {
  const user = await fetchUser(userId);
} catch (error) {
  if (error instanceof NotFoundError) {
    console.log(`${error.resource} not found`);
  } else if (error instanceof ValidationError) {
    console.log(`Invalid ${error.field}`);
  }
}
```

## Quick Reference

See supplementary documents for:

- [Patterns & Examples](./patterns-and-examples.md) — Reusable code patterns, OOP designs, generic utilities
- [TypeScript 6.0 & tsconfig](./typescript-6-and-tsconfig.md) — Breaking changes, migration guide, tsconfig deep dive

## Additional Resources

- [TypeScript 5.9 Release Notes](./documentation/typescript-5-9-summary.md)
- [TypeScript 6.0 Release Notes](./documentation/typescript-6-0-summary.md)
- [tsconfig.json Reference](./documentation/tsconfig-json-reference.md)
- [Official TypeScript Handbook](https://www.typescriptlang.org/docs/)
