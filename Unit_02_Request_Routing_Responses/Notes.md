# Unit 2: Request, Routing & Responses

## 📚 Course Outcomes Coverage
*   **CO2**: Apply routing, handle HTTP requests, and customize responses for seamless Laravel application navigation.

---

## Laravel Request Lifecycle

Understanding how requests flow through Laravel is critical for debugging and writing optimized code.

```
HTTP Request
     │
     ▼
[1. Entry Point]  ───> public/index.php
     │
     ▼
[2. HTTP Kernel]  ───> app/Http/Kernel.php (Loads Global Middleware)
     │
     ▼
[3. Service Providers] ──> app/Providers/ (Registers & Bootstraps Services)
     │
     ▼
[4. Router]       ───> routes/web.php (Matches requested path to Route)
     │
     ▼
[5. Middleware]   ───> app/Http/Middleware/ (Validates Session, CSRF, etc.)
     │
     ▼
[6. Controller]   ───> app/Http/Controllers/ (Processes logic, Queries DB)
     │
     ▼
[7. Response]     ───> Returns HTML / JSON payload back to browser
```

### Detailed Lifecycle Steps

1.  **Entry Point (`public/index.php`)**: The web server (Apache/Nginx) directs all traffic to this file. It loads the Composer autoloader and bootstraps the application by instantiating the Laravel Service Container.
2.  **HTTP Kernel (`app/Http/Kernel.php`)**: The request is passed to the Kernel. The Kernel registers global HTTP middleware (e.g., verifying maintenance mode, trimming strings, encrypting cookies).
3.  **Service Providers**: Service Providers bootstrap all core framework services (Database, Mail, Queue, Validation, Routing). They register bindings in the service container.
4.  **Routing**: The Router matches the incoming URL to a route in `routes/web.php` or `routes/api.php`.
5.  **Route Middleware**: The request passes through route-specific middleware (e.g., `auth`, `VerifyCsrfToken`).
6.  **Controller & Logic**: The controller method executes database queries and passes variables to a Blade template.
7.  **Response Delivery**: The view compiles, and the resulting HTML or JSON is returned to the client browser.

---

## Routing in Laravel

Routing defines the endpoints (URLs) of your web application.

### 1. HTTP Verbs (Routes API)
Laravel supports all standard HTTP verbs:
```php
use Illuminate\Support\Facades\Route;

Route::get('/items', [ItemController::class, 'index']);      // Retrieve items
Route::post('/items', [ItemController::class, 'store']);     // Create item
Route::put('/items/{id}', [ItemController::class, 'update']); // Replace item
Route::delete('/items/{id}', [ItemController::class, 'destroy']); // Delete item
```

*   **Match Route**: Allows a route to respond to specific HTTP verbs:
    ```php
    Route::match(['get', 'post'], '/form', [FormController::class, 'handle']);
    ```
*   **Any Route**: Responds to all HTTP verbs:
    ```php
    Route::any('/fallback-handler', [FallbackController::class, 'handle']);
    ```

### 2. Route Parameters

*   **Required Parameters**: Segments of the URL that must be present:
    ```php
    Route::get('/user/{id}', function ($id) {
        return "User ID: " . $id;
    });
    ```
*   **Optional Parameters**: Denoted by a trailing `?` with a fallback value in the closure/controller method:
    ```php
    Route::get('/user/{name?}', function ($name = 'Guest') {
        return "Welcome, " . $name;
    });
    ```
*   **Parameter Constraints**: Enforces rules on parameter formats using regular expressions:
    ```php
    Route::get('/user/{id}', function ($id) {
        return $id;
    })->where('id', '[0-9]+'); // Numeric only
    
    Route::get('/post/{slug}', function ($slug) {
        return $slug;
    })->where('slug', '[a-z-]+'); // Lowercase letters and dashes
    ```

---

## Views & Passing Data

Views contain the HTML markup served to users. They are written in Blade syntax (`.blade.php`) and reside in `resources/views/`.

### 1. Passing Data to Views
*   **Via Array**:
    ```php
    return view('profile', ['name' => 'Ravi', 'role' => 'Admin']);
    ```
*   **Via `with()`**:
    ```php
    return view('profile')->with('name', 'Ravi')->with('role', 'Admin');
    ```
*   **Via `compact()` (Recommended)**:
    ```php
    $name = 'Ravi';
    $role = 'Admin';
    return view('profile', compact('name', 'role'));
    ```

### 2. Sharing Data with All Views
To share a variable with every view in your application, place a `share()` call inside the `boot()` method of a service provider (e.g., `app/Providers/AppServiceProvider.php`):

```php
use Illuminate\Support\Facades\View;

public function boot()
{
    View::share('school_name', 'Lovely Professional University');
}
```

---

## Customizing Responses

Laravel offers a fluent API to customize the HTTP response sent back to the user.

### 1. Attaching Headers
You can chain custom HTTP headers to the response:
```php
return response("Here is the content")
            ->header('Content-Type', 'text/plain')
            ->header('X-Custom-Header', 'ExamPrep');
```

### 2. Attaching Cookies
Add cookies to outgoing responses using the `cookie()` method:
```php
return response("Theme Set")
            ->cookie('user_theme', 'dark', 60); // name, value, minutes
```

### 3. JSON Responses
For API endpoints, return data in JSON format:
```php
return response()->json([
    'status' => 'success',
    'data' => $products
], 200);
```

### 4. Redirections
Redirect users to new locations:
*   **To URL**:
    ```php
    return redirect('/dashboard');
    ```
*   **To Named Route**:
    ```php
    return redirect()->route('profile.show');
    ```
*   **To Controller Action**:
    ```php
    return redirect()->action([UserController::class, 'show'], ['id' => 1]);
    ```
*   **With Flash Data**: Flashes messages to the session for the next request only:
    ```php
    return redirect('/home')->with('status', 'Login Successful!');
    ```
