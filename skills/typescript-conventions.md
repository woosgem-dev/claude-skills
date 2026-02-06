# TypeScript Conventions

A comprehensive guide to TypeScript best practices for consistent, maintainable, and type-safe codebases.

## Type System

### Prefer `interface` for Object Shapes, `type` for Unions/Intersections

```typescript
// Object shapes - use interface (supports declaration merging, better error messages)
interface User {
  id: string;
  name: string;
  email: string;
}

// Union types - use type alias
type Status = 'idle' | 'loading' | 'success' | 'error';

// Intersection / composition - use type alias
type AdminUser = User & { permissions: string[] };
```

### Avoid `any` - Use `unknown` for Truly Unknown Types

```typescript
// Bad - disables type checking entirely
function parse(input: any): any { /* ... */ }

// Good - forces callers to narrow the type before use
function parse(input: unknown): Result {
  if (typeof input === 'string') {
    return JSON.parse(input);
  }
  throw new TypeError('Expected string input');
}
```

### Use Discriminated Unions for State Modeling

```typescript
// Model states that are mutually exclusive
type AsyncState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };

function render(state: AsyncState<User>) {
  switch (state.status) {
    case 'idle':
      return 'Ready';
    case 'loading':
      return 'Loading...';
    case 'success':
      return state.data.name; // TypeScript knows `data` exists here
    case 'error':
      return state.error.message; // TypeScript knows `error` exists here
  }
}
```

### Exhaustive Checks with `never`

```typescript
function assertNever(value: never): never {
  throw new Error(`Unexpected value: ${value}`);
}

// If a new status is added to the union, TypeScript will error at default
function handleStatus(status: Status) {
  switch (status) {
    case 'idle': return;
    case 'loading': return;
    case 'success': return;
    case 'error': return;
    default: assertNever(status);
  }
}
```

## Generics

### Name Generic Parameters Descriptively for Complex Types

```typescript
// Simple cases: T, K, V are fine
function identity<T>(value: T): T { return value; }

// Complex cases: use descriptive names with T prefix
interface Repository<TEntity, TId extends string | number> {
  findById(id: TId): Promise<TEntity | null>;
  save(entity: TEntity): Promise<TEntity>;
}
```

### Constrain Generics to Prevent Misuse

```typescript
// Constrain to objects with an `id` property
function getById<T extends { id: string }>(items: T[], id: string): T | undefined {
  return items.find(item => item.id === id);
}

// Constrain to keys of an object
function pick<T, K extends keyof T>(obj: T, keys: K[]): Pick<T, K> {
  const result = {} as Pick<T, K>;
  keys.forEach(key => { result[key] = obj[key]; });
  return result;
}
```

## Strict Configuration

### Recommended `tsconfig.json` Strict Options

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "forceConsistentCasingInFileNames": true,
    "exactOptionalPropertyTypes": true
  }
}
```

- `strict: true` enables `strictNullChecks`, `strictFunctionTypes`, `strictBindCallApply`, `noImplicitAny`, `noImplicitThis`, and `alwaysStrict`.
- `noUncheckedIndexedAccess` makes index signatures return `T | undefined` instead of `T`, catching real bugs with array/object access.
- `exactOptionalPropertyTypes` distinguishes between `undefined` and missing properties.

## Error Handling

### Define Domain-Specific Error Classes

```typescript
class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly statusCode: number = 500,
    public readonly cause?: unknown,
  ) {
    super(message);
    this.name = this.constructor.name;
  }
}

class NotFoundError extends AppError {
  constructor(resource: string, id: string) {
    super(`${resource} with id "${id}" not found`, 'NOT_FOUND', 404);
  }
}

class ValidationError extends AppError {
  constructor(
    message: string,
    public readonly fields: Record<string, string>,
  ) {
    super(message, 'VALIDATION_ERROR', 400);
  }
}
```

### Use `Result` Pattern Instead of Throwing for Expected Failures

```typescript
type Result<T, E = Error> =
  | { ok: true; value: T }
  | { ok: false; error: E };

function parseConfig(raw: string): Result<Config, ValidationError> {
  try {
    const parsed = JSON.parse(raw);
    if (!isValidConfig(parsed)) {
      return { ok: false, error: new ValidationError('Invalid config', {}) };
    }
    return { ok: true, value: parsed };
  } catch {
    return { ok: false, error: new ValidationError('Invalid JSON', {}) };
  }
}

// Usage - forces handling of both cases
const result = parseConfig(input);
if (!result.ok) {
  logger.error(result.error);
  return;
}
// result.value is safely typed as Config here
```

## Modules and Imports

### Prefer `import type` for Type-Only Imports

```typescript
// Elided at runtime - zero bundle impact
import type { User, UserConfig } from './types.js';

// Mixed imports
import { UserService } from './services/user-service.js';
import type { UserServiceConfig } from './services/user-service.js';
```

### Use Barrel Exports Only at Package Boundaries

```typescript
// packages/core/src/index.ts - public API boundary, barrel is fine
export { User } from './models/user.js';
export { UserService } from './services/user-service.js';
export type { UserConfig } from './types.js';

// Avoid deep barrel chains internally - they cause circular dependencies
// and slow down build tools. Import directly for internal module usage.
```

## Naming Conventions

| Construct | Convention | Example |
|-----------|-----------|---------|
| Interface | PascalCase | `UserProfile` |
| Type alias | PascalCase | `AsyncState` |
| Enum | PascalCase | `Direction` |
| Enum member | PascalCase | `Direction.North` |
| Function | camelCase | `getUserById` |
| Variable | camelCase | `currentUser` |
| Constant | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| Class | PascalCase | `HttpClient` |
| Generic param | T-prefixed or single letter | `TEntity`, `T` |
| File (module) | kebab-case | `user-service.ts` |
| File (component) | PascalCase | `UserCard.tsx` |

## Utility Types Cheat Sheet

```typescript
// Make all properties optional
Partial<User>

// Make all properties required
Required<User>

// Make all properties readonly
Readonly<User>

// Pick specific properties
Pick<User, 'id' | 'name'>

// Omit specific properties
Omit<User, 'password'>

// Create a record type
Record<string, User>

// Extract from union
Extract<Status, 'success' | 'error'>

// Exclude from union
Exclude<Status, 'idle'>

// Get return type of function
ReturnType<typeof getUserById>

// Get parameter types of function
Parameters<typeof getUserById>

// Get resolved type of Promise
Awaited<Promise<User>>

// Remove null and undefined from type
NonNullable<string | null | undefined>  // string
```
