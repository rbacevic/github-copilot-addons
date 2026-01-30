# Copilot Instructions Template

Use this template as a starting point. Adapt sections based on project needs.

## Standard Template Structure

```markdown
# Project Overview

[1-2 sentence description of what this application does]

## Tech Stack

- **Language**: [Primary language and version]
- **Framework**: [Main framework]
- **Database**: [Database technology]
- **Package Manager**: [npm/yarn/pip/etc.]
- **Build Tool**: [Webpack/Vite/Make/etc.]
- **Testing**: [Jest/Pytest/etc.]

## Project Structure

- `src/` - Main application source code
  - `components/` - [Description]
  - `services/` - [Description]
  - `utils/` - [Description]
- `tests/` - Test files
- `docs/` - Documentation
- `scripts/` - Development and deployment scripts

## Development Workflow

### Setup
- Run `[install command]` to install dependencies
- Copy `.env.example` to `.env` and configure

### Common Commands
- **Build**: `[build command]`
- **Test**: `[test command]`
- **Lint**: `[lint command]`
- **Format**: `[format command]`
- **Dev Server**: `[dev command]`

### Before Committing
- Run `[format command]` to ensure consistent formatting
- Run `[lint command]` to check for issues
- Run `[test command]` to verify tests pass

## Coding Guidelines

### Naming Conventions
- [Describe naming patterns for files, variables, functions, classes]

### Code Style
- [Specific style rules, e.g., "Use double quotes for strings"]
- [Import ordering preferences]
- [Line length limits]

### Error Handling
- [How errors should be handled]
- [Logging conventions]

### Comments and Documentation
- [When to add comments]
- [Documentation requirements]

## Testing Standards

- [Required test coverage]
- [Testing patterns to follow]
- [What to test vs what not to test]

## Security Practices

- [Input validation requirements]
- [Authentication/authorization patterns]
- [Data handling rules]

## Key Guidelines

1. [Most important rule]
2. [Second most important rule]
3. [Third most important rule]
```

## Minimal Template (for simple projects)

```markdown
# [Project Name]

[Brief description]

## Tech Stack
- [Language/Framework]
- Build: `[command]`
- Test: `[command]`

## Structure
- `src/` - Source code
- `tests/` - Tests

## Guidelines
- [Key rule 1]
- [Key rule 2]
- [Key rule 3]
```

## Language-Specific Additions

### JavaScript/TypeScript
```markdown
## JavaScript/TypeScript Standards
- Use [const/let] by default, avoid var
- Prefer [arrow functions/function declarations]
- Use [semicolons/no semicolons]
- Import order: external packages, then internal modules, then relative imports
- Use TypeScript strict mode
- Prefer interfaces over type aliases for object shapes
```

### Python
```markdown
## Python Standards
- Follow PEP 8 style guide
- Use type hints for function signatures
- Use f-strings for string formatting
- Prefer pathlib over os.path
- Use dataclasses or Pydantic for data structures
- Virtual environment: [venv/poetry/conda]
```

### Go
```markdown
## Go Standards
- Follow Effective Go guidelines
- Use gofmt for formatting
- Error handling: always check errors, don't use panic
- Naming: use MixedCaps, not underscores
- Package names: short, lowercase, no underscores
```

### Java
```markdown
## Java Standards
- Follow Google Java Style Guide
- Use [Maven/Gradle] for dependencies
- Prefer immutable objects
- Use Optional for nullable returns
- Logging: use [SLF4J/Log4j]
```

### Rust
```markdown
## Rust Standards
- Run `cargo fmt` before committing
- Run `cargo clippy` and address warnings
- Use Result for error handling, avoid unwrap in production
- Prefer &str over String for function parameters when possible
```

## Framework-Specific Additions

### React
```markdown
## React Standards
- Use functional components with hooks
- State management: [Redux/Zustand/Context]
- Styling: [CSS Modules/Tailwind/Styled Components]
- Component structure: one component per file
- Props: destructure in function signature
- Custom hooks: prefix with "use"
```

### Next.js
```markdown
## Next.js Standards
- Use App Router (app/ directory)
- Prefer Server Components by default
- Use 'use client' only when necessary
- API routes: use Route Handlers
- Data fetching: use fetch with caching
```

### Django
```markdown
## Django Standards
- Follow Django coding style
- Use Class-Based Views for complex logic
- Models: singular names, verbose_name for display
- URL patterns: use path() not url()
- Settings: use django-environ for configuration
```

### Spring Boot
```markdown
## Spring Boot Standards
- Use constructor injection for dependencies
- Follow REST naming conventions
- DTOs: use records for immutable data
- Validation: use Bean Validation annotations
- Configuration: use @ConfigurationProperties
```
