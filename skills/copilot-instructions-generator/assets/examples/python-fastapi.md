# Python FastAPI Service

REST API service built with FastAPI and modern Python practices.

## Tech Stack

- **Language**: Python 3.11+
- **Framework**: FastAPI
- **Database**: PostgreSQL with SQLAlchemy 2.0
- **Package Manager**: Poetry
- **Testing**: pytest with pytest-asyncio
- **Validation**: Pydantic v2

## Project Structure

- `app/` - Application source code
  - `api/` - API route handlers
    - `v1/` - Version 1 endpoints
  - `core/` - Core configuration and security
  - `models/` - SQLAlchemy ORM models
  - `schemas/` - Pydantic request/response models
  - `services/` - Business logic layer
  - `repositories/` - Database access layer
- `tests/` - Test files (mirrors app structure)
- `migrations/` - Alembic database migrations
- `scripts/` - Utility scripts

## Development Commands

- **Install**: `poetry install`
- **Dev server**: `poetry run uvicorn app.main:app --reload`
- **Test**: `poetry run pytest`
- **Lint**: `poetry run ruff check .`
- **Format**: `poetry run ruff format .`
- **Type check**: `poetry run mypy app`
- **Migrations**: `poetry run alembic upgrade head`

## Before Committing

- Run `poetry run ruff format .` for consistent formatting
- Run `poetry run ruff check .` for linting
- Run `poetry run mypy app` for type checking
- Run `poetry run pytest` to verify tests pass

## Coding Guidelines

### Python Style
- Follow PEP 8 (enforced via ruff)
- Use type hints for all function signatures
- Maximum line length: 88 characters (Black default)
- Use f-strings for string formatting

### Async Patterns
- Use async/await for all I/O operations
- Use asyncio.gather for concurrent operations
- Avoid blocking calls in async functions

### Pydantic Models
- Use Pydantic v2 syntax (model_validator, field_validator)
- Separate request and response schemas
- Use Field() for validation constraints
- Document fields with description parameter

### SQLAlchemy
- Use SQLAlchemy 2.0 style (select, not query)
- Define relationships explicitly
- Use async sessions with asyncpg
- Migrations required for all schema changes

### API Design
- Follow REST conventions for endpoints
- Use appropriate HTTP status codes
- Return consistent response structures
- Document endpoints with OpenAPI docstrings

### Dependency Injection
- Use FastAPI's Depends() for dependencies
- Create dependencies for database sessions
- Use dependencies for authentication/authorization

### Error Handling
- Define custom exception classes in core/exceptions.py
- Use exception handlers for consistent error responses
- Log errors with appropriate context
- Never expose internal errors to clients

## Testing Standards

- Use pytest fixtures for setup/teardown
- Test async functions with pytest-asyncio
- Use factory_boy for test data
- Mock external services, not internal code
- Integration tests use test database

## Security Practices

- Validate all input with Pydantic
- Use parameterized queries (SQLAlchemy handles this)
- Store secrets in environment variables
- Implement rate limiting on public endpoints
- Use HTTPS in production

## Key Guidelines

1. Separate concerns: routes → services → repositories
2. All database access through repository layer
3. Business logic lives in services, not routes
4. Use dependency injection for testability
5. Keep endpoints thin - delegate to services
