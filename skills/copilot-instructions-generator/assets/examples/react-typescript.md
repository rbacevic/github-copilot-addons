# React TypeScript Application

Modern React application with TypeScript, following current best practices.

## Tech Stack

- **Language**: TypeScript 5.x (strict mode)
- **Framework**: React 18+ with functional components
- **Build**: Vite
- **Package Manager**: npm
- **Testing**: Vitest + React Testing Library
- **Styling**: Tailwind CSS

## Project Structure

- `src/` - Application source code
  - `components/` - Reusable UI components
  - `pages/` - Route-level page components
  - `hooks/` - Custom React hooks
  - `services/` - API calls and external integrations
  - `utils/` - Pure utility functions
  - `types/` - TypeScript type definitions
  - `context/` - React context providers
- `tests/` - Test files (mirrors src structure)
- `public/` - Static assets

## Development Commands

- **Install**: `npm install`
- **Dev server**: `npm run dev`
- **Build**: `npm run build`
- **Test**: `npm test`
- **Lint**: `npm run lint`
- **Type check**: `npm run type-check`

## Before Committing

- Run `npm run lint` to check for issues
- Run `npm run type-check` for TypeScript errors
- Run `npm test` to verify tests pass

## Coding Guidelines

### TypeScript
- Enable strict mode in tsconfig.json
- Use interfaces for object shapes, types for unions/primitives
- Avoid `any` - use `unknown` when type is truly unknown
- Export types alongside their implementations

### React Components
- Use functional components with hooks exclusively
- One component per file, named same as file
- Destructure props in function signature
- Use React.FC sparingly - prefer explicit return types

### Hooks
- Custom hooks must start with "use"
- Keep hooks focused on single responsibility
- Extract complex state logic into custom hooks

### Imports
- Order: React, external packages, internal absolute, relative
- Use absolute imports with `@/` prefix for src directory
- Avoid default exports except for pages/components

### Styling
- Use Tailwind utility classes
- Extract repeated patterns to component classes
- Mobile-first responsive design

### State Management
- Use useState for local component state
- Use useReducer for complex state logic
- Use context for cross-cutting concerns only

### Error Handling
- Use Error Boundaries for component errors
- Handle async errors with try/catch
- Display user-friendly error messages

## Testing Standards

- Test behavior, not implementation
- Use React Testing Library queries (getByRole preferred)
- Mock external dependencies, not internal modules
- Aim for meaningful coverage, not 100%

## Key Guidelines

1. Prefer composition over prop drilling
2. Keep components small and focused
3. Handle loading and error states explicitly
4. Use semantic HTML elements
5. Ensure accessibility (ARIA labels, keyboard navigation)
