# Python Application

Modern Python application following PEP standards and best practices.

## Tech Stack

- **Language**: Python 3.11+
- **Package Manager**: Poetry / uv
- **Testing**: pytest
- **Linting**: ruff
- **Type Checking**: mypy
- **Formatting**: ruff format (or black)

## Project Structure

- `src/` - Source code package
  - `__init__.py` - Package initialization
  - `main.py` - Application entry point
  - `models/` - Data models and schemas
  - `services/` - Business logic
  - `utils/` - Utility functions
- `tests/` - Test files
- `scripts/` - Utility scripts
- `pyproject.toml` - Project configuration

## Development Commands

- **Install**: `poetry install` or `uv sync`
- **Run**: `poetry run python -m src.main`
- **Test**: `poetry run pytest`
- **Lint**: `poetry run ruff check .`
- **Format**: `poetry run ruff format .`
- **Type check**: `poetry run mypy src`

## Before Committing

- Run `ruff format .` for consistent formatting
- Run `ruff check .` to fix linting issues
- Run `mypy src` for type checking
- Run `pytest` to verify tests pass

## Coding Guidelines

### Style
- Follow PEP 8 style guide
- Maximum line length: 88 characters
- Use 4 spaces for indentation
- Two blank lines between top-level definitions

### Type Hints
- Add type hints to all function signatures
- Use `typing` module for complex types
- Use `TypeAlias` for complex type definitions
- Enable strict mypy checking

### Naming Conventions
- `snake_case` for functions, variables, modules
- `PascalCase` for classes
- `UPPER_SNAKE_CASE` for constants
- Private members prefixed with `_`

### Imports
- Standard library first, then third-party, then local
- Absolute imports preferred over relative
- One import per line for clarity
- Use `from __future__ import annotations` for forward refs

### Functions
- Keep functions small and focused
- Use docstrings for public functions
- Default arguments must be immutable
- Use `*args` and `**kwargs` sparingly

### Classes
- Use dataclasses for data containers
- Use `@property` for computed attributes
- Implement `__repr__` for debugging
- Prefer composition over inheritance

### Error Handling
- Use specific exception types
- Create custom exceptions for domain errors
- Use context managers for resources
- Log exceptions with traceback

### String Formatting
- Use f-strings for interpolation
- Use `.format()` for templates
- Raw strings for regex patterns
- Triple quotes for multiline strings

## Data Structures

- Use `list` for ordered collections
- Use `dict` for key-value mappings
- Use `set` for unique items
- Use `tuple` for immutable sequences
- Use `dataclass` or `NamedTuple` for structured data

## Testing Standards

- Use pytest fixtures for setup
- Parametrize tests for multiple cases
- Mock external dependencies
- Test edge cases and error paths
- Aim for high coverage on business logic

## Key Guidelines

1. Explicit is better than implicit
2. Use pathlib for file operations
3. Use logging module, not print
4. Handle resources with context managers
5. Write docstrings for public APIs
