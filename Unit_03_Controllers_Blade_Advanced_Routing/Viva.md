# Unit 3 Viva Questions & Answers

### Q1: What Artisan command generates a resource controller?
**Answer:**
```bash
php artisan make:controller BookController --resource
```

### Q2: How do you register a resource route in `web.php`?
**Answer:**
```php
Route::resource('books', BookController::class);
```

### Q3: What is the difference between `@yield` and `@stack` in Blade layout design?
**Answer:**
*   `@yield` defines a placeholder that receives a single, complete content block from a child view's `@section`.
*   `@stack` defines a location where multiple child views can append contents repeatedly using `@push` directives (useful for stacking scripts/CSS styles).

### Q4: How does Implicit Route Model Binding retrieve a record from the database?
**Answer:** It matches the type-hinted model parameter variable name to the route segment name (e.g. `{user}` route parameter matches `User $user` controller variable). Laravel automatically executes a query finding the record by its primary key ID. If not found, it throws a 404 error.

### Q5: How do you exclude a middleware from affecting specific methods inside a controller?
**Answer:** By using the `except()` modifier on the middleware call in the constructor:
```php
$this->middleware('auth')->except(['index', 'show']);
```

### Q6: Where does Blade write compiled cache template files?
**Answer:** In the `storage/framework/views/` directory.

### Q7: If a controller method uses `return view('admin.dashboard')`, what is the exact directory path of the view file?
**Answer:** `resources/views/admin/dashboard.blade.php`.

### Q8: What is the `$loop` variable in Blade?
**Answer:** It is a special variable instantiated inside a `@foreach` loop that provides metadata about the loop's status (e.g., `$loop->first`, `$loop->last`, `$loop->iteration`, `$loop->index`).

### Q9: Write the route group helper code to prefix a set of routes with `/api/v1`.
**Answer:**
```php
Route::prefix('api/v1')->group(function () {
    // Routes defined here match /api/v1/...
});
```

### Q10: How do you generate a secure HTTPS URL for an asset in Blade?
**Answer:** Using the `secure_asset()` helper function:
```blade
<link rel="stylesheet" href="{{ secure_asset('css/app.css') }}">
```

### Q11: What HTTP methods are triggered by a resource route when deleting and updating?
**Answer:**
*   **Update**: `PUT` or `PATCH` method.
*   **Delete**: `DELETE` method.

### Q12: Why does Laravel return a "MethodNotAllowedHttpException" error?
**Answer:** It occurs when a route exists for a given URL path, but the request was sent using an HTTP verb that is not registered for that path (e.g. submitting a POST request to a route defined with `Route::get`).

### Q13: What does `@forelse` do?
**Answer:** It initiates a loop over a collection. If the collection is not empty, it behaves exactly like a `@foreach` loop. If the collection is empty, it executes the `@empty` block instead.

### Q14: How do you pass arguments into route groups?
**Answer:** By wrapping attributes in fluent method chains or an options array:
```php
Route::middleware(['auth', 'admin'])->group(function() { ... });
```

### Q15: What command lists all registered routes in a project?
**Answer:**
```bash
php artisan route:list
```
