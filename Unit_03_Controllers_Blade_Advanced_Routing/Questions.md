# Unit 3 Question Bank: Controllers, Blade & Advanced Routing

## 2-Mark Questions

### Q1. What is a Resource Controller? How do you create one using Artisan?
**Answer:** A Resource Controller contains default methods for performing CRUD operations. It is generated via:
```bash
php artisan make:controller PostController --resource
```

### Q2. Explain the difference between `@yield` and `@section` in Blade.
**Answer:**
*   `@yield('placeholder')` is used in a layout file to mark the location where child content will be injected.
*   `@section('placeholder') ... @endsection` is used in child templates to define the content block injected into the corresponding `@yield`.

### Q3. Write the syntax for Route Model Binding in a Laravel route.
**Answer:** Type-hint the model variable inside the route closure or controller method:
```php
Route::get('/users/{user}', function (App\Models\User $user) {
    return $user->name; // User instance is resolved by ID automatically
});
```

### Q4. How do you apply middleware inside a controller constructor?
**Answer:** By calling the `middleware()` method inside the constructor:
```php
public function __construct() {
    $this->middleware('auth')->only('store');
}
```

### Q5. What is the purpose of the `asset()` helper in Blade?
**Answer:** It generates an absolute URL to a file stored inside the public folder (e.g. CSS, JS, image assets) matching the application's root host protocol (HTTP or HTTPS).

---

## 5-Mark Questions

### Q6. Detail the lifecycle of a Resource Route in Laravel, listing all seven registered paths, HTTP verbs, and controller methods.
**Answer:**
When registering a resource route using `Route::resource('posts', PostController::class);`, Laravel automatically registers the following routes:

| HTTP Verb | URL Path | Controller Method | Named Route | Description |
|-----------|----------|-------------------|-------------|-------------|
| **GET** | `/posts` | `index` | `posts.index` | Display list of posts. |
| **GET** | `/posts/create` | `create` | `posts.create` | Display form to create new post. |
| **POST** | `/posts` | `store` | `posts.store` | Save submitted post in database. |
| **GET** | `/posts/{post}` | `show` | `posts.show` | Display single post details. |
| **GET** | `/posts/{post}/edit` | `edit` | `posts.edit` | Display edit form containing data. |
| **PUT/PATCH** | `/posts/{post}` | `update` | `posts.update` | Update post details in database. |
| **DELETE** | `/posts/{post}` | `destroy` | `posts.destroy` | Delete post from database. |

### Q7. Explain how Template Inheritance works in Blade. Write basic code layouts illustrating a master template and a child template.
**Answer:**
Template inheritance allows developers to define a base HTML wrapper layout once (header, footer, nav) and extend it in dynamic sub-pages.

#### Master Layout (`resources/views/layouts/base.blade.php`):
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Application - @yield('title', 'LPU')</title>
</head>
<body>
    <nav>Navigation Bar Links</nav>

    <div class="content">
        @yield('main_content')
    </div>

    <footer>Footer copyright info</footer>
</body>
</html>
```

#### Child Template (`resources/views/dashboard.blade.php`):
```blade
@extends('layouts.base')

@section('title', 'Admin Dashboard')

@section('main_content')
    <h1>Dashboard Metrics</h1>
    <p>Welcome to the administration portal.</p>
@endsection
```

### Q8. Detail how Route Groups, Prefixing, and Subdomain Routing are structured in Laravel. Provide code examples.
**Answer:**
*   **Route Groups**: Group multiple routes that share configuration attributes like middleware, namespaces, or prefixes.
*   **Route Prefixing**: Appends a common prefix to all group URIs.
*   **Subdomain/Domain Routing**: Scopes routes to respond only when matched to a specific host domain or dynamic subdomain.

#### Code Example:
```php
// 1. Group with Prefix and Middleware
Route::prefix('manager')->middleware('auth')->group(function () {
    Route::get('/dashboard', [ManagerController::class, 'index']); // URI: /manager/dashboard
    Route::get('/reports', [ManagerController::class, 'reports']);   // URI: /manager/reports
});

// 2. Subdomain Routing Group
Route::domain('{tenant}.lpu.com')->group(function () {
    Route::get('/courses', function ($tenant) {
        return "List of courses for Tenant: " . $tenant;
    });
});
```

---

## 10-Mark Questions

### Q9. Discuss the Blade Templating Engine. Write a detailed analysis explaining compilation mechanics, control structures (`@if`, `@foreach`, `@forelse`), layout inheritance directives (`@extends`, `@yield`, `@section`, `@stack`, `@push`), and assets generation helpers.
**Answer:**
Blade is Laravel's clean, high-performance templating engine. Unlike other template systems, Blade does not restrict developers from writing raw PHP code inside views. Blade views compile down into raw PHP cache files inside `storage/framework/views/`, meaning there is zero performance overhead.

#### 1. Blade Compilation Mechanics:
When a client requests a page, the Blade parser compiles Blade tags (e.g. `{{ $name }}` or `@if`) into PHP syntax (e.g. `<?php echo e($name); ?>` or `<?php if(...): ?>`). It caches the compiled file. On subsequent requests, Laravel serves the compiled cache file directly, re-compiling only if the original `.blade.php` file is updated.

#### 2. Core Control Structures:
*   **`@if / @elseif / @else / @endif`**: Evaluates conditional blocks:
    ```blade
    @if(count($records) > 10)
        <span class="badge bg-danger">Critical Size</span>
    @else
        <span>Normal</span>
    @endif
    ```
*   **`@foreach / @endforeach`**: Loops over iterable arrays/collections. Exposes a `$loop` helper class:
    ```blade
    @foreach($products as $product)
        <li>{{ $product->name }} - Iteration: {{ $loop->iteration }}</li>
    @endforeach
    ```
*   **`@forelse / @empty / @endforelse`**: Shorthand combining loop initialization with array count checks. If the collection is empty, it renders the `@empty` block:
    ```blade
    @forelse($tasks as $task)
        <li>{{ $task->title }}</li>
    @empty
        <p>No active tasks assigned.</p>
    @endforelse
    ```

#### 3. Layout Inheritance and Stack Directives:
*   **`@extends`**: Declares which parent layout wrapper this view inherits.
*   **`@yield`**: Marks a placeholder in the layout for content injection.
*   **`@section / @endsection`**: Groups page-specific markup to be injected into a layout's yield placeholder.
*   **`@stack`**: Declares a dynamic script or stylesheet injection block in the layout:
    ```blade
    {{-- In Master Layout --}}
    @stack('scripts')
    ```
*   **`@push / @endpush`**: Pushes content into a stack. This allows individual child pages to load custom JavaScript or CSS dependencies dynamically:
    ```blade
    {{-- In Child view --}}
    @push('scripts')
        <script src="https://cdn.example.com/chart.js"></script>
    @endpush
    ```

#### 4. Assets & URL Generation Helpers:
*   `asset('path')` generates URLs to public resources using HTTP/HTTPS.
*   `route('name')` resolves names of defined routes to absolute URL strings.
*   `secure_asset('path')` explicitly forces HTTPS protocol asset retrieval.
