# Laravel Exam Preparation Guide

## 📚 Complete Question Bank (Units I-III)

This comprehensive guide contains all possible exam questions with detailed answers, organized by difficulty and topic.

---

## Table of Contents
- [Unit I Questions](#unit-i-getting-started-with-mvc-laravel-framework)
- [Unit II Questions](#unit-ii-request-routing--responses)
- [Unit III Questions](#unit-iii-controllers-blade--advanced-routing)
- [Practical/Programming Questions](#practicalprogramming-questions)
- [Quick Revision Notes](#quick-revision-notes)

---

## Unit I: Getting Started with MVC Laravel Framework

### 2-Mark Questions

#### Q1: Define MVC architecture.
**Answer:**
MVC (Model-View-Controller) is a design pattern separating application into:
- **Model**: Data and business logic
- **View**: User interface
- **Controller**: Handles input and coordinates Model-View

#### Q2: What is Laravel?
**Answer:**
Laravel is a free, open-source PHP web framework following MVC pattern, created by Taylor Otwell in 2011 for building modern web applications.

#### Q3: Name any four features of Laravel.
**Answer:**
1. Eloquent ORM
2. Blade templating engine
3. Artisan CLI
4. Database migrations

#### Q4: What is Composer?
**Answer:**
Composer is a dependency manager for PHP that manages libraries and handles autoloading.

#### Q5: What is the purpose of `composer.json` file?
**Answer:**
It defines project dependencies, autoloading rules, scripts, and metadata for the PHP project.

#### Q6: Differentiate between `composer.json` and `composer.lock`.
**Answer:**
- `composer.json`: Defines required packages with version constraints
- `composer.lock`: Locks exact installed versions for consistency

#### Q7: Write command to create new Laravel project.
**Answer:**
```bash
composer create-project laravel/laravel project-name
```

#### Q8: What is Artisan?
**Answer:**
Artisan is Laravel's command-line interface providing helpful commands for development tasks.

#### Q9: Give two examples of Artisan commands.
**Answer:**
1. `php artisan serve` - Start development server
2. `php artisan migrate` - Run database migrations

#### Q10: What is the entry point of Laravel application?
**Answer:**
`public/index.php` is the entry point for all HTTP requests.

#### Q11: What is the purpose of `.env` file?
**Answer:**
Stores environment-specific configuration like database credentials, API keys, and application settings.

#### Q12: Name the directory containing controllers.
**Answer:**
`app/Http/Controllers/`

#### Q13: Where are views stored in Laravel?
**Answer:**
`resources/views/`

#### Q14: What is the purpose of `routes/web.php`?
**Answer:**
Defines web routes for the application with session state and CSRF protection.

#### Q15: Which directory contains database migrations?
**Answer:**
`database/migrations/`

### 5-Mark Questions

#### Q16: Explain MVC architecture with diagram.
**Answer:**

**Components:**
1. **Model**: Manages data, business rules, and database interactions
2. **View**: Displays data to users (UI layer)
3. **Controller**: Receives user input, processes it via Model, returns View

**Flow:**
```
User → Controller → Model → Database
           ↓
         View → User
```

**Benefits:**
- Separation of concerns
- Code reusability
- Easy maintenance
- Parallel development
- Better testing

#### Q17: List and explain five features of Laravel framework.
**Answer:**

1. **Eloquent ORM**: Active Record implementation for database operations with relationships
2. **Blade Templating**: Lightweight templating engine with inheritance and control structures
3. **Artisan CLI**: Command-line tool for code generation, migrations, and automation
4. **Authentication**: Built-in authentication system with scaffolding
5. **Database Migrations**: Version control for database schema with rollback support

#### Q18: Explain the process of installing Laravel on Windows.
**Answer:**

**Step 1: Install PHP**
- Download from windows.php.net
- Extract to folder (e.g., `C:\php`)
- Add to system PATH

**Step 2: Install Composer**
- Download installer from getcomposer.org
- Run `.exe` file
- Verify: `composer --version`

**Step 3: Create Laravel Project**
```bash
composer create-project laravel/laravel myapp
cd myapp
```

**Step 4: Setup Environment**
```bash
copy .env.example .env
php artisan key:generate
```

**Step 5: Configure Database**
Edit `.env` with database credentials

**Step 6: Run Migrations**
```bash
php artisan migrate
```

**Step 7: Start Server**
```bash
php artisan serve
```

Visit `http://localhost:8000`

#### Q19: Explain Laravel directory structure in brief.
**Answer:**

- **app/**: Core application code (Controllers, Models, Middleware)
- **bootstrap/**: Framework bootstrap files
- **config/**: Configuration files
- **database/**: Migrations, seeders, factories
- **public/**: Publicly accessible files (index.php, assets)
- **resources/**: Raw assets (views, lang, sass)
- **routes/**: Route definitions (web.php, api.php)
- **storage/**: Logs, cache, sessions, uploads
- **tests/**: Test files
- **vendor/**: Composer dependencies

#### Q20: What is Artisan? Explain five useful Artisan commands.
**Answer:**

Artisan is Laravel's CLI tool.

**Commands:**
1. `php artisan serve` - Start development server on localhost:8000
2. `php artisan make:controller Name` - Create new controller
3. `php artisan make:model Name -m` - Create model with migration
4. `php artisan migrate` - Run database migrations
5. `php artisan route:list` - Display all registered routes

#### Q21: Explain the significance of service providers in Laravel.
**Answer:**

Service providers bootstrap application services:
- Register bindings in service container
- Configure services
- Load routes, views, translations
- Listed in `config/app.php` under `providers`

**Examples:**
- AppServiceProvider
- RouteServiceProvider
- AuthServiceProvider

### 10-Mark Questions

#### Q22: Compare traditional PHP development with Laravel development.
**Answer:**

| Aspect | Traditional PHP | Laravel |
|--------|----------------|---------|
| **Architecture** | No fixed pattern | MVC pattern enforced |
| **Security** | Manual implementation | Built-in CSRF, SQL injection, XSS protection |
| **Database** | Raw SQL queries | Eloquent ORM with relationships |
| **Routing** | Manual URL handling | Elegant routing system |
| **Templating** | Mixed PHP/HTML | Blade templating engine |
| **Testing** | Complex setup | Built-in PHPUnit integration |
| **Development Speed** | Slower (boilerplate code) | Faster (pre-built features) |
| **Learning Curve** | Lower initially | Steeper but rewarding |
| **Community** | Fragmented | Large, unified community |
| **Maintenance** | Difficult for large projects | Easy with conventions |

**Conclusion:** Laravel provides structure, security, and speed at the cost of initial learning time.

#### Q23: Describe complete Laravel installation process with post-installation setup.
**Answer:**

**Prerequisites:**
- PHP >= 8.1
- Composer
- Web server (Apache/Nginx)
- Database (MySQL/PostgreSQL)

**Installation Steps:**

1. **Install Composer** (if not installed)
   ```bash
   # Windows: Download from getcomposer.org
   # Linux/Mac:
   php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
   php composer-setup.php
   ```

2. **Create Laravel Project**
   ```bash
   composer create-project laravel/laravel blog-app
   cd blog-app
   ```

3. **Environment Setup**
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. **Database Configuration**
   Edit `.env`:
   ```
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=blog_db
   DB_USERNAME=root
   DB_PASSWORD=secret
   ```

5. **Run Migrations**
   ```bash
   php artisan migrate
   ```

6. **Start Development Server**
   ```bash
   php artisan serve
   ```

7. **Verify Installation**
   Visit `http://localhost:8000`

**Post-Installation:**
- Set file permissions
- Configure mail settings
- Install frontend dependencies: `npm install && npm run dev`
- Set up version control: `git init`

---

## Unit II: Request, Routing & Responses

### 2-Mark Questions

#### Q24: What is request lifecycle in Laravel?
**Answer:**
The path an HTTP request takes through the framework: Entry point → Kernel → Middleware → Router → Controller → Model → View → Response

#### Q25: Define routing in Laravel.
**Answer:**
Routing maps URLs to specific actions (closures or controller methods). Defined in `routes/web.php` or `routes/api.php`.

#### Q26: Write syntax for basic GET route.
**Answer:**
```php
Route::get('/url', function() {
    return 'Response';
});
```

#### Q27: What are routing parameters?
**Answer:**
Variables captured from URL segments:
```php
Route::get('/user/{id}', function($id) {
    return $id;
});
```

#### Q28: Differentiate between required and optional parameters.
**Answer:**
- Required: `{id}` - must be present
- Optional: `{name?}` - can be omitted with default value

#### Q29: What are views in Laravel?
**Answer:**
Views contain HTML presented to users, stored in `resources/views/`, using `.blade.php` extension.

#### Q30: How do you pass data to views?
**Answer:**
```php
return view('page', ['name' => 'John']);
// or
return view('page')->with('name', 'John');
```

#### Q31: What is the purpose of `compact()` function?
**Answer:**
Creates array from variables:
```php
$name = 'John';
return view('page', compact('name')); // ['name' => 'John']
```

#### Q32: How do you share data with all views?
**Answer:**
```php
View::share('key', 'value');
```

#### Q33: What is JSON response?
**Answer:**
Returns data in JSON format:
```php
return response()->json(['key' => 'value']);
```

#### Q34: How do you attach headers to response?
**Answer:**
```php
return response($content)->header('Content-Type', 'application/json');
```

#### Q35: How do you set cookies in Laravel?
**Answer:**
```php
return response($content)->cookie('name', 'value', 60);
```

#### Q36: What is redirection in Laravel?
**Answer:**
Redirecting users to different URLs:
```php
return redirect('/home');
```

#### Q37: What are named routes?
**Answer:**
Routes with assigned names for easier reference:
```php
Route::get('/profile')->name('profile');
```

#### Q38: How do you redirect to named route?
**Answer:**
```php
return redirect()->route('profile');
```

#### Q39: What is flash data?
**Answer:**
Session data available only for next request:
```php
return redirect('/home')->with('status', 'Success!');
```

#### Q40: Write command to list all routes.
**Answer:**
```bash
php artisan route:list
```

### 5-Mark Questions

#### Q41: Explain Laravel request lifecycle with diagram.
**Answer:**

**Lifecycle Steps:**

1. **Entry Point** (`public/index.php`)
   - Loads autoloader
   - Bootstraps application

2. **HTTP Kernel**
   - Loads middleware
   - Handles request

3. **Service Providers**
   - Bootstrap services
   - Register bindings

4. **Router**
   - Matches URL to route
   - Determines controller/action

5. **Middleware**
   - Filters request
   - Authentication, CSRF, etc.

6. **Controller**
   - Processes business logic
   - Interacts with models

7. **Model**
   - Database operations
   - Returns data

8. **View**
   - Renders HTML
   - Displays data

9. **Response**
   - Sent back to browser

**Diagram:**
```
Request → index.php → Kernel → Service Providers → Router 
    → Middleware → Controller → Model → View → Response
```

#### Q42: Explain routing parameters with examples.
**Answer:**

**Required Parameters:**
```php
Route::get('/user/{id}', function($id) {
    return "User ID: $id";
});
// /user/123 → $id = 123
```

**Optional Parameters:**
```php
Route::get('/user/{name?}', function($name = 'Guest') {
    return "Hello, $name";
});
// /user/John → $name = 'John'
// /user → $name = 'Guest'
```

**Multiple Parameters:**
```php
Route::get('/posts/{postId}/comments/{commentId}', 
    function($postId, $commentId) {
        return "Post: $postId, Comment: $commentId";
    });
```

**Constraints:**
```php
Route::get('/user/{id}', function($id) {
    return $id;
})->where('id', '[0-9]+');
```

#### Q43: Explain different ways to pass data to views.
**Answer:**

**Method 1: Array**
```php
return view('greeting', ['name' => 'John', 'age' => 25]);
```

**Method 2: with()**
```php
return view('greeting')->with('name', 'John');
```

**Method 3: compact()**
```php
$name = 'John';
$age = 25;
return view('greeting', compact('name', 'age'));
```

**Method 4: Magic Methods**
```php
return view('greeting')->withName('John')->withAge(25);
```

**In View:**
```blade
<h1>Hello, {{ $name }}</h1>
<p>Age: {{ $age }}</p>
```

#### Q44: Explain sharing data with all views.
**Answer:**

**Method 1: View::share()**
In `AppServiceProvider`:
```php
public function boot()
{
    View::share('siteName', 'My Website');
    View::share('year', date('Y'));
}
```

**Method 2: View Composers**
```php
View::composer('*', function($view) {
    $view->with('categories', Category::all());
});
```

**Usage in any view:**
```blade
<p>{{ $siteName }} - {{ $year }}</p>
```

#### Q45: Explain different response types in Laravel.
**Answer:**

**String Response:**
```php
return 'Hello World';
```

**View Response:**
```php
return view('welcome');
```

**JSON Response:**
```php
return response()->json(['status' => 'success']);
```

**Download Response:**
```php
return response()->download($pathToFile);
```

**With Headers:**
```php
return response($content)->header('X-Custom', 'Value');
```

**With Cookies:**
```php
return response($content)->cookie('name', 'value', 60);
```

#### Q46: Explain redirections in Laravel with examples.
**Answer:**

**Basic Redirect:**
```php
return redirect('/home');
```

**To Named Route:**
```php
return redirect()->route('profile');
```

**With Parameters:**
```php
return redirect()->route('user.profile', ['id' => 1]);
```

**To Controller Action:**
```php
return redirect()->action([UserController::class, 'index']);
```

**Back:**
```php
return redirect()->back();
// or
return back();
```

**With Flash Data:**
```php
return redirect('/home')->with('status', 'Success!');
```

### 10-Mark Questions

#### Q47: Explain Laravel responses with headers, cookies, and JSON.
**Answer:**

**Basic Responses:**
```php
return 'Simple text';
return view('page');
```

**JSON Responses:**
```php
// Basic JSON
return response()->json(['name' => 'John', 'age' => 25]);

// With status code
return response()->json(['error' => 'Not found'], 404);

// With headers
return response()->json($data, 200, [
    'X-Total-Count' => 100,
    'Cache-Control' => 'max-age=3600'
]);
```

**Attaching Headers:**
```php
return response($content)
    ->header('Content-Type', 'application/json')
    ->header('X-API-Key', 'secret-key')
    ->header('Cache-Control', 'no-cache');

// Multiple headers
return response($content)->withHeaders([
    'Header1' => 'Value1',
    'Header2' => 'Value2'
]);
```

**Attaching Cookies:**
```php
// Simple cookie
return response($content)->cookie('theme', 'dark', 60);

// Detailed cookie
return response($content)->cookie(
    'remember_me',  // name
    $token,         // value
    525600,         // minutes (1 year)
    '/',            // path
    '.example.com', // domain
    true,           // secure (HTTPS)
    true,           // httpOnly
    false,          // raw
    'lax'           // sameSite
);

// Forever cookie
return response($content)->cookie(
    cookie()->forever('language', 'en')
);

// Forget cookie
return response($content)->cookie(
    cookie()->forget('session_token')
);
```

**File Downloads:**
```php
return response()->download($pathToFile);
return response()->download($path, $name, $headers);
```

#### Q48: Explain routing with parameters, constraints, and named routes.
**Answer:**

**Basic Routes:**
```php
Route::get('/', function() {
    return 'Home';
});

Route::post('/submit', function() {
    return 'Submitted';
});
```

**Parameters:**
```php
// Required
Route::get('/user/{id}', function($id) {
    return $id;
});

// Optional
Route::get('/user/{name?}', function($name = 'Guest') {
    return $name;
});

// Multiple
Route::get('/posts/{post}/comments/{comment}', 
    function($post, $comment) {
        return "$post - $comment";
    });
```

**Constraints:**
```php
// Numeric only
Route::get('/user/{id}', function($id) {
    return $id;
})->where('id', '[0-9]+');

// Alphabetic only
Route::get('/user/{name}', function($name) {
    return $name;
})->where('name', '[A-Za-z]+');

// Multiple constraints
Route::get('/user/{id}/{slug}', function($id, $slug) {
    // ...
})->where(['id' => '[0-9]+', 'slug' => '[a-z-]+']);
```

**Named Routes:**
```php
// Define
Route::get('/profile', function() {
    // ...
})->name('profile');

Route::get('/user/{id}', [UserController::class, 'show'])
    ->name('user.show');

// Generate URL
$url = route('profile');
$url = route('user.show', ['id' => 1]);

// Redirect
return redirect()->route('profile');

// In Blade
<a href="{{ route('profile') }}">Profile</a>
```

---

## Unit III: Controllers, Blade & Advanced Routing

### 2-Mark Questions

#### Q49: What are controllers?
**Answer:**
Classes that group related request handling logic. Located in `app/Http/Controllers/`.

#### Q50: Write command to create controller.
**Answer:**
```bash
php artisan make:controller UserController
```

#### Q51: What is resource controller?
**Answer:**
Controller with predefined CRUD methods: index, create, store, show, edit, update, destroy.

#### Q52: Write command to create resource controller.
**Answer:**
```bash
php artisan make:controller PhotoController --resource
```

#### Q53: What is middleware?
**Answer:**
Filters that process HTTP requests before they reach controllers (authentication, logging, etc.).

#### Q54: How do you apply middleware to controller?
**Answer:**
```php
public function __construct() {
    $this->middleware('auth');
}
```

#### Q55: What is Blade?
**Answer:**
Laravel's templating engine allowing clean PHP in HTML templates with `.blade.php` extension.

#### Q56: How do you display variable in Blade?
**Answer:**
```blade
{{ $variable }}
```

#### Q57: What is template inheritance?
**Answer:**
Mechanism to define base layout and extend it in child views using `@extends`, `@section`, `@yield`.

#### Q58: Write Blade syntax for if statement.
**Answer:**
```blade
@if(condition)
    // content
@endif
```

#### Q59: Write Blade syntax for foreach loop.
**Answer:**
```blade
@foreach($items as $item)
    {{ $item }}
@endforeach
```

#### Q60: What is `@csrf` directive?
**Answer:**
Generates CSRF token field for form protection against cross-site request forgery.

#### Q61: What are route groups?
**Answer:**
Group multiple routes sharing attributes (middleware, prefix, namespace).

#### Q62: What is route prefixing?
**Answer:**
Adding common prefix to group of routes:
```php
Route::prefix('admin')->group(function() {
    // Routes here have /admin prefix
});
```

#### Q63: What is domain routing?
**Answer:**
Routing based on domain/subdomain:
```php
Route::domain('{account}.myapp.com')->group(function() {
    // Routes for subdomain
});
```

#### Q64: How do you generate URL in Laravel?
**Answer:**
```php
url('/path');
route('route-name');
action([Controller::class, 'method']);
```

#### Q65: What is asset helper?
**Answer:**
Generates URL for assets (CSS, JS, images):
```blade
{{ asset('css/style.css') }}
```

### 5-Mark Questions

#### Q66: Explain creating and using controllers with example.
**Answer:**

**Create Controller:**
```bash
php artisan make:controller UserController
```

**Controller Code:**
```php
<?php

namespace App\Http\Controllers;

use App\Models\User;

class UserController extends Controller
{
    public function index()
    {
        $users = User::all();
        return view('users.index', compact('users'));
    }

    public function show($id)
    {
        $user = User::findOrFail($id);
        return view('users.show', compact('user'));
    }
}
```

**Routes:**
```php
Route::get('/users', [UserController::class, 'index']);
Route::get('/users/{id}', [UserController::class, 'show']);
```

#### Q67: Explain RESTful resource controller with all methods.
**Answer:**

**Create:**
```bash
php artisan make:controller PostController --resource
```

**Methods:**
1. `index()` - List all resources (GET /posts)
2. `create()` - Show create form (GET /posts/create)
3. `store()` - Save new resource (POST /posts)
4. `show($id)` - Display single resource (GET /posts/{id})
5. `edit($id)` - Show edit form (GET /posts/{id}/edit)
6. `update($id)` - Update resource (PUT /posts/{id})
7. `destroy($id)` - Delete resource (DELETE /posts/{id})

**Register Route:**
```php
Route::resource('posts', PostController::class);
```

#### Q68: Explain Blade template inheritance with example.
**Answer:**

**Layout File** (`layouts/app.blade.php`):
```blade
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title')</title>
</head>
<body>
    @include('partials.header')
    
    <div class="container">
        @yield('content')
    </div>
    
    @include('partials.footer')
</body>
</html>
```

**Child View** (`home.blade.php`):
```blade
@extends('layouts.app')

@section('title', 'Home Page')

@section('content')
    <h1>Welcome to Home</h1>
@endsection
```

#### Q69: Explain Blade control structures with examples.
**Answer:**

**If Statement:**
```blade
@if($age >= 18)
    Adult
@elseif($age >= 13)
    Teenager
@else
    Child
@endif
```

**Foreach Loop:**
```blade
@foreach($users as $user)
    <p>{{ $user->name }}</p>
@endforeach
```

**Forelse Loop:**
```blade
@forelse($posts as $post)
    <li>{{ $post->title }}</li>
@empty
    <p>No posts found</p>
@endforelse
```

**For Loop:**
```blade
@for($i = 0; $i < 10; $i++)
    {{ $i }}
@endfor
```

**While Loop:**
```blade
@while($condition)
    Content
@endwhile
```

#### Q70: Explain route groups with middleware and prefix.
**Answer:**

**Basic Group:**
```php
Route::middleware(['auth'])->group(function() {
    Route::get('/dashboard', [DashboardController::class, 'index']);
    Route::get('/profile', [ProfileController::class, 'show']);
});
```

**With Prefix:**
```php
Route::prefix('admin')->group(function() {
    Route::get('/dashboard', function() {
        // URL: /admin/dashboard
    });
});
```

**Combined:**
```php
Route::prefix('admin')
    ->middleware(['auth', 'admin'])
    ->name('admin.')
    ->group(function() {
        Route::get('/dashboard', [AdminController::class, 'index'])
            ->name('dashboard');
        
        Route::get('/users', [UserController::class, 'index'])
            ->name('users.index');
    });
```

Now routes are: `admin.dashboard`, `admin.users.index`

#### Q71: Explain URL generation methods in Laravel.
**Answer:**

**Current URL:**
```php
url()->current();   // Current URL without query string
url()->full();      // Current URL with query string
url()->previous();  // Previous URL
```

**Generate URLs:**
```php
// Using url() helper
url('users/profile');
url('users/profile', ['id' => 1]);

// Using route() helper
route('profile');
route('user.show', ['id' => 1]);

// Using action() helper
action([UserController::class, 'index']);
action([UserController::class, 'show'], ['id' => 1]);
```

**Asset URLs:**
```blade
<link href="{{ asset('css/app.css') }}" rel="stylesheet">
<script src="{{ asset('js/app.js') }}"></script>
<img src="{{ asset('images/logo.png') }}">
```

### 10-Mark Questions

#### Q72: Explain Blade templating engine with all features.
**Answer:**

**Displaying Data:**
```blade
{{ $variable }}           {{-- Escaped --}}
{!! $html !!}             {{-- Unescaped --}}
{{ $name ?? 'Guest' }}    {{-- Default --}}
```

**Control Structures:**
```blade
@if($condition)
    Content
@endif

@unless($condition)
    Content
@endunless

@switch($value)
    @case(1)
        First
        @break
    @default
        Default
@endswitch
```

**Loops:**
```blade
@foreach($items as $item)
    {{ $loop->iteration }}. {{ $item }}
@endforeach

@forelse($items as $item)
    {{ $item }}
@empty
    No items
@endforelse

@for($i = 0; $i < 10; $i++)
    {{ $i }}
@endfor

@while($condition)
    Content
@endwhile
```

**Template Inheritance:**
```blade
{{-- Layout --}}
@yield('content')
@hasSection('sidebar')
    @yield('sidebar')
@endif

{{-- Child --}}
@extends('layouts.app')
@section('content')
    Content here
@endsection
```

**Includes:**
```blade
@include('partials.header')
@include('partials.alert', ['type' => 'success'])
@includeIf('partials.sidebar')
@includeWhen($condition, 'partial')
```

**Stacks:**
```blade
{{-- Layout --}}
@stack('scripts')

{{-- Child --}}
@push('scripts')
    <script src="app.js"></script>
@endpush
```

**Comments:**
```blade
{{-- This is a comment --}}
```

**PHP Blocks:**
```blade
@php
    $var = 'value';
@endphp
```

**Forms:**
```blade
<form method="POST">
    @csrf
    @method('PUT')
</form>
```

**Authentication:**
```blade
@auth
    Logged in content
@endauth

@guest
    Guest content
@endguest
```

#### Q73: Explain advanced routing features with examples.
**Answer:**

**Named Routes:**
```php
Route::get('/profile', function() {})->name('profile');
route('profile'); // Generate URL
redirect()->route('profile'); // Redirect
```

**Parameter Constraints:**
```php
Route::get('/user/{id}', function($id) {})
    ->where('id', '[0-9]+');

Route::get('/user/{id}/{slug}', function($id, $slug) {})
    ->where(['id' => '[0-9]+', 'slug' => '[a-z-]+']);
```

**Global Patterns:**
```php
// In RouteServiceProvider
Route::pattern('id', '[0-9]+');
```

**Route Groups:**
```php
Route::middleware(['auth'])->group(function() {
    Route::get('/dashboard', [DashboardController::class, 'index']);
});

Route::prefix('api/v1')->group(function() {
    Route::get('/users', [UserController::class, 'index']);
});

Route::namespace('Admin')->group(function() {
    Route::get('/dashboard', [DashboardController::class, 'index']);
});
```

**Combined Groups:**
```php
Route::prefix('admin')
    ->middleware(['auth', 'admin'])
    ->name('admin.')
    ->group(function() {
        Route::get('/dashboard', [AdminController::class, 'index'])
            ->name('dashboard');
    });
```

**Domain Routing:**
```php
Route::domain('{account}.myapp.com')->group(function() {
    Route::get('/dashboard', function($account) {
        // account.myapp.com/dashboard
    });
});

Route::domain('api.myapp.com')->group(function() {
    Route::get('/users', [API\UserController::class, 'index']);
});
```

**Route Model Binding:**
```php
// Implicit
Route::get('/users/{user}', function(User $user) {
    return $user->name;
});

// Explicit
Route::bind('user', function($value) {
    return User::where('username', $value)->firstOrFail();
});
```

**Secure Routes:**
```php
Route::get('/payment', function() {})->secure();
```

---

## Practical/Programming Questions

### P1: Create a Laravel project "BlogSystem" and set it up.
**Answer:**
```bash
composer create-project laravel/laravel BlogSystem
cd BlogSystem
copy .env.example .env
php artisan key:generate
php artisan migrate
php artisan serve
```

### P2: Create routes for CRUD operations on "Products".
**Answer:**
```php
use App\Http\Controllers\ProductController;

Route::get('/products', [ProductController::class, 'index'])->name('products.index');
Route::get('/products/create', [ProductController::class, 'create'])->name('products.create');
Route::post('/products', [ProductController::class, 'store'])->name('products.store');
Route::get('/products/{id}', [ProductController::class, 'show'])->name('products.show');
Route::get('/products/{id}/edit', [ProductController::class, 'edit'])->name('products.edit');
Route::put('/products/{id}', [ProductController::class, 'update'])->name('products.update');
Route::delete('/products/{id}', [ProductController::class, 'destroy'])->name('products.destroy');

// Or simply:
Route::resource('products', ProductController::class);
```

### P3: Create a controller "StudentController" with resource methods.
**Answer:**
```bash
php artisan make:controller StudentController --resource
```

```php
<?php

namespace App\Http\Controllers;

use App\Models\Student;
use Illuminate\Http\Request;

class StudentController extends Controller
{
    public function index()
    {
        $students = Student::all();
        return view('students.index', compact('students'));
    }

    public function create()
    {
        return view('students.create');
    }

    public function store(Request $request)
    {
        $validated = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:students',
        ]);

        Student::create($validated);

        return redirect()->route('students.index')
            ->with('success', 'Student added!');
    }

    public function show(Student $student)
    {
        return view('students.show', compact('student'));
    }

    public function edit(Student $student)
    {
        return view('students.edit', compact('student'));
    }

    public function update(Request $request, Student $student)
    {
        $validated = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email',
        ]);

        $student->update($validated);

        return redirect()->route('students.index')
            ->with('success', 'Student updated!');
    }

    public function destroy(Student $student)
    {
        $student->delete();

        return redirect()->route('students.index')
            ->with('success', 'Student deleted!');
    }
}
```

### P4: Create a Blade layout with header, footer, and content sections.
**Answer:**

**Layout** (`layouts/app.blade.php`):
```blade
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'My App')</title>
    <link rel="stylesheet" href="{{ asset('css/style.css') }}">
</head>
<body>
    <header>
        <h1>{{ config('app.name') }}</h1>
        <nav>
            <a href="{{ url('/') }}">Home</a>
            <a href="{{ route('about') }}">About</a>
        </nav>
    </header>

    <main>
        @if(session('success'))
            <div class="alert">{{ session('success') }}</div>
        @endif
        
        @yield('content')
    </main>

    <footer>
        <p>&copy; {{ date('Y') }} My App</p>
    </footer>

    @stack('scripts')
</body>
</html>
```

**Child View** (`home.blade.php`):
```blade
@extends('layouts.app')

@section('title', 'Home Page')

@section('content')
    <h2>Welcome to Home</h2>
    <p>This is the home page content.</p>
@endsection

@push('scripts')
    <script src="{{ asset('js/home.js') }}"></script>
@endpush
```

### P5: Create middleware "CheckAge" that redirects users under 18.
**Answer:**
```bash
php artisan make:middleware CheckAge
```

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckAge
{
    public function handle(Request $request, Closure $next)
    {
        if ($request->input('age') < 18) {
            return redirect('/underage');
        }

        return $next($request);
    }
}
```

**Register in `app/Http/Kernel.php`:**
```php
protected $routeMiddleware = [
    'check.age' => \App\Http\Middleware\CheckAge::class,
];
```

**Use in route:**
```php
Route::get('/enter', function() {
    return 'Welcome!';
})->middleware('check.age');
```

### P6: Write code to redirect user after login with success message.
**Answer:**
```php
public function login(Request $request)
{
    $credentials = $request->validate([
        'email' => 'required|email',
        'password' => 'required',
    ]);

    if (auth()->attempt($credentials)) {
        return redirect()
            ->route('dashboard')
            ->with('success', 'Welcome back, ' . auth()->user()->name);
    }

    return back()->withErrors(['email' => 'Invalid credentials']);
}
```

**Display in view:**
```blade
@if(session('success'))
    <div class="alert alert-success">
        {{ session('success') }}
    </div>
@endif
```

### P7: Create JSON API endpoint returning user data.
**Answer:**
```php
Route::get('/api/users/{id}', function($id) {
    $user = User::find($id);
    
    if (!$user) {
        return response()->json([
            'error' => 'User not found'
        ], 404);
    }

    return response()->json([
        'status' => 'success',
        'data' => [
            'id' => $user->id,
            'name' => $user->name,
            'email' => $user->email
        ]
    ], 200, [
        'Content-Type' => 'application/json',
        'Cache-Control' => 'max-age=3600'
    ]);
});
```

### P8: Create form with CSRF protection and display validation errors.
**Answer:**
```blade
<form method="POST" action="{{ route('users.store') }}">
    @csrf
    
    <div>
        <label>Name:</label>
        <input type="text" name="name" value="{{ old('name') }}">
        @error('name')
            <span class="error">{{ $message }}</span>
        @enderror
    </div>
    
    <div>
        <label>Email:</label>
        <input type="email" name="email" value="{{ old('email') }}">
        @error('email')
            <span class="error">{{ $message }}</span>
        @enderror
    </div>
    
    <button type="submit">Submit</button>
</form>
```

---

## Quick Revision Notes

### Important Commands
```bash
# Installation
composer create-project laravel/laravel project-name

# Server
php artisan serve

# Make Commands
php artisan make:controller Name [--resource|--api|--invokable]
php artisan make:model Name [-mfs]
php artisan make:middleware Name
php artisan make:request Name

# Database
php artisan migrate
php artisan db:seed
php artisan make:migration create_table_name

# Cache
php artisan optimize:clear
php artisan config:cache
php artisan route:cache

# Routes
php artisan route:list
```

### Key Concepts Summary

**MVC:**
- Model: Data & logic
- View: UI
- Controller: Coordination

**Routing:**
- Define in `routes/web.php`
- Use parameters: `{id}`, `{name?}`
- Apply constraints: `->where('id', '[0-9]+')`
- Name routes: `->name('route.name')`

**Controllers:**
- Group related logic
- Resource controllers for CRUD
- Apply middleware in constructor

**Blade:**
- `{{ $var }}` for escaped output
- `@extends`, `@section`, `@yield` for inheritance
- `@if`, `@foreach`, `@forelse` for control
- `@include` for partials
- `@csrf` for forms

**Responses:**
- String, View, JSON, Download
- Attach headers: `->header()`
- Attach cookies: `->cookie()`
- Redirect: `redirect()->route()`

**Advanced Routing:**
- Route groups for organization
- Middleware for filtering
- Prefix for URL structure
- Domain routing for subdomains
- Model binding for auto-resolution

---

## Exam Tips

### For Theory Questions:
✅ Understand concepts thoroughly  
✅ Use diagrams where applicable  
✅ Provide real-world examples  
✅ Compare and contrast when asked  
✅ Write clear, structured answers  

### For Practical Questions:
✅ Write complete, working code  
✅ Follow Laravel conventions  
✅ Include proper namespaces  
✅ Add comments for clarity  
✅ Handle edge cases  

### Common Mistakes to Avoid:
❌ Forgetting CSRF token in forms  
❌ Not using proper HTTP verbs  
❌ Missing route names  
❌ Incorrect Blade syntax  
❌ Forgetting to register middleware  
❌ Not validating user input  

### Time Management:
- 2-mark questions: 3-4 minutes each
- 5-mark questions: 8-10 minutes each
- 10-mark questions: 15-20 minutes each
- Practical questions: 20-25 minutes each

---

## Final Checklist

Before exam, ensure you can:
- ✅ Explain MVC architecture
- ✅ Install Laravel project
- ✅ Use Artisan commands
- ✅ Define routes with parameters
- ✅ Create controllers (basic & resource)
- ✅ Apply middleware
- ✅ Use Blade templating
- ✅ Implement template inheritance
- ✅ Pass data to views
- ✅ Create different response types
- ✅ Perform redirections
- ✅ Use route groups
- ✅ Generate URLs
- ✅ Write complete CRUD application

---

**Good Luck with Your Exam! 🎓**

---

**Previous Units:**
- [Unit I: Getting Started](../Unit_I_Getting_Started/01_Getting_Started_MVC_Laravel.md)
- [Unit II: Request, Routing & Responses](../Unit_II_Request_Routing_Responses/02_Request_Routing_Responses.md)
- [Unit III: Controllers, Blade & Advanced Routing](../Unit_III_Controllers_Blade_Advanced_Routing/03_Controllers_Blade_Advanced_Routing.md)

**Additional Resources:**
- [Commands Reference](../Commands_Reference.md)
