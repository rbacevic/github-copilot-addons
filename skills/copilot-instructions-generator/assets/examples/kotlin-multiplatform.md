# Kotlin Multiplatform Application

Cross-platform application using Kotlin Multiplatform (KMP).

## Tech Stack

- **Language**: Kotlin 1.9+
- **Platforms**: Android, iOS, Desktop, Web
- **UI**: Compose Multiplatform
- **Networking**: Ktor Client
- **Serialization**: Kotlinx Serialization
- **DI**: Koin
- **Async**: Kotlin Coroutines

## Project Structure

```
project/
├── composeApp/           # Compose Multiplatform UI
│   ├── commonMain/       # Shared UI code
│   ├── androidMain/      # Android-specific UI
│   ├── iosMain/          # iOS-specific UI
│   └── desktopMain/      # Desktop-specific UI
├── shared/               # Shared business logic
│   ├── commonMain/       # Shared code
│   │   ├── domain/       # Domain models, use cases
│   │   ├── data/         # Repositories, data sources
│   │   └── di/           # Koin modules
│   ├── androidMain/      # Android implementations
│   └── iosMain/          # iOS implementations
├── iosApp/               # iOS application entry
└── build.gradle.kts      # Root build configuration
```

## Development Commands

- **Build all**: `./gradlew build`
- **Run Android**: `./gradlew :composeApp:installDebug`
- **Run Desktop**: `./gradlew :composeApp:run`
- **Run iOS**: Open in Xcode and run
- **Test shared**: `./gradlew :shared:allTests`

## Before Committing

- Run `./gradlew build` to verify all platforms
- Run `./gradlew allTests` for tests
- Check iOS build in Xcode
- Format with ktfmt

## Coding Guidelines

### Shared Code (commonMain)
```kotlin
// Domain model
data class User(
    val id: String,
    val name: String,
    val email: String
)

// Repository interface
interface UserRepository {
    suspend fun getUsers(): List<User>
    fun observeUsers(): Flow<List<User>>
}
```

### Platform-Specific Code
```kotlin
// In commonMain - expect declaration
expect class Platform() {
    val name: String
}

// In androidMain - actual implementation
actual class Platform actual constructor() {
    actual val name: String = "Android ${Build.VERSION.SDK_INT}"
}

// In iosMain - actual implementation
actual class Platform actual constructor() {
    actual val name: String = UIDevice.currentDevice.systemName()
}
```

### Ktor Client
```kotlin
// Shared HTTP client configuration
val httpClient = HttpClient {
    install(ContentNegotiation) {
        json(Json {
            ignoreUnknownKeys = true
            isLenient = true
        })
    }
    install(Logging) {
        level = LogLevel.INFO
    }
}

class UserApi(private val client: HttpClient) {
    suspend fun getUsers(): List<UserDto> =
        client.get("https://api.example.com/users").body()
}
```

### Kotlinx Serialization
```kotlin
@Serializable
data class UserDto(
    val id: String,
    val name: String,
    val email: String
) {
    fun toDomain() = User(id, name, email)
}
```

### Koin Dependency Injection
```kotlin
// Shared module
val sharedModule = module {
    single { HttpClient { /* config */ } }
    single { UserApi(get()) }
    single<UserRepository> { UserRepositoryImpl(get()) }
    factory { GetUsersUseCase(get()) }
}

// Platform-specific module (Android)
val androidModule = module {
    single { AndroidPlatform() }
}

// Initialize in Application
startKoin {
    modules(sharedModule, platformModule)
}
```

### Compose Multiplatform UI
```kotlin
@Composable
fun App() {
    MaterialTheme {
        var users by remember { mutableStateOf<List<User>>(emptyList()) }
        val scope = rememberCoroutineScope()
        val repository = koinInject<UserRepository>()
        
        LaunchedEffect(Unit) {
            repository.observeUsers().collect { users = it }
        }
        
        UserList(users = users)
    }
}

@Composable
fun UserList(users: List<User>) {
    LazyColumn {
        items(users) { user ->
            UserItem(user)
        }
    }
}
```

### ViewModel Pattern
```kotlin
class UserViewModel(
    private val getUsersUseCase: GetUsersUseCase
) : ViewModel() {
    
    private val _state = MutableStateFlow<UiState>(UiState.Loading)
    val state: StateFlow<UiState> = _state.asStateFlow()
    
    init {
        loadUsers()
    }
    
    private fun loadUsers() {
        viewModelScope.launch {
            try {
                val users = getUsersUseCase()
                _state.value = UiState.Success(users)
            } catch (e: Exception) {
                _state.value = UiState.Error(e.message)
            }
        }
    }
}
```

### Coroutines Dispatchers
```kotlin
// In commonMain
expect val ioDispatcher: CoroutineDispatcher

// In androidMain
actual val ioDispatcher: CoroutineDispatcher = Dispatchers.IO

// In iosMain
actual val ioDispatcher: CoroutineDispatcher = Dispatchers.Default
```

## Platform Considerations

### iOS
- Use Kotlin/Native memory model
- Handle iOS lifecycle properly
- Test on actual devices

### Android
- Follow Android best practices
- Use AndroidX libraries
- Handle configuration changes

### Desktop
- Handle window management
- Support keyboard shortcuts
- Consider multiple windows

## Testing
```kotlin
class UserRepositoryTest {
    
    @Test
    fun `getUsers returns mapped domain models`() = runTest {
        val api = mockk<UserApi> {
            coEvery { getUsers() } returns listOf(
                UserDto("1", "John", "john@example.com")
            )
        }
        val repository = UserRepositoryImpl(api)
        
        val result = repository.getUsers()
        
        assertEquals(1, result.size)
        assertEquals("John", result[0].name)
    }
}
```

## Key Guidelines

1. Maximize shared code in commonMain
2. Use expect/actual for platform specifics
3. Keep UI in composeApp, logic in shared
4. Test shared code thoroughly
5. Handle platform differences gracefully
