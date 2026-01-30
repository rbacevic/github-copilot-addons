# Android Kotlin Application

Modern Android application using Kotlin and Jetpack.

## Tech Stack

- **Language**: Kotlin 1.9+
- **Min SDK**: 24 (Android 7.0)
- **Target SDK**: 34 (Android 14)
- **UI**: Jetpack Compose / XML Views
- **Architecture**: MVVM + Clean Architecture
- **DI**: Hilt
- **Async**: Kotlin Coroutines + Flow
- **Network**: Retrofit + OkHttp

## Project Structure

- `app/src/main/kotlin/com/example/`
  - `ui/` - Composables, Screens, Theme
    - `feature/` - Feature modules
  - `viewmodel/` - ViewModels
  - `domain/` - Use cases, domain models
  - `data/` - Data layer
    - `local/` - Room, DataStore
    - `remote/` - API services
    - `repository/` - Repository implementations
  - `di/` - Hilt modules
- `app/src/main/res/` - Resources
- `app/src/test/` - Unit tests
- `app/src/androidTest/` - UI tests

## Development Commands

- **Build**: `./gradlew assembleDebug`
- **Test**: `./gradlew test`
- **Lint**: `./gradlew lint`
- **Detekt**: `./gradlew detekt`

## Before Committing

- Run `./gradlew detekt` for static analysis
- Run `./gradlew test` for unit tests
- Format with ktfmt or ktlint
- Check for compiler warnings

## Coding Guidelines

### Kotlin Style
- Use data classes for DTOs
- Use sealed classes/interfaces for state
- Prefer immutability (val over var)
- Use scope functions appropriately
- Avoid !! operator (handle nulls properly)

### Coroutines & Flow
```kotlin
class UserRepository @Inject constructor(
    private val api: UserApi,
    private val dao: UserDao
) {
    fun getUsers(): Flow<List<User>> = dao.observeAll()
        .map { entities -> entities.map { it.toDomain() } }
    
    suspend fun refreshUsers() {
        val users = api.getUsers()
        dao.insertAll(users.map { it.toEntity() })
    }
}
```

### ViewModel
```kotlin
@HiltViewModel
class UserViewModel @Inject constructor(
    private val getUsersUseCase: GetUsersUseCase,
    private val savedStateHandle: SavedStateHandle
) : ViewModel() {
    
    private val _uiState = MutableStateFlow<UiState>(UiState.Loading)
    val uiState: StateFlow<UiState> = _uiState.asStateFlow()
    
    init {
        loadUsers()
    }
    
    private fun loadUsers() {
        viewModelScope.launch {
            getUsersUseCase()
                .catch { _uiState.value = UiState.Error(it.message) }
                .collect { users -> _uiState.value = UiState.Success(users) }
        }
    }
}

sealed interface UiState {
    data object Loading : UiState
    data class Success(val users: List<User>) : UiState
    data class Error(val message: String?) : UiState
}
```

### Jetpack Compose
```kotlin
@Composable
fun UserScreen(
    viewModel: UserViewModel = hiltViewModel()
) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()
    
    UserScreenContent(
        uiState = uiState,
        onUserClick = viewModel::onUserClick,
        onRetry = viewModel::retry
    )
}

@Composable
private fun UserScreenContent(
    uiState: UiState,
    onUserClick: (User) -> Unit,
    onRetry: () -> Unit,
    modifier: Modifier = Modifier
) {
    when (uiState) {
        is UiState.Loading -> LoadingIndicator()
        is UiState.Success -> UserList(uiState.users, onUserClick)
        is UiState.Error -> ErrorMessage(uiState.message, onRetry)
    }
}
```

### Room Database
```kotlin
@Entity(tableName = "users")
data class UserEntity(
    @PrimaryKey val id: String,
    val name: String,
    val email: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    fun observeAll(): Flow<List<UserEntity>>
    
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(users: List<UserEntity>)
}
```

### Hilt Modules
```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    
    @Provides
    @Singleton
    fun provideOkHttpClient(): OkHttpClient =
        OkHttpClient.Builder()
            .addInterceptor(HttpLoggingInterceptor())
            .build()
    
    @Provides
    @Singleton
    fun provideRetrofit(okHttpClient: OkHttpClient): Retrofit =
        Retrofit.Builder()
            .baseUrl(BuildConfig.API_URL)
            .client(okHttpClient)
            .addConverterFactory(MoshiConverterFactory.create())
            .build()
}
```

### Navigation (Compose)
```kotlin
@Composable
fun AppNavigation(
    navController: NavHostController = rememberNavController()
) {
    NavHost(navController, startDestination = "home") {
        composable("home") {
            HomeScreen(onNavigateToDetail = { id ->
                navController.navigate("detail/$id")
            })
        }
        composable("detail/{userId}") { backStackEntry ->
            val userId = backStackEntry.arguments?.getString("userId")
            DetailScreen(userId = userId)
        }
    }
}
```

## Testing

```kotlin
class UserViewModelTest {
    
    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()
    
    private lateinit var viewModel: UserViewModel
    
    @Mock
    private lateinit var getUsersUseCase: GetUsersUseCase
    
    @Test
    fun `when users loaded successfully, state is Success`() = runTest {
        val users = listOf(User("1", "John"))
        whenever(getUsersUseCase()).thenReturn(flowOf(users))
        
        viewModel = UserViewModel(getUsersUseCase, SavedStateHandle())
        
        viewModel.uiState.test {
            assertEquals(UiState.Loading, awaitItem())
            assertEquals(UiState.Success(users), awaitItem())
        }
    }
}
```

## Key Guidelines

1. Use Kotlin idioms (scope functions, extensions)
2. Prefer Compose for new UI
3. Use Flow for reactive streams
4. Handle all states (loading, success, error)
5. Write testable code with dependency injection
