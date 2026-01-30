# Android Java Application

Native Android application using Java.

## Tech Stack

- **Language**: Java 17
- **Min SDK**: 24 (Android 7.0)
- **Target SDK**: 34 (Android 14)
- **Build**: Gradle (Kotlin DSL)
- **Architecture**: MVVM + Repository
- **DI**: Hilt
- **Async**: RxJava / LiveData

## Project Structure

- `app/src/main/java/com/example/`
  - `ui/` - Activities, Fragments, Adapters
    - `feature/` - Feature-based packages
  - `viewmodel/` - ViewModels
  - `repository/` - Data repositories
  - `data/` - Data layer
    - `local/` - Room database, DAOs
    - `remote/` - Retrofit services
    - `model/` - Data models
  - `di/` - Hilt modules
  - `util/` - Utility classes
- `app/src/main/res/` - Resources
- `app/src/test/` - Unit tests
- `app/src/androidTest/` - Instrumented tests

## Development Commands

- **Build**: `./gradlew assembleDebug`
- **Test**: `./gradlew test`
- **Lint**: `./gradlew lint`
- **Install**: `./gradlew installDebug`

## Before Committing

- Run `./gradlew lint` for code analysis
- Run `./gradlew test` for unit tests
- Check for any TODO comments
- Review resource naming

## Coding Guidelines

### Activities
```java
@AndroidEntryPoint
public class MainActivity extends AppCompatActivity {
    
    private ActivityMainBinding binding;
    
    @Inject
    MainViewModel viewModel;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());
        
        setupUI();
        observeData();
    }
    
    private void setupUI() {
        binding.button.setOnClickListener(v -> viewModel.onButtonClick());
    }
    
    private void observeData() {
        viewModel.getUserData().observe(this, this::updateUI);
    }
}
```

### ViewModels
```java
@HiltViewModel
public class MainViewModel extends ViewModel {
    
    private final UserRepository userRepository;
    private final MutableLiveData<User> userData = new MutableLiveData<>();
    
    @Inject
    public MainViewModel(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public LiveData<User> getUserData() {
        return userData;
    }
    
    public void loadUser(String userId) {
        userRepository.getUser(userId)
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe(userData::setValue, this::handleError);
    }
}
```

### Repository Pattern
```java
@Singleton
public class UserRepository {
    
    private final UserDao userDao;
    private final UserApiService apiService;
    
    @Inject
    public UserRepository(UserDao userDao, UserApiService apiService) {
        this.userDao = userDao;
        this.apiService = apiService;
    }
    
    public Single<User> getUser(String userId) {
        return apiService.getUser(userId)
            .doOnSuccess(userDao::insert)
            .onErrorResumeNext(error -> userDao.getById(userId));
    }
}
```

### Room Database
```java
@Entity(tableName = "users")
public class UserEntity {
    @PrimaryKey
    @NonNull
    public String id;
    public String name;
    public String email;
}

@Dao
public interface UserDao {
    @Query("SELECT * FROM users WHERE id = :userId")
    Single<UserEntity> getById(String userId);
    
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    void insert(UserEntity user);
}
```

### Retrofit Service
```java
public interface UserApiService {
    @GET("users/{id}")
    Single<User> getUser(@Path("id") String userId);
    
    @POST("users")
    Single<User> createUser(@Body CreateUserRequest request);
}
```

### Hilt Modules
```java
@Module
@InstallIn(SingletonComponent.class)
public abstract class RepositoryModule {
    
    @Binds
    @Singleton
    abstract UserRepository bindUserRepository(UserRepositoryImpl impl);
}

@Module
@InstallIn(SingletonComponent.class)
public class NetworkModule {
    
    @Provides
    @Singleton
    Retrofit provideRetrofit() {
        return new Retrofit.Builder()
            .baseUrl(BuildConfig.API_BASE_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .addCallAdapterFactory(RxJava3CallAdapterFactory.create())
            .build();
    }
}
```

### View Binding
- Always use View Binding (not findViewById)
- Null binding in onDestroyView for Fragments
- Access views through binding object

### Naming Conventions
- Layouts: `activity_main.xml`, `fragment_home.xml`, `item_user.xml`
- IDs: `snake_case` (button_submit, text_title)
- Drawables: `ic_` prefix for icons, `bg_` for backgrounds
- Strings: Descriptive keys (screen_home_title)

## Best Practices

- Use View Binding, not findViewById
- Handle configuration changes properly
- Use ViewModel for UI state
- Implement proper lifecycle handling
- Handle back navigation correctly

## Testing

```java
@RunWith(MockitoJUnitRunner.class)
public class MainViewModelTest {
    
    @Mock
    UserRepository userRepository;
    
    private MainViewModel viewModel;
    
    @Before
    public void setup() {
        viewModel = new MainViewModel(userRepository);
    }
    
    @Test
    public void loadUser_Success_UpdatesLiveData() {
        User expected = new User("1", "John");
        when(userRepository.getUser("1")).thenReturn(Single.just(expected));
        
        viewModel.loadUser("1");
        
        assertEquals(expected, viewModel.getUserData().getValue());
    }
}
```

## Key Guidelines

1. Use MVVM architecture consistently
2. Keep Activities/Fragments lean
3. Handle errors gracefully with user feedback
4. Support different screen sizes
5. Follow Material Design guidelines
