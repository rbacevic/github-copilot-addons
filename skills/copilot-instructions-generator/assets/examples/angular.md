# Angular Application

Modern Angular application following official best practices.

## Tech Stack

- **Framework**: Angular 17+
- **Language**: TypeScript 5.x
- **Build**: Angular CLI / Nx
- **Testing**: Jest / Karma + Jasmine
- **Styling**: SCSS + Angular Material

## Project Structure

- `src/app/` - Application code
  - `core/` - Singleton services, guards, interceptors
  - `shared/` - Shared components, directives, pipes
  - `features/` - Feature modules
    - `feature-name/`
      - `components/`
      - `services/`
      - `models/`
      - `feature-name.routes.ts`
  - `app.config.ts` - App configuration
  - `app.routes.ts` - Root routes
- `src/assets/` - Static assets
- `src/environments/` - Environment configs

## Development Commands

- **Serve**: `ng serve`
- **Build**: `ng build`
- **Test**: `ng test`
- **E2E**: `ng e2e`
- **Lint**: `ng lint`
- **Generate**: `ng generate component feature/component-name`

## Coding Guidelines

### Standalone Components (Angular 17+)

```typescript
@Component({
  selector: 'app-user-list',
  standalone: true,
  imports: [CommonModule, RouterLink, UserCardComponent],
  templateUrl: './user-list.component.html',
  styleUrl: './user-list.component.scss',
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class UserListComponent {
  private readonly userService = inject(UserService);
  
  users = signal<User[]>([]);
  loading = signal(false);
  
  constructor() {
    this.loadUsers();
  }
  
  private async loadUsers() {
    this.loading.set(true);
    try {
      const users = await firstValueFrom(this.userService.getAll());
      this.users.set(users);
    } finally {
      this.loading.set(false);
    }
  }
}
```

### Signals (Angular 17+)

```typescript
@Component({...})
export class CounterComponent {
  count = signal(0);
  doubleCount = computed(() => this.count() * 2);
  
  increment() {
    this.count.update(c => c + 1);
  }
}
```

### Services

```typescript
@Injectable({ providedIn: 'root' })
export class UserService {
  private readonly http = inject(HttpClient);
  private readonly apiUrl = inject(API_URL);
  
  getAll(): Observable<User[]> {
    return this.http.get<User[]>(`${this.apiUrl}/users`);
  }
  
  getById(id: string): Observable<User> {
    return this.http.get<User>(`${this.apiUrl}/users/${id}`);
  }
  
  create(user: CreateUserDto): Observable<User> {
    return this.http.post<User>(`${this.apiUrl}/users`, user);
  }
}
```

### Routing (Standalone)

```typescript
// app.routes.ts
export const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', loadComponent: () => 
    import('./features/home/home.component').then(m => m.HomeComponent) 
  },
  { path: 'users', loadChildren: () => 
    import('./features/users/users.routes').then(m => m.USER_ROUTES) 
  },
];

// users.routes.ts
export const USER_ROUTES: Routes = [
  { path: '', component: UserListComponent },
  { path: ':id', component: UserDetailComponent },
];
```

### HTTP Interceptors

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authService = inject(AuthService);
  const token = authService.getToken();
  
  if (token) {
    req = req.clone({
      setHeaders: { Authorization: `Bearer ${token}` }
    });
  }
  
  return next(req);
};

// In app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(withInterceptors([authInterceptor])),
    provideRouter(routes),
  ]
};
```

### Forms

```typescript
// Reactive Forms
@Component({...})
export class UserFormComponent {
  private readonly fb = inject(FormBuilder);
  
  form = this.fb.group({
    name: ['', [Validators.required, Validators.minLength(2)]],
    email: ['', [Validators.required, Validators.email]],
    role: ['user', Validators.required],
  });
  
  onSubmit() {
    if (this.form.valid) {
      console.log(this.form.value);
    }
  }
}
```

### Templates

```html
<!-- Control flow (Angular 17+) -->
@if (loading()) {
  <app-spinner />
} @else if (users().length === 0) {
  <p>No users found</p>
} @else {
  <ul>
    @for (user of users(); track user.id) {
      <li>{{ user.name }}</li>
    }
  </ul>
}

<!-- Defer loading -->
@defer (on viewport) {
  <app-heavy-component />
} @placeholder {
  <p>Loading...</p>
}
```

### Testing

```typescript
describe('UserService', () => {
  let service: UserService;
  let httpMock: HttpTestingController;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [
        provideHttpClient(),
        provideHttpClientTesting(),
      ]
    });
    service = TestBed.inject(UserService);
    httpMock = TestBed.inject(HttpTestingController);
  });
  
  it('should fetch users', () => {
    const mockUsers = [{ id: '1', name: 'John' }];
    
    service.getAll().subscribe(users => {
      expect(users).toEqual(mockUsers);
    });
    
    const req = httpMock.expectOne('/api/users');
    req.flush(mockUsers);
  });
});
```

## Key Guidelines

1. Use standalone components (default in Angular 17+)
2. Prefer signals over RxJS for component state
3. Use OnPush change detection
4. Lazy load feature routes
5. Use inject() function over constructor injection
