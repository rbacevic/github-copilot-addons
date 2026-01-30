# .NET C# Application

Modern .NET application following Microsoft best practices and clean architecture.

## Tech Stack

- **Language**: C# 12 / .NET 8
- **IDE**: Visual Studio 2022 / Rider / VS Code
- **Testing**: xUnit + Moq + FluentAssertions
- **Build**: dotnet CLI / MSBuild
- **Package Manager**: NuGet

## Project Structure

- `src/` - Source code
  - `Domain/` - Enterprise business rules, entities
  - `Application/` - Application business rules, use cases
  - `Infrastructure/` - External concerns (DB, APIs, files)
  - `Presentation/` - UI layer (API controllers, views)
- `tests/` - Test projects
  - `UnitTests/` - Unit tests
  - `IntegrationTests/` - Integration tests
- `docs/` - Documentation

## Development Commands

- **Build**: `dotnet build`
- **Test**: `dotnet test`
- **Run**: `dotnet run --project src/Presentation`
- **Format**: `dotnet format`
- **Publish**: `dotnet publish -c Release`

## Before Committing

- Run `dotnet format` for consistent formatting
- Run `dotnet build --warnaserrors` to catch warnings
- Run `dotnet test` to verify tests pass

## Coding Guidelines

### C# Style
- Use file-scoped namespaces
- Use primary constructors where appropriate
- Prefer records for immutable data transfer objects
- Use pattern matching and switch expressions
- Enable nullable reference types (`<Nullable>enable</Nullable>`)

### Naming Conventions
- PascalCase for public members, types, namespaces
- camelCase for private fields (with _ prefix: `_fieldName`)
- I prefix for interfaces (`IUserService`)
- Async suffix for async methods (`GetUserAsync`)

### Async/Await
- Use async/await for all I/O operations
- Avoid `.Result` and `.Wait()` - causes deadlocks
- Use `ConfigureAwait(false)` in library code
- Return `Task` not `void` for async methods

### Dependency Injection
- Use constructor injection
- Register services in `Program.cs` or extension methods
- Prefer interfaces for dependencies
- Use appropriate lifetimes (Scoped for DB contexts)

### Error Handling
- Use exceptions for exceptional cases only
- Create custom exception types for domain errors
- Use Result pattern for expected failures
- Log exceptions with full context

### LINQ
- Prefer method syntax over query syntax
- Use meaningful variable names in lambdas
- Avoid multiple enumerations (materialize with `.ToList()`)
- Use async LINQ methods for EF Core queries

## Testing Standards

- Arrange-Act-Assert pattern
- One assertion concept per test
- Use FluentAssertions for readable assertions
- Mock external dependencies only
- Use test fixtures for shared setup

## Key Guidelines

1. Follow SOLID principles
2. Keep controllers thin - delegate to services
3. Use MediatR for CQRS pattern
4. Validate input at API boundaries
5. Use strongly-typed IDs for entities
