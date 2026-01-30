# Spring Boot Application

RESTful API service built with Spring Boot.

## Tech Stack

- **Framework**: Spring Boot 3.2+
- **Language**: Java 21
- **Build**: Maven / Gradle
- **Database**: Spring Data JPA + PostgreSQL/MySQL
- **Testing**: JUnit 5 + Spring Boot Test + Testcontainers
- **Docs**: SpringDoc OpenAPI

## Project Structure

- `src/main/java/com/example/`
  - `config/` - Configuration classes
  - `controller/` - REST controllers
  - `service/` - Business logic services
  - `repository/` - Data access interfaces
  - `model/` - Domain entities
  - `dto/` - Data transfer objects
  - `exception/` - Custom exceptions and handlers
  - `mapper/` - Object mappers
- `src/main/resources/`
  - `application.yml` - Configuration
  - `db/migration/` - Flyway migrations
- `src/test/java/` - Test code

## Development Commands

- **Run**: `./mvnw spring-boot:run`
- **Test**: `./mvnw test`
- **Build**: `./mvnw clean package`
- **Docker**: `./mvnw spring-boot:build-image`

## Before Committing

- Run tests: `./mvnw test`
- Check formatting: `./mvnw spotless:check`
- Verify build: `./mvnw verify`
- Update API docs if endpoints changed

## Coding Guidelines

### Controllers
```java
@RestController
@RequestMapping("/api/v1/users")
@RequiredArgsConstructor
@Tag(name = "Users", description = "User management endpoints")
public class UserController {

    private final UserService userService;

    @GetMapping("/{id}")
    @Operation(summary = "Get user by ID")
    public ResponseEntity<UserResponse> getUser(@PathVariable Long id) {
        return ResponseEntity.ok(userService.findById(id));
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public UserResponse createUser(@Valid @RequestBody CreateUserRequest request) {
        return userService.create(request);
    }
}
```

### Services
```java
@Service
@RequiredArgsConstructor
@Transactional(readOnly = true)
public class UserService {

    private final UserRepository userRepository;
    private final UserMapper userMapper;

    public UserResponse findById(Long id) {
        return userRepository.findById(id)
            .map(userMapper::toResponse)
            .orElseThrow(() -> new UserNotFoundException(id));
    }

    @Transactional
    public UserResponse create(CreateUserRequest request) {
        User user = userMapper.toEntity(request);
        User saved = userRepository.save(user);
        return userMapper.toResponse(saved);
    }
}
```

### Repositories
```java
public interface UserRepository extends JpaRepository<User, Long> {
    
    Optional<User> findByEmail(String email);
    
    @Query("SELECT u FROM User u WHERE u.status = :status")
    List<User> findByStatus(@Param("status") UserStatus status);
    
    boolean existsByEmail(String email);
}
```

### DTOs
```java
// Request DTO with validation
public record CreateUserRequest(
    @NotBlank @Size(max = 100) String name,
    @NotBlank @Email String email,
    @NotNull UserRole role
) {}

// Response DTO
public record UserResponse(
    Long id,
    String name,
    String email,
    UserRole role,
    Instant createdAt
) {}
```

### Entities
```java
@Entity
@Table(name = "users")
@Getter
@Setter
@NoArgsConstructor
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
    
    @Column(nullable = false, unique = true)
    private String email;
    
    @Enumerated(EnumType.STRING)
    private UserStatus status = UserStatus.ACTIVE;
    
    @CreatedDate
    private Instant createdAt;
    
    @LastModifiedDate
    private Instant updatedAt;
}
```

### Exception Handling
```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ErrorResponse handleNotFound(UserNotFoundException ex) {
        return new ErrorResponse(ex.getMessage(), "USER_NOT_FOUND");
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ErrorResponse handleValidation(MethodArgumentNotValidException ex) {
        // Extract and return validation errors
    }
}
```

### Configuration
```java
@Configuration
@EnableJpaAuditing
public class JpaConfig {
}

@ConfigurationProperties(prefix = "app")
public record AppProperties(
    String name,
    SecurityProperties security
) {
    public record SecurityProperties(String jwtSecret, Duration tokenExpiration) {}
}
```

### Testing
```java
@SpringBootTest
@Testcontainers
class UserServiceIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15");

    @Autowired
    private UserService userService;

    @Test
    void createUser_ValidRequest_ReturnsCreatedUser() {
        var request = new CreateUserRequest("John", "john@example.com", UserRole.USER);
        
        var result = userService.create(request);
        
        assertThat(result.name()).isEqualTo("John");
        assertThat(result.id()).isNotNull();
    }
}
```

## Best Practices

- Use constructor injection (Lombok `@RequiredArgsConstructor`)
- Use `@Transactional` at service layer
- Validate input with Bean Validation
- Use DTOs, not entities in controllers
- Document APIs with OpenAPI annotations
- Use profiles for environment config

## Key Guidelines

1. Keep controllers thin - delegate to services
2. Use records for DTOs (immutable)
3. Handle exceptions globally
4. Write integration tests with Testcontainers
5. Use Flyway/Liquibase for migrations
