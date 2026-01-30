# TypeScript Application

Modern TypeScript application following best practices.

## Tech Stack

- **Language**: TypeScript 5.x
- **Runtime**: Node.js 20+ / Bun / Deno
- **Package Manager**: npm / pnpm / yarn
- **Testing**: Vitest / Jest
- **Linting**: ESLint + typescript-eslint
- **Formatting**: Prettier

## Project Structure

- `src/` - Source code
  - `types/` - Type definitions
  - `utils/` - Utility functions
  - `services/` - Business logic
  - `index.ts` - Entry point
- `tests/` - Test files
- `tsconfig.json` - TypeScript configuration

## Development Commands

- **Build**: `npm run build`
- **Dev**: `npm run dev`
- **Test**: `npm test`
- **Lint**: `npm run lint`
- **Type check**: `npm run typecheck`

## Coding Guidelines

### Type Definitions

```typescript
// Prefer interfaces for object shapes
interface User {
  id: string;
  name: string;
  email: string;
}

// Use type for unions, intersections
type Status = 'active' | 'inactive' | 'pending';
type ID = string | number;

// Generic types
interface ApiResponse<T> {
  data: T;
  status: number;
}
```

### Functions

```typescript
// Explicit return types
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// Generic functions
function first<T>(array: T[]): T | undefined {
  return array[0];
}
```

### Null Handling

```typescript
// Nullish coalescing
const value = input ?? defaultValue;

// Optional chaining
const name = user?.profile?.name;

// Type narrowing
function process(value: string | null): string {
  if (value === null) return 'default';
  return value.toUpperCase();
}
```

### Error Handling

```typescript
// Custom error types
class ValidationError extends Error {
  constructor(message: string, public readonly field: string) {
    super(message);
    this.name = 'ValidationError';
  }
}

// Result type pattern
type Result<T, E = Error> =
  | { success: true; data: T }
  | { success: false; error: E };
```

### Async/Await

```typescript
async function fetchUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) {
    throw new Error(`Failed: ${response.status}`);
  }
  return response.json();
}
```

## Key Guidelines

1. Enable strict mode in tsconfig
2. Prefer interfaces for objects, types for unions
3. Avoid `any` - use `unknown` when needed
4. Use readonly for immutable data
5. Export types alongside implementations
