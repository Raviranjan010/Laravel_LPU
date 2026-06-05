# Unit 3: Controllers, Blade & Advanced Routing

## 📚 Course Outcomes Coverage
*   **CO3**: Develop controllers, utilize Blade templates, and apply advanced routing for modular and structured development.

---

## Controllers

Controllers group related request-handling logic into individual classes. They are stored in `app/Http/Controllers/`.

### 1. Basic Controllers
To create a basic controller:
```bash
php artisan make:controller UserController
```

#### Controller Definition:
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\User;

class UserController extends Controller
{
    public function show($id)
    {
        $user = User::findOrFail($id);
        return view('user.profile', compact('user'));
    }
}
```

#### Routing to Controller:
```php
use App\Http\Controllers\UserController;

Route::get('/user/{id}', [UserController::class, 'show']);
```

### 2. Controller Middleware
You can assign middleware to your controller actions inside route definitions or within the controller's constructor:

```php
class UserController extends Controller
{
    public function __construct()
    {
        // Require auth middleware for all methods in this controller
        $this->middleware('auth');

        // Apply only to specific methods
        $this->middleware('log')->only('show');

        // Apply to all except specific methods
        $this->middleware('subscribed')->except('index');
    }
}
```

### 3. RESTful Resource Controllers
A Resource Controller contains predefined methods to handle all CRUD actions:

| Verb | URI | Action | Route Name | Description |
|------|-----|--------|------------|-------------|
| **GET** | `/photos` | `index` | `photos.index` | Display a list of photos |
| **GET** | `/photos/create` | `create` | `photos.create` | Render the creation form |
| **POST** | `/photos` | `store` | `photos.store` | Save a new photo |
| **GET** | `/photos/{photo}` | `show` | `photos.show` | Display a specific photo |
| **GET** | `/photos/{photo}/edit` | `edit` | `photos.edit` | Render the edit form |
| **PUT/PATCH** | `/photos/{photo}` | `update` | `photos.update` | Update a specific photo |
| **DELETE** | `/photos/{photo}` | `destroy` | `photos.destroy` | Delete a specific photo |

#### Generate Resource Controller:
```bash
php artisan make:controller PhotoController --resource
```

#### Register Resource Route:
```php
Route::resource('photos', PhotoController::class);
```

---

## Blade Templating Engine

Blade is Laravel's clean, robust templating engine.

### 1. Control Structures
*   **Conditionals**:
    ```blade
    @if ($role == 'admin')
        <p>Welcome, Administrator.</p>
    @elseif ($role == 'editor')
        <p>Welcome, Editor.</p>
    @else
        <p>Welcome, User.</p>
    @endif
    ```
*   **Loops**:
    ```blade
    @foreach ($users as $user)
        <li>{{ $user->name }} (Index: {{ $loop->index }})</li>
    @endforeach
    ```
*   **Forelse (Loop with Empty Check)**:
    ```blade
    @forelse ($books as $book)
        <li>{{ $book->title }}</li>
    @empty
        <p>No books available in the database.</p>
    @endforelse
    ```

### 2. Layout Inheritance
Allows you to define a master layout and extend it in child views.

#### Master Layout (`resources/views/layouts/app.blade.php`):
```blade
<!DOCTYPE html>
<html>
<head>
    <title>LPU App - @yield('title')</title>
</head>
<body>
    <header><h1>Header Navigation</h1></header>
    
    <main>
        @yield('content') {{-- Placeholder for child view --}}
    </main>

    <footer><p>Footer Copy 2026</p></footer>
</body>
</html>
```

#### Child View (`resources/views/home.blade.php`):
```blade
@extends('layouts.app') {{-- Extends master layout --}}

@section('title', 'Dashboard')

@section('content')
    <h2>Welcome Home!</h2>
    <p>This content is injected into the yield block.</p>
@endsection
```

---

## Advanced Routing

### 1. Route Groups & Prefixing
Group routes that share attributes (e.g. prefix, middleware, namespaces):
```php
Route::prefix('admin')->middleware(['auth', 'admin'])->group(function () {
    Route::get('/dashboard', [AdminController::class, 'index']); // Matches /admin/dashboard
    Route::get('/settings', [AdminController::class, 'settings']); // Matches /admin/settings
});
```

### 2. Domain Routing
Handle subdomains dynamically:
```php
Route::domain('{account}.lpu.com')->group(function () {
    Route::get('/profile/{id}', function ($account, $id) {
        return "Account Subdomain: " . $account . " for Profile: " . $id;
    });
});
```

### 3. Route Model Binding
Automatically injects model instances directly into routes:
*   **Implicit Binding**: The parameter matches the model variable name:
    ```php
    Route::get('/api/users/{user}', function (App\Models\User $user) {
        return $user->email; // Automatically queries database by ID
    });
    ```

---

## URL Generation

Laravel provides helper functions to generate clean, dynamic URLs.

### 1. Basic URL Helpers
*   `url('/')` -> `http://localhost:8000/`
*   `url()->current()` -> Current URL path without parameters.
*   `url()->full()` -> Current URL path with query string.
*   `url()->previous()` -> Previous URL the request redirected from.

### 2. Shortcuts
*   **Named Route URLs**:
    ```php
    $url = route('profile.show', ['id' => 1]); // http://localhost:8000/profile/1
    ```
*   **Controller Action URLs**:
    ```php
    $url = action([UserController::class, 'show'], ['id' => 1]);
    ```
*   **Asset URLs**:
    ```php
    $url = asset('css/styles.css'); // http://localhost:8000/css/styles.css
    ```
