# Scala Application

Functional Scala application following best practices.

## Tech Stack

- **Language**: Scala 3 (or 2.13)
- **Build**: sbt
- **Testing**: ScalaTest / MUnit
- **HTTP**: http4s / Tapir
- **Effects**: Cats Effect / ZIO
- **JSON**: Circe

## Project Structure

- `src/main/scala/` - Source code
  - `domain/` - Domain models and business logic
  - `infrastructure/` - External integrations
  - `application/` - Application services
  - `api/` - HTTP routes and endpoints
- `src/test/scala/` - Test files
- `project/` - sbt configuration

## Development Commands

- **Compile**: `sbt compile`
- **Test**: `sbt test`
- **Run**: `sbt run`
- **Console**: `sbt console`
- **Format**: `sbt scalafmtAll`
- **Coverage**: `sbt coverage test coverageReport`

## Before Committing

- Run `sbt scalafmtAll` for formatting
- Run `sbt compile` to verify compilation
- Run `sbt test` to verify tests pass

## Coding Guidelines

### Scala 3 Style
- Use `given`/`using` over `implicit`
- Use extension methods over implicit classes
- Use enums over sealed trait hierarchies
- Use union types where appropriate
- Prefer toplevel definitions

### Scala 2.13 Style
- Use implicit classes for extensions
- Use sealed trait + case class for ADTs
- Prefer implicit parameters for context

### Functional Programming
- Prefer immutability
- Use pure functions
- Avoid side effects in business logic
- Use monads for effect management
- Compose small functions

### Case Classes
```scala
// Scala 3
case class User(
  id: UserId,
  name: String,
  email: Email,
  createdAt: Instant
)

// Opaque types for type safety
opaque type UserId = String
object UserId:
  def apply(value: String): UserId = value
  extension (id: UserId) def value: String = id
```

### ADTs (Algebraic Data Types)
```scala
// Scala 3
enum PaymentStatus:
  case Pending
  case Completed(transactionId: String)
  case Failed(reason: String)

// Scala 2
sealed trait PaymentStatus
object PaymentStatus {
  case object Pending extends PaymentStatus
  case class Completed(transactionId: String) extends PaymentStatus
  case class Failed(reason: String) extends PaymentStatus
}
```

### Error Handling
- Use `Either[Error, A]` for expected failures
- Use `Option` for optional values
- Use effect types for side effects
- Avoid throwing exceptions
- Define domain error types

### Collections
- Prefer immutable collections
- Use `map`, `flatMap`, `filter` over loops
- Use `foldLeft` for accumulation
- Be mindful of performance for large collections

### Effects (Cats Effect / ZIO)
```scala
// Cats Effect
def fetchUser(id: UserId): IO[User] = 
  IO.blocking(repository.find(id))
    .flatMap(IO.fromOption(_)(UserNotFound(id)))

// ZIO
def fetchUser(id: UserId): ZIO[UserRepository, UserError, User] =
  ZIO.serviceWithZIO[UserRepository](_.find(id))
```

### Type Classes
```scala
// Scala 3
trait JsonEncoder[A]:
  def encode(value: A): Json
  
given JsonEncoder[User] with
  def encode(user: User): Json = ???
```

### Implicits / Givens
- Keep implicit scope small
- Use explicit imports for clarity
- Document implicit requirements
- Prefer type class pattern

## HTTP Endpoints (http4s/Tapir)
```scala
val userEndpoint = endpoint
  .get
  .in("users" / path[UserId])
  .out(jsonBody[User])
  .errorOut(jsonBody[ApiError])
```

## Testing Standards

- Use ScalaTest or MUnit
- Property-based testing with ScalaCheck
- Test pure functions directly
- Use test fixtures for setup
- Mock external dependencies

## Key Guidelines

1. Favor immutability and pure functions
2. Use the type system to prevent errors
3. Make illegal states unrepresentable
4. Handle errors explicitly with types
5. Prefer composition over inheritance
