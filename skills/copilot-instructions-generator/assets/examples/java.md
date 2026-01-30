# Java Application

Modern Java application following best practices.

## Tech Stack

- **Language**: Java 21 (LTS)
- **Build**: Maven / Gradle
- **Testing**: JUnit 5 + Mockito
- **Logging**: SLF4J + Logback
- **IDE**: IntelliJ IDEA / Eclipse

## Project Structure

- `src/main/java/` - Source code
  - `domain/` - Domain models and business logic
  - `service/` - Application services
  - `repository/` - Data access layer
  - `controller/` - API controllers (if web)
  - `config/` - Configuration classes
  - `util/` - Utility classes
- `src/main/resources/` - Resources
- `src/test/java/` - Test code
- `pom.xml` or `build.gradle` - Build configuration

## Development Commands

### Maven
- **Build**: `mvn clean package`
- **Test**: `mvn test`
- **Run**: `mvn exec:java` or `java -jar target/app.jar`
- **Format**: `mvn spotless:apply`

### Gradle
- **Build**: `./gradlew build`
- **Test**: `./gradlew test`
- **Run**: `./gradlew run`
- **Format**: `./gradlew spotlessApply`

## Before Committing

- Run formatter (Spotless/Google Java Format)
- Run `mvn verify` or `./gradlew check`
- Ensure all tests pass
- Check for compiler warnings

## Coding Guidelines

### Java Style
- Follow Google Java Style Guide
- Use 2 or 4 space indentation (be consistent)
- Maximum line length: 100-120 characters
- One class per file

### Naming Conventions
- Classes: `PascalCase`
- Methods/variables: `camelCase`
- Constants: `UPPER_SNAKE_CASE`
- Packages: lowercase, no underscores

### Modern Java Features (17+)
```java
// Records for immutable data
public record User(String id, String name, String email) {}

// Pattern matching
if (obj instanceof String s) {
    System.out.println(s.length());
}

// Switch expressions
String result = switch (status) {
    case ACTIVE -> "Active user";
    case INACTIVE -> "Inactive user";
    default -> "Unknown";
};

// Text blocks
String json = """
    {
        "name": "example",
        "value": 123
    }
    """;

// Sealed classes
public sealed interface Shape permits Circle, Rectangle {}
```

### Null Handling
- Use `Optional<T>` for potentially absent values
- Never return null from collections (return empty)
- Use `@Nullable` and `@NonNull` annotations
- Validate parameters early (fail fast)

### Collections
- Prefer interfaces (`List`, `Map`) over implementations
- Use immutable collections when possible
- Use Stream API for transformations
- Consider `var` for local variables with clear types

### Exception Handling
```java
// Custom exceptions
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String userId) {
        super("User not found: " + userId);
    }
}

// Try-with-resources
try (var reader = new BufferedReader(new FileReader(path))) {
    return reader.lines().toList();
}
```

### Dependency Injection
- Use constructor injection
- Make dependencies final
- Avoid field injection
- Use interfaces for dependencies

### Documentation
```java
/**
 * Retrieves a user by their unique identifier.
 *
 * @param userId the user's unique identifier
 * @return the user if found
 * @throws UserNotFoundException if no user exists with the given ID
 */
public User findById(String userId) {
    // ...
}
```

### Logging
```java
private static final Logger log = LoggerFactory.getLogger(UserService.class);

public User createUser(CreateUserRequest request) {
    log.info("Creating user with email: {}", request.email());
    // ...
    log.debug("User created successfully: {}", user.id());
    return user;
}
```

## Testing Standards

- Use JUnit 5 with `@DisplayName`
- One assertion concept per test
- Use Mockito for mocking dependencies
- Test edge cases and error paths
- Use AssertJ for fluent assertions

```java
@Test
@DisplayName("should return user when valid ID provided")
void findById_ValidId_ReturnsUser() {
    // Arrange
    when(repository.findById("123")).thenReturn(Optional.of(testUser));
    
    // Act
    User result = service.findById("123");
    
    // Assert
    assertThat(result).isEqualTo(testUser);
}
```

## Key Guidelines

1. Prefer composition over inheritance
2. Make classes final unless designed for extension
3. Use immutable objects when possible
4. Follow SOLID principles
5. Keep methods small and focused
