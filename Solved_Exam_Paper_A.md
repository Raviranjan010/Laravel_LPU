# Solved Laravel Practical Exam Paper A

This document contains full, complete, and exam-ready code solutions for all 10 questions of Practical Exam Paper A (from the image `WhatsApp Image 2026-06-07 at 15.16.16.jpeg`).

---

## Q1: Session Management (User Login & Logout)

### Question
*   **a)** A website needs to remember the logged-in user during a session. Store the user's name in a Laravel session after login.
*   **b)** Create another page that retrieves and displays the stored session data and provides an option to delete the session on logout.

### Solution

#### 1. Define Routes (`routes/web.php`)
```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

// Route to render login form
Route::get('/login', function() {
    return view('login');
})->name('login');

// Route to handle login form submission and store user in session
Route::post('/login', function(Request $request) {
    $request->validate(['name' => 'required|string']);
    session(['user_name' => $request->input('name')]);
    return redirect('/dashboard');
});

// Route to display dashboard (retrieves session)
Route::get('/dashboard', function() {
    if (!session()->has('user_name')) {
        return redirect('/login')->with('error', 'Please login first.');
    }
    return view('dashboard');
});

// Route to handle logout (deletes session)
Route::post('/logout', function(Request $request) {
    // Destroy session data
    session()->forget('user_name');
    
    // Alternative secure way: $request->session()->invalidate();
    return redirect('/login')->with('status', 'Logged out successfully.');
});
```

#### 2. Create Login View (`resources/views/login.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
</head>
<body>
    <h2>Login Page</h2>
    @if(session('error')) <p style="color:red;">{{ session('error') }}</p> @endif
    @if(session('status')) <p style="color:green;">{{ session('status') }}</p> @endif

    <form method="POST" action="/login">
        @csrf
        Name: <input type="text" name="name" required>
        <button type="submit">Login</button>
    </form>
</body>
</html>
```

#### 3. Create Dashboard View (`resources/views/dashboard.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Dashboard</title>
</head>
<body>
    <h2>Welcome to your Dashboard</h2>
    <!-- Retrieve session value -->
    <p>Logged in User: <strong>{{ session('user_name') }}</strong></p>

    <!-- Logout option -->
    <form method="POST" action="/logout">
        @csrf
        <button type="submit">Logout</button>
    </form>
</body>
</html>
```

---

## Q2: REST API with Resource Controller

### Question
*   **a)** A mobile application requires an API to retrieve product data. Create a REST API using a Laravel resource controller to list products.
*   **b)** Extend the API to allow creating and deleting products using HTTP requests.

### Solution

#### 1. Define API Routes (`routes/api.php`)
```php
use App\Http\Controllers\Api\ProductApiController;
use Illuminate\Support\Facades\Route;

// Register API resource routes (covers index, store, destroy, etc.)
Route::apiResource('products', ProductApiController::class);
```

#### 2. Create the Resource Controller
Generate using: `php artisan make:controller Api/ProductApiController --api`

File: `app/Http/Controllers/Api/ProductApiController.php`
```php
namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

// Dummy Product storage for exam simplicity (normally matches a Model)
class ProductApiController extends Controller
{
    private static $products = [
        ['id' => 1, 'name' => 'Laptop', 'price' => 75000],
        ['id' => 2, 'name' => 'Smartphone', 'price' => 25000]
    ];

    // a) List Products (GET /api/products)
    public function index()
    {
        return response()->json(self::$products, 200);
    }

    // b) Create Product (POST /api/products)
    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|string',
            'price' => 'required|numeric'
        ]);

        $newProduct = [
            'id' => count(self::$products) + 1,
            'name' => $request->input('name'),
            'price' => $request->input('price')
        ];

        return response()->json([
            'message' => 'Product created successfully',
            'product' => $newProduct
        ], 201);
    }

    // b) Delete Product (DELETE /api/products/{id})
    public function destroy($id)
    {
        // Simulate product deletion
        return response()->json([
            'message' => "Product with ID {$id} deleted successfully"
        ], 200);
    }
}
```

---

## Q3: Authenticated Admin Dashboard (Middleware)

### Question
*   **a)** An application has an admin dashboard. Create a controller for the dashboard page.
*   **b)** Apply middleware so that only authenticated users can access the dashboard route.

### Solution

#### 1. Create Dashboard Controller
Generate using: `php artisan make:controller AdminDashboardController`

File: `app/Http/Controllers/AdminDashboardController.php`
```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class AdminDashboardController extends Controller
{
    public function index()
    {
        return view('admin.dashboard');
    }
}
```

#### 2. Define Routes with Authentication Middleware (`routes/web.php`)
```php
use App\Http\Controllers\AdminDashboardController;
use Illuminate\Support\Facades\Route;

// Define admin dashboard and apply standard 'auth' middleware
Route::get('/admin/dashboard', [AdminDashboardController::class, 'index'])
    ->middleware('auth');
```

#### 3. Create Blade View (`resources/views/admin/dashboard.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Admin Dashboard</title>
</head>
<body>
    <h1>Secure Admin Dashboard</h1>
    <p>Only authenticated users can view this text.</p>
</body>
</html>
```

---

## Q4: Localization (English / Spanish Dynamic Switcher)

### Question
*   **a)** A company website wants to support multiple languages. Implement Laravel localization to support English and Spanish.
*   **b)** Create a dropdown that allows users to switch between languages dynamically.

### Solution

#### 1. Create Translation Files
*   **English:** `lang/en/labels.php`
    ```php
    return ['welcome' => 'Welcome to our platform!'];
    ```
*   **Spanish:** `lang/es/labels.php`
    ```php
    return ['welcome' => '¡Bienvenido a nuestra plataforma!'];
    ```

#### 2. Define Locale Middleware
Generate using: `php artisan make:middleware SetLocale`

File: `app/Http/Middleware/SetLocale.php`
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\App;

class SetLocale
{
    public function handle(Request $request, Closure $next)
    {
        if (session()->has('locale')) {
            App::setLocale(session('locale'));
        }
        return $next($request);
    }
}
```
*Note: Make sure to register this middleware in the `web` middleware group inside `app/Http/Kernel.php` or `bootstrap/app.php`.*

#### 3. Define Switch Route (`routes/web.php`)
```php
Route::get('/lang/{locale}', function($locale) {
    if (in_array($locale, ['en', 'es'])) {
        session(['locale' => $locale]);
    }
    return redirect()->back();
});

Route::get('/home', function() {
    return view('home');
});
```

#### 4. Create View File (`resources/views/home.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Localization</title>
</head>
<body>
    <!-- Display translated welcome message -->
    <h1>{{ __('labels.welcome') }}</h1>

    <!-- Dropdown Switcher -->
    <label>Choose Language:</label>
    <select onchange="window.location.href='/lang/' + this.value">
        <option value="en" {{ app()->getLocale() == 'en' ? 'selected' : '' }}>English</option>
        <option value="es" {{ app()->getLocale() == 'es' ? 'selected' : '' }}>Español</option>
    </select>
</body>
</html>
```

---

## Q5: Shared View Data

### Question
*   **a)** A training institute wants to display a list of courses on its website. Create a controller and Blade view that displays course details passed from the controller.
*   **b)** Modify the application so that the course data is shared with all views in the application.

### Solution

#### 1. Define Controller and Course Passing
File: `app/Http/Controllers/CourseController.php`
```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class CourseController extends Controller
{
    public function index()
    {
        // Simple array of courses to display on courses page
        $courses = ['BCA', 'MCA', 'BTech', 'MTech'];
        return view('courses', compact('courses'));
    }
}
```

#### 2. Share Data Across All Views (`app/Providers/AppServiceProvider.php`)
Modify the `boot()` method inside `AppServiceProvider` to share course data dynamically:
```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\View;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Share course array globally across all Blade templates
        View::share('globalCourses', ['BCA', 'MCA', 'BTech', 'MTech']);
    }
}
```

#### 3. Define Routes & Blade Views
*   *Route:* `Route::get('/courses', [CourseController::class, 'index']);`
*   *View (`resources/views/courses.blade.php`):*
    ```html
    <h3>Local View Courses:</h3>
    <ul>
        @foreach($courses as $course)
            <li>{{ $course }}</li>
        @endforeach
    </ul>

    <h3>Global Shared Courses:</h3>
    <ul>
        @foreach($globalCourses as $gCourse)
            <li>{{ $gCourse }}</li>
        @endforeach
    </ul>
    ```

---

## Q6: Blade Layout & Template Inheritance

### Question
*   **a)** A company website requires a common layout with a header, navigation bar, and footer. Create a Blade layout template.
*   **b)** Implement template inheritance to create multiple pages such as Home, About, and Contact using the same layout.

### Solution

#### 1. Create Main Layout Template (`resources/views/layouts/app.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'LPU MVC Portal')</title>
    <style>
        header, footer { background: #333; color: #fff; padding: 10px; text-align: center; }
        nav { background: #f4f4f4; padding: 10px; }
        nav a { margin-right: 15px; }
    </style>
</head>
<body>
    <header><h1>Header Section</h1></header>
    <nav>
        <a href="/home">Home</a>
        <a href="/about">About</a>
        <a href="/contact">Contact</a>
    </nav>
    <main style="padding: 20px;">
        @yield('content')
    </main>
    <footer><p>&copy; 2026 Lovely Professional University</p></footer>
</body>
</html>
```

#### 2. Implement Child Pages Inheriting the Layout
*   **Home Page (`resources/views/home_inherited.blade.php`):**
    ```html
    @extends('layouts.app')
    @section('title', 'Home Page')
    @section('content')
        <h2>Home Welcome</h2>
        <p>This page content belongs to the Home section.</p>
    @endsection
    ```
*   **About Page (`resources/views/about.blade.php`):**
    ```html
    @extends('layouts.app')
    @section('title', 'About Us')
    @section('content')
        <h2>About Section</h2>
        <p>We are a professional MVC program training provider.</p>
    @endsection
    ```
*   **Contact Page (`resources/views/contact.blade.php`):**
    ```html
    @extends('layouts.app')
    @section('title', 'Contact Us')
    @section('content')
        <h2>Contact Details</h2>
        <p>Email us at: support@lpu.co.in</p>
    @endsection
    ```

---

## Q7: Route Parameters & Named Redirection

### Question
*   **a)** A website needs a user profile page. Create a route that accepts a username as a parameter and displays the user profile using a controller and Blade view.
*   **b)** Generate a named route for the profile page and demonstrate how to redirect users to this route.

### Solution

#### 1. Define Named Route with Parameter (`routes/web.php`)
```php
use App\Http\Controllers\ProfileController;

// Define a named route with dynamic 'username' parameter
Route::get('/user/profile/{username}', [ProfileController::class, 'show'])
    ->name('user.profile');

// Redirection test route
Route::get('/go-to-ravi', function() {
    // Redirecting user dynamically to named route 'user.profile'
    return redirect()->route('user.profile', ['username' => 'RaviKumar']);
});
```

#### 2. Create Profile Controller
File: `app/Http/Controllers/ProfileController.php`
```php
namespace App\Http\Controllers;

class ProfileController extends Controller
{
    public function show($username)
    {
        return view('profile', compact('username'));
    }
}
```

#### 3. Create View (`resources/views/profile.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>User Profile</title>
</head>
<body>
    <h2>Profile Page</h2>
    <p>Viewing profile of user: <strong>{{ $username }}</strong></p>
</body>
</html>
```

---

## Q8: Theme Selection using Cookies

### Question
*   **a)** A blogging website wants to allow users to choose between light mode and dark mode. Create a Laravel feature where a user can select the theme using a button and store the preference using cookies.
*   **b)** When the user revisits the website, retrieve the cookie value and automatically apply the previously selected theme to the webpage.

### Solution

#### 1. Define Routes (`routes/web.php`)
```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cookie;
use Illuminate\Support\Facades\Route;

// Display main page with dynamic theme
Route::get('/blog', function(Request $request) {
    // Retrieve cookie 'theme_pref', defaulting to 'light'
    $theme = $request->cookie('theme_pref', 'light');
    return view('blog', compact('theme'));
});

// Route to switch theme preference
Route::post('/switch-theme', function(Request $request) {
    $theme = $request->input('theme', 'light');
    
    // Store in cookie for 1 year (525600 minutes)
    return redirect('/blog')->cookie('theme_pref', $theme, 525600);
});
```

#### 2. Create View Template (`resources/views/blog.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Theme Switcher Blog</title>
    <!-- Apply theme dynamic styles -->
    <style>
        body {
            background-color: {{ $theme == 'dark' ? '#333' : '#fff' }};
            color: {{ $theme == 'dark' ? '#fff' : '#000' }};
            font-family: Arial, sans-serif;
            padding: 30px;
        }
    </style>
</head>
<body>
    <h2>Welcome to LPU Tech Blog</h2>
    <p>This layout automatically remembers your theme setting using browser cookies.</p>

    <!-- Theme Switch Button Form -->
    <form method="POST" action="/switch-theme">
        @csrf
        @if($theme == 'light')
            <input type="hidden" name="theme" value="dark">
            <button type="submit">Switch to Dark Theme</button>
        @else
            <input type="hidden" name="theme" value="light">
            <button type="submit">Switch to Light Theme</button>
        @endif
    </form>
</body>
</html>
```

---

## Q9: Eloquent Product CRUD (Model & Migration)

### Question
*   **a)** An online store needs a system to manage products. Create a database table using migration and a model for products.
*   **b)** Implement CRUD operations using Eloquent ORM to add, update, delete, and display products.

### Solution

#### 1. Generate Model and Migration
Run command: `php artisan make:model Product -m`

*   **Migration Code (`database/migrations/xxxx_create_products_table.php`):**
    ```php
    public function up()
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->decimal('price', 10, 2);
            $table->text('description')->nullable();
            $table->timestamps();
        });
    }
    ```
*   **Model Code (`app/Models/Product.php`):**
    ```php
    namespace App\Models;
    use Illuminate\Database\Eloquent\Model;

    class Product extends Model {
        protected $fillable = ['name', 'price', 'description'];
    }
    ```

#### 2. Create CRUD Controller
File: `app/Http/Controllers/ProductController.php`
```php
namespace App\Http\Controllers;

use App\Models\Product;
use Illuminate\Http\Request;

class ProductController extends Controller
{
    // READ (List)
    public function index() {
        $products = Product::all();
        return view('products.index', compact('products'));
    }

    // CREATE (Store)
    public function store(Request $request) {
        $request->validate(['name' => 'required', 'price' => 'required|numeric']);
        Product::create($request->all());
        return back()->with('status', 'Product added successfully.');
    }

    // UPDATE
    public function update(Request $request, $id) {
        $product = Product::findOrFail($id);
        $product->update($request->all());
        return back()->with('status', 'Product updated.');
    }

    // DELETE
    public function destroy($id) {
        Product::findOrFail($id)->delete();
        return back()->with('status', 'Product deleted.');
    }
}
```

#### 3. Routes (`routes/web.php`)
```php
use App\Http\Controllers\ProductController;

Route::get('/products', [ProductController::class, 'index']);
Route::post('/products', [ProductController::class, 'store']);
Route::put('/products/{id}', [ProductController::class, 'update']);
Route::delete('/products/{id}', [ProductController::class, 'destroy']);
```

---

## Q10: Profile Image Upload & Storage

### Question
*   **a)** A website allows users to upload their profile pictures. Create a form that accepts name, email, and an image file.
*   **b)** Retrieve the request data in the controller and store the uploaded image in the storage directory, then display the uploaded image on the profile page.

### Solution

#### 1. Define Routes (`routes/web.php`)
```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::get('/upload-profile', function() {
    return view('upload_profile');
});

Route::post('/upload-profile', function(Request $request) {
    // Validate request inputs including image size and format constraints
    $request->validate([
        'name' => 'required|string',
        'email' => 'required|email',
        'avatar' => 'required|image|mimes:jpeg,png,webp|max:2048'
    ]);

    if ($request->hasFile('avatar')) {
        $file = $request->file('avatar');
        
        // Save avatar inside storage/app/public/avatars/
        $filename = 'avatar_' . time() . '.' . $file->getClientOriginalExtension();
        $path = $file->storeAs('avatars', $filename, 'public');

        // Pass path to rendering view page
        return view('profile_view', [
            'name' => $request->input('name'),
            'email' => $request->input('email'),
            'avatar_path' => $path
        ]);
    }
});
```

#### 2. Create Upload form (`resources/views/upload_profile.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Upload Picture</title>
</head>
<body>
    <h2>User Profile Upload</h2>
    
    <form method="POST" action="/upload-profile" enctype="multipart/form-data">
        @csrf
        Name: <input type="text" name="name" required><br><br>
        Email: <input type="email" name="email" required><br><br>
        Profile Picture: <input type="file" name="avatar" required><br><br>
        <button type="submit">Upload Profile</button>
    </form>
</body>
</html>
```

#### 3. Create Display Page (`resources/views/profile_view.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Profile Page</title>
</head>
<body>
    <h2>User Profile Dashboard</h2>
    <p>Name: {{ $name }}</p>
    <p>Email: {{ $email }}</p>

    <!-- Run "php artisan storage:link" to make public access functional -->
    <p>Avatar:</p>
    <img src="{{ asset('storage/' . $avatar_path) }}" width="150" alt="Avatar">
</body>
</html>
```
