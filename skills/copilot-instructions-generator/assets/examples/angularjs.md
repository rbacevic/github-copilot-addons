# AngularJS Application (Legacy)

Legacy AngularJS 1.x application maintenance guidelines.

## Tech Stack

- **Framework**: AngularJS 1.8.x
- **Build**: Gulp / Grunt / Webpack
- **Testing**: Karma + Jasmine
- **Package Manager**: npm / Bower

## Project Structure

- `app/` - Application code
  - `components/` - Component directives
  - `services/` - Services and factories
  - `controllers/` - Controllers
  - `directives/` - Custom directives
  - `filters/` - Custom filters
  - `views/` - HTML templates
- `assets/` - Static assets
- `tests/` - Test files

## Coding Guidelines

### Modules

```javascript
// Define module with dependencies
angular.module('myApp', [
  'ngRoute',
  'ngResource',
  'myApp.components',
  'myApp.services'
]);

// Separate module files
angular.module('myApp.services', []);
```

### Controllers

```javascript
// Use controllerAs syntax
angular
  .module('myApp')
  .controller('UserController', UserController);

UserController.$inject = ['UserService', '$log'];

function UserController(UserService, $log) {
  var vm = this;
  
  vm.users = [];
  vm.loading = false;
  vm.loadUsers = loadUsers;
  
  activate();
  
  function activate() {
    loadUsers();
  }
  
  function loadUsers() {
    vm.loading = true;
    UserService.getAll()
      .then(function(users) {
        vm.users = users;
      })
      .catch(function(error) {
        $log.error('Failed to load users', error);
      })
      .finally(function() {
        vm.loading = false;
      });
  }
}
```

### Services

```javascript
angular
  .module('myApp.services')
  .factory('UserService', UserService);

UserService.$inject = ['$http', '$q', 'API_URL'];

function UserService($http, $q, API_URL) {
  var service = {
    getAll: getAll,
    getById: getById,
    create: create
  };
  return service;
  
  function getAll() {
    return $http.get(API_URL + '/users')
      .then(function(response) {
        return response.data;
      });
  }
  
  function getById(id) {
    return $http.get(API_URL + '/users/' + id)
      .then(function(response) {
        return response.data;
      });
  }
}
```

### Components

```javascript
angular
  .module('myApp.components')
  .component('userCard', {
    templateUrl: 'components/user-card/user-card.html',
    bindings: {
      user: '<',
      onSelect: '&'
    },
    controller: UserCardController
  });

function UserCardController() {
  var $ctrl = this;
  
  $ctrl.$onInit = function() {
    // Initialization logic
  };
  
  $ctrl.$onChanges = function(changes) {
    if (changes.user) {
      // Handle user changes
    }
  };
}
```

### Directives

```javascript
angular
  .module('myApp.directives')
  .directive('focusOn', focusOn);

focusOn.$inject = ['$timeout'];

function focusOn($timeout) {
  return {
    restrict: 'A',
    link: function(scope, element, attrs) {
      scope.$watch(attrs.focusOn, function(value) {
        if (value) {
          $timeout(function() {
            element[0].focus();
          });
        }
      });
    }
  };
}
```

### Best Practices

- Use `controllerAs` syntax (not $scope)
- Use component architecture where possible
- Explicit dependency injection with $inject
- One component/service per file
- Use promises, avoid callbacks

## Migration Considerations

- Consider migrating to Angular (2+)
- Use ngUpgrade for hybrid apps
- Gradually convert to components
- TypeScript can be added incrementally

## Key Guidelines

1. Always use explicit $inject annotation
2. Prefer components over directives
3. Use one-way bindings (<) when possible
4. Avoid watchers - use one-time bindings (::)
5. Keep controllers thin, logic in services
