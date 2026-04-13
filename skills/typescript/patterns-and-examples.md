# TypeScript Patterns and Examples

Reusable code patterns, OOP designs, and generic utilities for production TypeScript development.

## Table of Contents

- [Utility Types and Helpers](#utility-types-and-helpers)
- [OOP Patterns](#oop-patterns)
- [Generic Utilities](#generic-utilities)
- [Error Handling](#error-handling)
- [API Client Pattern](#api-client-pattern)
- [Repository Pattern](#repository-pattern)
- [Dependency Injection](#dependency-injection)

---

## Utility Types and Helpers

### Extracting Types from Values

```typescript
// ✅ Extract union type from array
const ROLES = ['admin', 'user', 'guest'] as const;
type Role = typeof ROLES[number]; // 'admin' | 'user' | 'guest'

// ✅ Extract enum-like type from object
const STATUS = {
  pending: 'pending',
  active: 'active',
  inactive: 'inactive',
} as const;

type Status = typeof STATUS[keyof typeof STATUS]; // 'pending' | 'active' | 'inactive'

// ✅ Map object keys
const permissionConfig = {
  canRead: true,
  canWrite: false,
  canDelete: false,
} as const;

type Permission = keyof typeof permissionConfig; // 'canRead' | 'canWrite' | 'canDelete'
```

### Practical Utility Type Definitions

```typescript
// Common utility types for APIs
type ApiResponse<T> = {
  success: boolean;
  data?: T;
  error?: string;
  timestamp: Date;
};

type Paginated<T> = {
  items: T[];
  total: number;
  page: number;
  pageSize: number;
  hasMore: boolean;
};

type PaginationParams = {
  page: number;
  pageSize: number;
  sortBy?: string;
  order?: 'asc' | 'desc';
};

// Request/Response types
type ApiRequest<T> = {
  body: T;
  headers?: Record<string, string>;
  query?: Record<string, string | number | boolean>;
};

type ApiError = {
  code: string;
  message: string;
  details?: Record<string, unknown>;
};
```

---

## OOP Patterns

### Base Service Class with Logging

```typescript
abstract class BaseService {
  protected readonly logger: Logger;
  protected readonly cache: Map<string, unknown> = new Map();
  private readonly cacheTimeout: number = 5 * 60 * 1000; // 5 minutes

  constructor(logger: Logger) {
    this.logger = logger;
  }

  protected getCached<T>(key: string): T | null {
    const cached = this.cache.get(key);
    if (!cached) return null;

    const { value, timestamp } = cached as { value: T; timestamp: number };
    if (Date.now() - timestamp > this.cacheTimeout) {
      this.cache.delete(key);
      return null;
    }

    return value;
  }

  protected setCached<T>(key: string, value: T): void {
    this.cache.set(key, { value, timestamp: Date.now() });
  }

  protected clearCache(key?: string): void {
    if (key) {
      this.cache.delete(key);
    } else {
      this.cache.clear();
    }
  }
}
```

### Repository Pattern

```typescript
// Define repository interface
interface IUserRepository {
  find(id: string): Promise<User>;
  findAll(): Promise<User[]>;
  save(user: User): Promise<void>;
  delete(id: string): Promise<void>;
  exists(id: string): Promise<boolean>;
}

// Implement repository
class UserRepository implements IUserRepository {
  constructor(private db: Database) {}

  async find(id: string): Promise<User> {
    const user = await this.db.query('SELECT * FROM users WHERE id = $1', [id]);
    if (!user) {
      throw new NotFoundError('User', id);
    }
    return user;
  }

  async findAll(): Promise<User[]> {
    return this.db.query('SELECT * FROM users');
  }

  async save(user: User): Promise<void> {
    await this.db.query(
      'INSERT INTO users (id, name, email) VALUES ($1, $2, $3) ON CONFLICT (id) DO UPDATE SET name = $2, email = $3',
      [user.id, user.name, user.email]
    );
  }

  async delete(id: string): Promise<void> {
    await this.db.query('DELETE FROM users WHERE id = $1', [id]);
  }

  async exists(id: string): Promise<boolean> {
    const result = await this.db.query('SELECT 1 FROM users WHERE id = $1 LIMIT 1', [id]);
    return !!result;
  }
}

// Use repository in service
class UserService extends BaseService {
  constructor(private repo: IUserRepository, logger: Logger) {
    super(logger);
  }

  async getUserProfile(userId: string): Promise<User> {
    // Try cache first
    const cached = this.getCached<User>(`user:${userId}`);
    if (cached) {
      this.logger.debug('User retrieved from cache', { userId });
      return cached;
    }

    // Fetch from repository
    const user = await this.repo.find(userId);
    this.setCached(`user:${userId}`, user);
    return user;
  }
}
```

### Observer Pattern

```typescript
type EventListener<T> = (event: T) => void | Promise<void>;

class EventEmitter<T> {
  private listeners: Set<EventListener<T>> = new Set();

  on(listener: EventListener<T>): () => void {
    this.listeners.add(listener);
    // Return unsubscribe function
    return () => this.listeners.delete(listener);
  }

  off(listener: EventListener<T>): void {
    this.listeners.delete(listener);
  }

  async emit(event: T): Promise<void> {
    await Promise.all(Array.from(this.listeners).map(listener => listener(event)));
  }
}

// Usage
interface UserCreatedEvent {
  userId: string;
  email: string;
  createdAt: Date;
}

const userEvents = new EventEmitter<UserCreatedEvent>();

userEvents.on(async (event) => {
  console.log(`User ${event.userId} created`);
  // Send welcome email, etc.
});

await userEvents.emit({
  userId: '123',
  email: 'user@example.com',
  createdAt: new Date(),
});
```

---

## Generic Utilities

### Type-Safe Result Type (Like Rust)

```typescript
type Result<T, E = Error> =
  | { ok: true; value: T }
  | { ok: false; error: E };

class ResultOk<T>(value: T): Result<T> {
  return { ok: true, value };
}

class ResultErr<E>(error: E): Result<unknown, E> {
  return { ok: false, error };
}

// Usage
async function fetchUser(id: string): Promise<Result<User>> {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
      return { ok: false, error: new Error(`HTTP ${response.status}`) };
    }
    const data = await response.json();
    return { ok: true, value: data as User };
  } catch (error) {
    return { ok: false, error: error instanceof Error ? error : new Error(String(error)) };
  }
}

// Type-safe unwrapping
const result = await fetchUser('123');
if (result.ok) {
  console.log(result.value.name); // ✅ value exists
} else {
  console.error(result.error.message); // ✅ error exists
}
```

### Generic Cache Wrapper

```typescript
type CacheSetter<T> = (key: string, value: T) => Promise<void>;
type CacheGetter<T> = (key: string) => Promise<T | null>;

class CachedFunction<T, Args extends unknown[]> {
  constructor(
    private fn: (...args: Args) => Promise<T>,
    private getter: CacheGetter<T>,
    private setter: CacheSetter<T>,
    private keyGenerator: (...args: Args) => string
  ) {}

  async call(...args: Args): Promise<T> {
    const key = this.keyGenerator(...args);

    // Try cache
    const cached = await this.getter(key);
    if (cached !== null) {
      return cached;
    }

    // Execute function
    const result = await this.fn(...args);

    // Store in cache
    await this.setter(key, result);

    return result;
  }
}
```

### Generic Validator

```typescript
type Validator<T> = (value: unknown) => Result<T, string>;

function createStringValidator(minLength = 0, maxLength = Infinity): Validator<string> {
  return (value: unknown): Result<string, string> => {
    if (typeof value !== 'string') {
      return { ok: false, error: 'Value must be a string' };
    }
    if (value.length < minLength || value.length > maxLength) {
      return {
        ok: false,
        error: `String length must be between ${minLength} and ${maxLength}`,
      };
    }
    return { ok: true, value };
  };
}

function createObjectValidator<T extends Record<string, unknown>>(
  schema: Record<keyof T, Validator<any>>
): Validator<T> {
  return (value: unknown): Result<T, string> => {
    if (typeof value !== 'object' || value === null) {
      return { ok: false, error: 'Value must be an object' };
    }

    const obj = value as Record<string, unknown>;
    const result: Record<string, unknown> = {};

    for (const [key, validator] of Object.entries(schema)) {
      const fieldResult = validator(obj[key]);
      if (!fieldResult.ok) {
        return { ok: false, error: `Field '${key}': ${fieldResult.error}` };
      }
      result[key] = fieldResult.value;
    }

    return { ok: true, value: result as T };
  };
}

// Usage
const userValidator = createObjectValidator<User>({
  id: createStringValidator(1, 50),
  email: createStringValidator(5, 100),
});

const validationResult = userValidator(userData);
if (validationResult.ok) {
  console.log('Valid user:', validationResult.value);
} else {
  console.error('Validation error:', validationResult.error);
}
```

---

## Error Handling

### Custom Error Hierarchy

```typescript
abstract class AppError extends Error {
  abstract readonly statusCode: number;
  abstract readonly isOperational: boolean;

  constructor(message: string) {
    super(message);
    Object.setPrototypeOf(this, AppError.prototype);
  }
}

class ValidationError extends AppError {
  readonly statusCode = 400;
  readonly isOperational = true;

  constructor(
    public readonly field: string,
    message: string
  ) {
    super(message);
    Object.setPrototypeOf(this, ValidationError.prototype);
  }
}

class NotFoundError extends AppError {
  readonly statusCode = 404;
  readonly isOperational = true;

  constructor(
    public readonly resource: string,
    public readonly id: string
  ) {
    super(`${resource} with id '${id}' not found`);
    Object.setPrototypeOf(this, NotFoundError.prototype);
  }
}

class ConflictError extends AppError {
  readonly statusCode = 409;
  readonly isOperational = true;

  constructor(
    public readonly resource: string,
    message: string
  ) {
    super(message);
    Object.setPrototypeOf(this, ConflictError.prototype);
  }
}

class UnauthorizedError extends AppError {
  readonly statusCode = 401;
  readonly isOperational = true;

  constructor(message = 'Unauthorized') {
    super(message);
    Object.setPrototypeOf(this, UnauthorizedError.prototype);
  }
}

class InternalServerError extends AppError {
  readonly statusCode = 500;
  readonly isOperational = false;

  constructor(message = 'Internal server error') {
    super(message);
    Object.setPrototypeOf(this, InternalServerError.prototype);
  }
}
```

### Error Handler Middleware

```typescript
function handleError(error: unknown): { statusCode: number; message: string } {
  if (error instanceof AppError) {
    return {
      statusCode: error.statusCode,
      message: error.message,
    };
  }

  if (error instanceof Error) {
    // Log unexpected errors
    console.error('Unexpected error:', error);
    return {
      statusCode: 500,
      message: 'Internal server error',
    };
  }

  console.error('Unknown error:', error);
  return {
    statusCode: 500,
    message: 'Internal server error',
  };
}
```

---

## API Client Pattern

```typescript
interface ApiClientConfig {
  baseUrl: string;
  timeout?: number;
  headers?: Record<string, string>;
}

class ApiClient {
  private readonly baseUrl: string;
  private readonly timeout: number;
  private readonly defaultHeaders: Record<string, string>;

  constructor(config: ApiClientConfig) {
    this.baseUrl = config.baseUrl;
    this.timeout = config.timeout ?? 30000;
    this.defaultHeaders = {
      'Content-Type': 'application/json',
      ...config.headers,
    };
  }

  async get<T>(path: string, options?: { signal?: AbortSignal }): Promise<T> {
    return this.request<T>(path, { method: 'GET', ...options });
  }

  async post<T>(path: string, body: unknown, options?: { signal?: AbortSignal }): Promise<T> {
    return this.request<T>(path, {
      method: 'POST',
      body: JSON.stringify(body),
      ...options,
    });
  }

  async put<T>(path: string, body: unknown, options?: { signal?: AbortSignal }): Promise<T> {
    return this.request<T>(path, {
      method: 'PUT',
      body: JSON.stringify(body),
      ...options,
    });
  }

  async delete<T>(path: string, options?: { signal?: AbortSignal }): Promise<T> {
    return this.request<T>(path, { method: 'DELETE', ...options });
  }

  private async request<T>(
    path: string,
    init: RequestInit & { signal?: AbortSignal }
  ): Promise<T> {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), this.timeout);

    try {
      const url = `${this.baseUrl}${path}`;
      const response = await fetch(url, {
        ...init,
        headers: {
          ...this.defaultHeaders,
          ...(init.headers || {}),
        },
        signal: init.signal ?? controller.signal,
      });

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }

      return (await response.json()) as T;
    } finally {
      clearTimeout(timeoutId);
    }
  }
}

// Usage
const api = new ApiClient({ baseUrl: 'https://api.example.com' });
const users = await api.get<User[]>('/users');
```

---

## Dependency Injection

### Simple DI Container

```typescript
type Constructor<T> = new (...args: unknown[]) => T;

class Container {
  private services = new Map<string, unknown>();
  private factories = new Map<string, () => unknown>();

  register<T>(key: string, service: T): void {
    this.services.set(key, service);
  }

  registerFactory<T>(key: string, factory: () => T): void {
    this.factories.set(key, factory);
  }

  get<T>(key: string): T {
    // Check singleton services first
    if (this.services.has(key)) {
      return this.services.get(key) as T;
    }

    // Check factories
    if (this.factories.has(key)) {
      const factory = this.factories.get(key)!;
      const instance = factory();
      this.services.set(key, instance); // Cache the result
      return instance as T;
    }

    throw new Error(`Service '${key}' not registered`);
  }
}

// Usage
const container = new Container();

// Register services
container.register('logger', new Logger());
container.registerFactory('userRepo', () => new UserRepository(container.get('db')));
container.registerFactory('userService', () => new UserService(container.get('userRepo'), container.get('logger')));

// Retrieve services
const userService = container.get<UserService>('userService');
```

---

## Best Practices Summary

- ✅ Use composition over inheritance
- ✅ Depend on abstractions (interfaces), not concrete implementations
- ✅ Keep functions small and focused
- ✅ Use type unions and discriminated unions for type-safe branching
- ✅ Validate at API boundaries (input/output)
- ✅ Use proper error types, never generic `Error`
- ✅ Cache strategically when performance matters
- ✅ Use readonly properties and immutability where appropriate
- ✅ Document why, not what (code shows what)
