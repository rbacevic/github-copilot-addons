# Go Service

Backend service built with Go following idiomatic patterns.

## Tech Stack

- **Language**: Go 1.21+
- **Framework**: Standard library + chi router
- **Database**: PostgreSQL with pgx
- **Testing**: Standard testing package + testify
- **Build**: Make + Go modules

## Project Structure

- `cmd/` - Application entry points
  - `server/` - Main API server
  - `worker/` - Background job processor
- `internal/` - Private application code
  - `api/` - HTTP handlers and middleware
  - `domain/` - Business logic and entities
  - `repository/` - Database access
  - `service/` - Application services
- `pkg/` - Public reusable packages
- `migrations/` - SQL migration files
- `scripts/` - Development scripts

## Development Commands

- **Build**: `make build`
- **Test**: `make test`
- **Run**: `make run`
- **Lint**: `make lint` (uses golangci-lint)
- **Format**: `make fmt`
- **Full CI**: `make ci`

## Before Committing

- Run `make fmt` to format code with gofmt
- Run `make lint` to check for issues
- Run `make test` to verify tests pass

## Coding Guidelines

### General Go Style
- Follow Effective Go guidelines
- Use gofmt for formatting (non-negotiable)
- Keep functions small and focused
- Prefer composition over inheritance

### Naming
- Use MixedCaps, not underscores
- Package names: short, lowercase, single word
- Interfaces: verb-er pattern (Reader, Writer)
- Avoid stuttering (http.HTTPServer → http.Server)

### Error Handling
- Always check returned errors
- Wrap errors with context: `fmt.Errorf("operation failed: %w", err)`
- Use custom error types for domain errors
- Never use panic in library code

### Concurrency
- Use channels for communication between goroutines
- Use sync package primitives when needed
- Avoid goroutine leaks - ensure they can exit
- Use context for cancellation

### Interfaces
- Keep interfaces small (1-3 methods)
- Define interfaces where they're used, not implemented
- Accept interfaces, return concrete types
- Use interface{} sparingly - prefer generics

### Packages
- One package per directory
- internal/ for private packages
- Avoid circular dependencies
- Keep package APIs minimal

### Database Access
- Use prepared statements
- Handle connection pooling properly
- Use transactions for multi-step operations
- Close rows and statements

### HTTP Handlers
- Use middleware for cross-cutting concerns
- Return appropriate status codes
- Log requests with request ID
- Use structured logging (slog)

## Testing Standards

- Table-driven tests for multiple cases
- Use testify for assertions
- Mock external dependencies with interfaces
- Test error paths, not just happy paths
- Use t.Parallel() for independent tests

## Project Patterns

### Dependency Injection
```go
type Server struct {
    userService UserService
    logger      *slog.Logger
}

func NewServer(us UserService, l *slog.Logger) *Server {
    return &Server{userService: us, logger: l}
}
```

### Error Types
```go
type NotFoundError struct {
    Resource string
    ID       string
}

func (e *NotFoundError) Error() string {
    return fmt.Sprintf("%s not found: %s", e.Resource, e.ID)
}
```

## Key Guidelines

1. Error handling is explicit - never ignore errors
2. Keep the main package minimal - delegate to internal
3. Use context for request-scoped values and cancellation
4. Prefer standard library over external dependencies
5. Document exported types and functions
