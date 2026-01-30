# Blazor Application

Interactive web application built with Blazor and .NET.

## Tech Stack

- **Framework**: Blazor WebAssembly / Blazor Server (.NET 8)
- **Language**: C# 12
- **Styling**: CSS Isolation + Bootstrap / Tailwind
- **State**: Fluxor / Built-in cascading parameters
- **Testing**: bUnit + xUnit

## Project Structure

- `Client/` - Blazor WebAssembly project (if WASM)
  - `Pages/` - Routable page components
  - `Components/` - Reusable UI components
  - `Layout/` - Layout components
  - `Services/` - Client-side services
  - `wwwroot/` - Static assets
- `Server/` - ASP.NET Core host (if hosted WASM)
- `Shared/` - Shared models and utilities
- `Tests/` - bUnit test project

## Development Commands

- **Run**: `dotnet watch run`
- **Build**: `dotnet build`
- **Test**: `dotnet test`
- **Publish**: `dotnet publish -c Release`

## Before Committing

- Run `dotnet format` for consistent formatting
- Run `dotnet build` to verify compilation
- Run `dotnet test` to verify tests pass
- Check browser console for runtime errors

## Coding Guidelines

### Component Structure
- One component per file
- File name matches component name (PascalCase.razor)
- Use code-behind files for complex logic (.razor.cs)
- Keep components focused and small

### Component Patterns
```razor
@* Parameters at top *@
[Parameter] public string Title { get; set; } = string.Empty;
[Parameter] public EventCallback<string> OnChange { get; set; }

@* Inject services *@
@inject IUserService UserService
@inject NavigationManager Navigation

@* Cascading parameters *@
[CascadingParameter] public Task<AuthenticationState> AuthState { get; set; }
```

### Lifecycle Methods
- Use `OnInitializedAsync` for initial data loading
- Use `OnParametersSetAsync` when parameters change
- Implement `IDisposable` for cleanup
- Avoid heavy work in `OnAfterRender`

### State Management
- Use cascading parameters for theme/auth state
- Use Fluxor for complex shared state
- Avoid excessive use of `StateHasChanged()`
- Use `@key` directive for list rendering optimization

### Event Handling
- Use `EventCallback<T>` for component events
- Prevent default with `@onclick:preventDefault`
- Use async event handlers when needed
- Debounce rapid-fire events (search inputs)

### Forms and Validation
- Use `EditForm` with `DataAnnotationsValidator`
- Create custom validators for complex rules
- Show validation summary and field-level errors
- Disable submit button during processing

### CSS Isolation
- Use `.razor.css` files for component styles
- Styles are automatically scoped
- Use `::deep` for child component styling
- Keep global styles in wwwroot/css

### JavaScript Interop
- Minimize JS interop calls
- Use `IJSRuntime` for JS calls
- Batch multiple operations when possible
- Handle JS errors gracefully

## Performance

- Use virtualization for large lists (`<Virtualize>`)
- Implement `ShouldRender()` to prevent unnecessary renders
- Use streaming rendering for slow data
- Lazy load assemblies for large apps

## Testing Standards

- Use bUnit for component testing
- Test component rendering and interactions
- Mock services with dependency injection
- Test parameter changes and events

## Key Guidelines

1. Prefer Server-Side Rendering for SEO-critical pages
2. Use component parameters for configuration
3. Handle loading and error states in UI
4. Implement proper disposal for subscriptions
5. Use authentication state for protected routes
