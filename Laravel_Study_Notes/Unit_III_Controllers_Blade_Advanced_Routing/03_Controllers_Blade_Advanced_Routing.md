# Unit III: Controllers, Blade & Advanced Routing

## 📚 Table of Contents
- [Basic Controllers](#basic-controllers)
- [Controller Routing](#controller-routing)
- [Controller Middleware](#controller-middleware)
- [RESTful Resource Controllers](#restful-resource-controllers)
- [Blade Templating Engine](#blade-templating-engine)
- [Advanced Routing](#advanced-routing)
- [URL Generation](#url-generation)
- [Exam Questions & Answers](#exam-questions--answers)

---

## Basic Controllers

### What are Controllers?
Controllers group related request handling logic into a single class. They store all logic for handling requests in one organized place.

**Location:** `app/Http/Controllers/`

### Creating Controllers

#### Method 1: Using Artisan Command
```bash
# Basic controller
php artisan make:controller UserController

# Resource controller (CRUD operations)
php artisan make:controller PhotoController --resource

# API resource controller
php artisan make:controller API/PhotoController --api

# Invokable controller (single action)
php artisan make:controller ShowProfile --invokable
```

#### Method 2: Manual Creation
Create file: `app/Http/Controllers/UserController.php`

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    // Controller methods here
}
```

### Basic Controller Structure

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Display a listing of users.
     */
    public function index()
    {
        $users = User::all();
        return view('users.index', compact('users'));
    }

    /**
     * Display the specified user.
     */
    public function show($id)
    {
        $user = User::findOrFail($id);
        return view('users.show', compact('user'));
    }

    /**
     * Store a newly created user.
     */
    public function store(Request $request)
    {
        $validated = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users',
        ]);

        $user = User::create($validated);

        return redirect()->route('users.show', $user->id)
            ->with('success', 'User created successfully!');
    }
}
```

### Single Action Controllers (Invokable)
For controllers with only one action:

```bash
php artisan make:controller ShowProfile --invokable
```

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;

class ShowProfile extends Controller
{
    /**
     * Handle the incoming request.
     */
    public function __invoke(Request $request, $id)
    {
        $user = User::findOrFail($id);
        return view('users.profile', compact('user'));
    }
}
```

**Route definition:**
```php
Route::get('/profile/{id}', ShowProfile::class);
```

### Controller Best Practices

✅ **Do:**
- Keep controllers thin (delegate to services/models)
- Use type hinting for dependencies
- Return appropriate response types
- Follow RESTful conventions

❌ **Don't:**
- Put business logic in controllers
- Make controllers too large
- Duplicate code across controllers
- Access database directly (use models)

---

## Controller Routing

### Basic Controller Routes

#### Closure vs Controller
```php
// Closure route (simple)
Route::get('/users', function () {
    return view('users.index');
});

// Controller route (organized)
Route::get('/users', [UserController::class, 'index']);
```

### Route to Controller Actions

#### Single Action
```php
use App\Http\Controllers\UserController;

Route::get('/users', [UserController::class, 'index']);
Route::get('/users/{id}', [UserController::class, 'show']);
Route::post('/users', [UserController::class, 'store']);
Route::put('/users/{id}', [UserController::class, 'update']);
Route::delete('/users/{id}', [UserController::class, 'destroy']);
```

#### PHP 8 Syntax (Shorter)
```php
use App\Http\Controllers\UserController;

Route::get('/users', [UserController::class, 'index']);
```

### Complete CRUD Example

**Controller:**
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::latest()->paginate(10);
        return view('posts.index', compact('posts'));
    }

    public function create()
    {
        return view('posts.create');
    }

    public function store(Request $request)
    {
        $validated = $request->validate([
            'title' => 'required|max:255',
            'content' => 'required',
        ]);

        Post::create($validated);

        return redirect()->route('posts.index')
            ->with('success', 'Post created!');
    }

    public function show(Post $post)
    {
        return view('posts.show', compact('post'));
    }

    public function edit(Post $post)
    {
        return view('posts.edit', compact('post'));
    }

    public function update(Request $request, Post $post)
    {
        $validated = $request->validate([
            'title' => 'required|max:255',
            'content' => 'required',
        ]);

        $post->update($validated);

        return redirect()->route('posts.index')
            ->with('success', 'Post updated!');
    }

    public function destroy(Post $post)
    {
        $post->delete();

        return redirect()->route('posts.index')
            ->with('success', 'Post deleted!');
    }
}
```

**Routes:**
```php
use App\Http\Controllers\PostController;

Route::get('/posts', [PostController::class, 'index'])->name('posts.index');
Route::get('/posts/create', [PostController::class, 'create'])->name('posts.create');
Route::post('/posts', [PostController::class, 'store'])->name('posts.store');
Route::get('/posts/{post}', [PostController::class, 'show'])->name('posts.show');
Route::get('/posts/{post}/edit', [PostController::class, 'edit'])->name('posts.edit');
Route::put('/posts/{post}', [PostController::class, 'update'])->name('posts.update');
Route::delete('/posts/{post}', [PostController::class, 'destroy'])->name('posts.destroy');
```

---

## Controller Middleware

### What is Middleware?
Middleware filters HTTP requests entering your application. Common uses:
- Authentication
- Authorization
- CORS
- Logging
- Rate limiting

### Assigning Middleware to Controllers

#### In Controller Constructor
```php
<?php

namespace App\Http\Controllers;

class UserController extends Controller
{
    public function __construct()
    {
        // Apply to all methods
        $this->middleware('auth');
        
        // Apply to specific methods
        $this->middleware('log')->only('index');
        $this->middleware('subscribed')->except('store');
    }

    public function index() { /* ... */ }
    public function store() { /* ... */ }
}
```

#### In Route Definition
```php
Route::get('/profile', [UserController::class, 'index'])
    ->middleware('auth');

Route::post('/users', [UserController::class, 'store'])
    ->middleware(['auth', 'verified']);
```

### Creating Custom Middleware

```bash
php artisan make:middleware CheckAge
```

**File:** `app/Http/Middleware/CheckAge.php`

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckAge
{
    public function handle(Request $request, Closure $next)
    {
        if ($request->age <= 200) {
            return redirect('home');
        }

        return $next($request);
    }
}
```

**Register in `app/Http/Kernel.php`:**
```php
protected $routeMiddleware = [
    // ...
    'check.age' => \App\Http\Middleware\CheckAge::class,
];
```

**Use in controller:**
```php
public function __construct()
{
    $this->middleware('check.age');
}
```

### Middleware Groups

Laravel provides default middleware groups:

**web** group (in `routes/web.php`):
- EncryptCookies
- AddQueuedCookiesToResponse
- StartSession
- ShareErrorsFromSession
- VerifyCsrfToken
- SubstituteBindings

**api** group (in `routes/api.php`):
- ThrottleRequests
- SubstituteBindings

---

## RESTful Resource Controllers

### What are Resource Controllers?
Resource controllers handle all CRUD operations for a resource with conventional method names.

### Creating Resource Controller

```bash
php artisan make:controller PhotoController --resource
```

This creates a controller with 7 methods:

```php
<?php

namespace App\Http\Controllers;

use App\Models\Photo;
use Illuminate\Http\Request;

class PhotoController extends Controller
{
    /**
     * Display a listing of the resource.
     * GET /photos
     */
    public function index()
    {
        $photos = Photo::all();
        return view('photos.index', compact('photos'));
    }

    /**
     * Show the form for creating a new resource.
     * GET /photos/create
     */
    public function create()
    {
        return view('photos.create');
    }

    /**
     * Store a newly created resource.
     * POST /photos
     */
    public function store(Request $request)
    {
        $validated = $request->validate([
            'title' => 'required',
            'image' => 'required|image',
        ]);

        Photo::create($validated);

        return redirect()->route('photos.index')
            ->with('success', 'Photo uploaded!');
    }

    /**
     * Display the specified resource.
     * GET /photos/{photo}
     */
    public function show(Photo $photo)
    {
        return view('photos.show', compact('photo'));
    }

    /**
     * Show the form for editing the specified resource.
     * GET /photos/{photo}/edit
     */
    public function edit(Photo $photo)
    {
        return view('photos.edit', compact('photo'));
    }

    /**
     * Update the specified resource.
     * PUT/PATCH /photos/{photo}
     */
    public function update(Request $request, Photo $photo)
    {
        $validated = $request->validate([
            'title' => 'required',
        ]);

        $photo->update($validated);

        return redirect()->route('photos.index')
            ->with('success', 'Photo updated!');
    }

    /**
     * Remove the specified resource.
     * DELETE /photos/{photo}
     */
    public function destroy(Photo $photo)
    {
        $photo->delete();

        return redirect()->route('photos.index')
            ->with('success', 'Photo deleted!');
    }
}
```

### Registering Resource Route

```php
use App\Http\Controllers\PhotoController;

// Single resource
Route::resource('photos', PhotoController::class);

// Multiple resources
Route::resources([
    'photos' => PhotoController::class,
    'posts' => PostController::class,
]);
```

This single line creates 7 routes:

| Verb | URI | Action | Route Name |
|------|-----|--------|------------|
| GET | `/photos` | index | photos.index |
| GET | `/photos/create` | create | photos.create |
| POST | `/photos` | store | photos.store |
| GET | `/photos/{photo}` | show | photos.show |
| GET | `/photos/{photo}/edit` | edit | photos.edit |
| PUT/PATCH | `/photos/{photo}` | update | photos.update |
| DELETE | `/photos/{photo}` | destroy | photos.destroy |

View all routes:
```bash
php artisan route:list
```

### Partial Resource Routes

If you don't need all actions:

```php
// Only specific actions
Route::resource('photos', PhotoController::class)->only([
    'index', 'show'
]);

// Exclude specific actions
Route::resource('photos', PhotoController::class)->except([
    'create', 'edit'
]);
```

### API Resource Controller
For API endpoints (no create/edit forms):

```bash
php artisan make:controller API/PhotoController --api
```

```php
Route::apiResource('photos', PhotoController::class);
```

Creates only 5 routes (excludes create and edit):

| Verb | URI | Action |
|------|-----|--------|
| GET | `/photos` | index |
| POST | `/photos` | store |
| GET | `/photos/{photo}` | show |
| PUT/PATCH | `/photos/{photo}` | update |
| DELETE | `/photos/{photo}` | destroy |

### Nested Resources

```php
// Photos belong to users
Route::resource('users.photos', PhotoController::class);
```

Generates routes like:
- `GET /users/{user}/photos` → index
- `POST /users/{user}/photos` → store
- `GET /users/{user}/photos/{photo}` → show

**Controller:**
```php
public function index(User $user)
{
    $photos = $user->photos;
    return view('photos.index', compact('photos'));
}
```

### Shallow Nesting
Avoid deeply nested URLs:

```php
Route::resource('users.photos', PhotoController::class)->shallow();
```

Generates:
- `GET /users/{user}/photos` → index
- `POST /users/{user}/photos` → store
- `GET /photos/{photo}` → show (not `/users/{user}/photos/{photo}`)
- `PUT /photos/{photo}` → update
- `DELETE /photos/{photo}` → destroy

---

## Blade Templating Engine

### What is Blade?
Blade is Laravel's simple yet powerful templating engine. Unlike other PHP templating engines, Blade doesn't restrict you from using plain PHP code in your templates.

**File extension:** `.blade.php`  
**Location:** `resources/views/`

### Creating Templates

#### Basic Template
```blade
<!-- resources/views/welcome.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>Welcome</title>
</head>
<body>
    <h1>Hello, {{ $name }}</h1>
</body>
</html>
```

### PHP Output in Blade

#### Displaying Data
```blade
<!-- Escaped output (prevents XSS) -->
{{ $variable }}

<!-- Unescaped output (use carefully!) -->
{!! $htmlContent !!}

<!-- Default value if variable is null -->
{{ $name ?? 'Guest' }}

<!-- Ternary operator -->
{{ isset($name) ? $name : 'Guest' }}
```

#### Security Note
- `{{ }}` automatically escapes HTML entities (safe)
- `{!! !!}` does NOT escape (use only with trusted data)

### Control Structures

#### If Statements
```blade
@if(count($records) === 1)
    I have one record!
@elseif(count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif
```

#### Unless Statement
```blade
@unless(auth()->check())
    You are not signed in.
@endunless
```

#### Switch Statement
```blade
@switch($i)
    @case(1)
        First case
        @break

    @case(2)
        Second case
        @break

    @default
        Default case
@endswitch
```

### Loops

#### For Loop
```blade
@for($i = 0; $i < 10; $i++)
    The current value is {{ $i }}
@endfor
```

#### Foreach Loop
```blade
@foreach($users as $user)
    <p>User ID: {{ $user->id }}</p>
@endforeach
```

#### Forelse Loop (with empty check)
```blade
@forelse($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users found</p>
@endforelse
```

#### While Loop
```blade
@while(true)
    <p>I'm looping forever!</p>
@endwhile
```

#### Loop Variables
Blade provides `$loop` variable in loops:

```blade
@foreach($users as $user)
    @if($loop->first)
        This is the first iteration.
    @endif

    @if($loop->last)
        This is the last iteration.
    @endif

    <p>User {{ $loop->iteration }}: {{ $user->name }}</p>
    <p>Index: {{ $loop->index }}</p>
    <p>Remaining: {{ $loop->remaining }}</p>
    <p>Count: {{ $loop->count }}</p>
@endforeach
```

**Loop Properties:**

| Property | Description |
|----------|-------------|
| `$loop->index` | Current iteration (0-based) |
| `$loop->iteration` | Current iteration (1-based) |
| `$loop->remaining` | Iterations remaining |
| `$loop->count` | Total items |
| `$loop->first` | Is first iteration? |
| `$loop->last` | Is last iteration? |
| `$loop->even` | Is even iteration? |
| `$loop->odd` | Is odd iteration? |
| `$loop->depth` | Nesting level |
| `$loop->parent` | Parent loop variable (nested loops) |

### Template Inheritance

#### Layout File
```blade
<!-- resources/views/layouts/app.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'My Application')</title>
    <link rel="stylesheet" href="{{ asset('css/app.css') }}">
</head>
<body>
    @include('partials.header')
    
    <div class="container">
        @hasSection('sidebar')
            <aside>
                @yield('sidebar')
            </aside>
        @endif
        
        <main>
            @yield('content')
        </main>
    </div>
    
    @include('partials.footer')
    
    @stack('scripts')
</body>
</html>
```

#### Child Template
```blade
<!-- resources/views/home.blade.php -->
@extends('layouts.app')

@section('title', 'Home Page')

@section('sidebar')
    <h3>Sidebar Content</h3>
    <ul>
        <li>Link 1</li>
        <li>Link 2</li>
    </ul>
@endsection

@section('content')
    <h1>Welcome to Home Page</h1>
    <p>This is the main content.</p>
@endsection

@push('scripts')
    <script src="{{ asset('js/home.js') }}"></script>
@endpush
```

#### Sections vs Stacks
- **@section/@yield**: Define content areas (can be overridden)
- **@push/@stack**: Append content (multiple pushes allowed)

### Including Sub-Views

#### Basic Include
```blade
@include('partials.header')
```

#### Include with Data
```blade
@include('partials.alert', ['type' => 'success', 'message' => 'Saved!'])
```

#### Include If Exists
```blade
@includeIf('partials.sidebar')
```

#### Include When Condition is True
```blade
@includeWhen(auth()->check(), 'partials.user-menu')
```

#### Include First Available
```blade
@includeFirst(['custom.header', 'partials.header'])
```

### Components (Modern Approach)

#### Create Component
```bash
php artisan make:component Alert
```

**Component Class:** `app/View/Components/Alert.php`
```php
<?php

namespace App\View\Components;

use Illuminate\View\Component;

class Alert extends Component
{
    public $type;
    public $message;

    public function __construct($type = 'info', $message = '')
    {
        $this->type = $type;
        $this->message = $message;
    }

    public function render()
    {
        return view('components.alert');
    }
}
```

**Component View:** `resources/views/components/alert.blade.php`
```blade
<div class="alert alert-{{ $type }}">
    {{ $message }}
</div>
```

**Usage:**
```blade
<x-alert type="success" message="Operation completed!" />

<!-- Or with slot -->
<x-alert type="danger">
    Something went wrong!
</x-alert>
```

### Blade Directives Reference

#### Comments
```blade
{{-- This comment won't appear in HTML --}}
```

#### PHP Blocks
```blade
@php
    $counter = 0;
    $name = 'John';
@endphp
```

#### CSRF Token
```blade
<form method="POST">
    @csrf
    <!-- form fields -->
</form>
```

#### Method Spoofing
```blade
<form method="POST">
    @method('PUT')
    <!-- form fields -->
</form>
```

#### Assets
```blade
<link rel="stylesheet" href="{{ asset('css/style.css') }}">
<script src="{{ mix('js/app.js') }}"></script>
<img src="{{ url('images/logo.png') }}">
```

#### Authentication Checks
```blade
@auth
    <p>You are logged in as {{ auth()->user()->name }}</p>
@endauth

@guest
    <p>Please log in</p>
@endguest

@role('admin')
    <p>Admin content</p>
@endrole
```

#### Environment Checks
```blade
@production
    <!-- Production only -->
@endproduction

@env('local')
    <!-- Local environment only -->
@endenv
```

---

## Advanced Routing

### Named Routes

#### Defining Named Routes
```php
Route::get('/profile', function () {
    // ...
})->name('profile');

Route::get('/user/{id}/profile', [UserController::class, 'show'])
    ->name('user.profile');
```

#### Generating URLs
```php
// In controller
$url = route('profile');
$url = route('user.profile', ['id' => 1]);

// In Blade
<a href="{{ route('profile') }}">Profile</a>
<a href="{{ route('user.profile', ['id' => 1]) }}">User Profile</a>
```

#### Redirecting
```php
return redirect()->route('profile');
return redirect()->route('user.profile', ['id' => 1]);
```

### Secure Routes (HTTPS)

#### Force HTTPS
```php
Route::get('/payment', function () {
    // ...
})->secure();
```

Or in `AppServiceProvider`:
```php
use Illuminate\Support\Facades\URL;

public function boot()
{
    if(config('app.env') === 'production') {
        URL::forceScheme('https');
    }
}
```

### Parameter Constraints

#### Inline Constraints
```php
Route::get('/user/{id}', function ($id) {
    // Only numeric IDs
})->where('id', '[0-9]+');

Route::get('/user/{name}', function ($name) {
    // Only alphabetic names
})->where('name', '[A-Za-z]+');
```

#### Multiple Constraints
```php
Route::get('/user/{id}/{slug}', function ($id, $slug) {
    // ...
})->where(['id' => '[0-9]+', 'slug' => '[a-z-]+']);
```

#### Global Patterns
In `RouteServiceProvider`:
```php
public function boot()
{
    Route::pattern('id', '[0-9]+');
    Route::pattern('slug', '[a-z0-9-]+');
    
    parent::boot();
}
```

### Route Groups

#### Middleware Groups
```php
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/dashboard', [DashboardController::class, 'index']);
    Route::get('/profile', [ProfileController::class, 'show']);
    Route::get('/settings', [SettingsController::class, 'edit']);
});
```

#### Namespace Groups
```php
Route::namespace('Admin')->group(function () {
    Route::get('/dashboard', [DashboardController::class, 'index']);
    Route::get('/users', [UserController::class, 'index']);
});
```

#### Prefix Groups
```php
Route::prefix('admin')->group(function () {
    Route::get('/dashboard', function () {
        // URL: /admin/dashboard
    });
    
    Route::get('/users', function () {
        // URL: /admin/users
    });
});
```

#### Combined Groups
```php
Route::prefix('admin')
    ->middleware(['auth', 'admin'])
    ->name('admin.')
    ->group(function () {
        Route::get('/dashboard', [AdminController::class, 'index'])
            ->name('dashboard');
        
        Route::get('/users', [UserController::class, 'index'])
            ->name('users.index');
    });
```

Now routes are named: `admin.dashboard`, `admin.users.index`

### Route Prefixing

#### Basic Prefix
```php
Route::prefix('api/v1')->group(function () {
    Route::get('/users', [API\UserController::class, 'index']);
    Route::get('/posts', [API\PostController::class, 'index']);
});
```

URLs:
- `/api/v1/users`
- `/api/v1/posts`

#### Dynamic Prefixes
```php
Route::prefix('{locale}')->group(function () {
    Route::get('/about', function ($locale) {
        // URL: /en/about, /fr/about
    });
});
```

### Domain Routing

#### Subdomain Routing
```php
Route::domain('{account}.myapp.com')->group(function () {
    Route::get('/dashboard', function ($account) {
        // account.myapp.com/dashboard
    });
});
```

#### Multiple Domains
```php
Route::domain('admin.myapp.com')->group(function () {
    Route::get('/dashboard', [AdminController::class, 'index']);
});

Route::domain('api.myapp.com')->group(function () {
    Route::get('/users', [API\UserController::class, 'index']);
});
```

### Route Model Binding

#### Implicit Binding
Laravel automatically resolves Eloquent models:

```php
Route::get('/users/{user}', function (User $user) {
    return $user->name;
});
```

URL `/users/1` automatically fetches `User::findOrFail(1)`

#### Explicit Binding
Define custom resolution in `RouteServiceProvider`:

```php
public function boot()
{
    Route::bind('user', function ($value) {
        return User::where('username', $value)->firstOrFail();
    });
    
    parent::boot();
}
```

Now `/users/john` finds user by username instead of ID.

#### Custom Key
In Model:
```php
public function getRouteKeyName()
{
    return 'slug';
}
```

---

## URL Generation

### The Current URL

#### Get Current URL
```php
// Full URL with query string
url()->current();
URL::current();

// Full URL including query parameters
url()->full();

// Previous URL
url()->previous();
```

#### In Blade
```blade
<p>Current: {{ url()->current() }}</p>
<p>Full: {{ url()->full() }}</p>
<a href="{{ url()->previous() }}">Back</a>
```

### Generating Framework URLs

#### Using `url()` Helper
```php
// Generate URL
url('users/profile');

// With parameters
url('users/profile', ['id' => 1]);

// Secure URL
url('users/profile', [], true);
```

#### Using `route()` Helper
```php
// Named route
route('profile');

// With parameters
route('user.profile', ['id' => 1, 'tab' => 'settings']);

// Absolute URL
route('profile', [], true);
```

#### Using `action()` Helper
```php
// Controller action
action([UserController::class, 'index']);

// With parameters
action([UserController::class, 'show'], ['id' => 1]);
```

### Asset URLs

#### CSS/JS Files
```blade
<link rel="stylesheet" href="{{ asset('css/app.css') }}">
<script src="{{ asset('js/app.js') }}"></script>
```

#### Images
```blade
<img src="{{ asset('images/logo.png') }}" alt="Logo">
```

#### Mixed Assets (with versioning)
```blade
<link rel="stylesheet" href="{{ mix('css/app.css') }}">
<script src="{{ mix('js/app.js') }}"></script>
```

### Generation Shortcuts

#### Quick Helpers
```php
// Home URL
url('/');

// Asset URL
asset('file.pdf');

// Route URL
route('home');

// Action URL
action([HomeController::class, 'index']);

// Signed URL (for temporary access)
URL::signedRoute('downloads.file', ['file' => 'report.pdf']);

// Temporary signed URL (expires)
URL::temporarySignedRoute(
    'downloads.file',
    now()->addHours(2),
    ['file' => 'report.pdf']
);
```

### URL Examples

**Complete Example:**
```blade
<!DOCTYPE html>
<html>
<head>
    <title>{{ config('app.name') }}</title>
    
    <!-- Assets -->
    <link rel="stylesheet" href="{{ asset('css/style.css') }}">
</head>
<body>
    <!-- Navigation -->
    <nav>
        <a href="{{ url('/') }}">Home</a>
        <a href="{{ route('about') }}">About</a>
        <a href="{{ action([ContactController::class, 'index']) }}">Contact</a>
    </nav>
    
    <!-- Content -->
    @yield('content')
    
    <!-- Scripts -->
    <script src="{{ asset('js/app.js') }}"></script>
    @stack('scripts')
</body>
</html>
```

---

## Exam Questions & Answers

### Short Answer Questions (2-5 marks)

#### Q1: What are controllers? Why use them?
**Answer:**
Controllers group related request handling logic into classes. Benefits:
- Organized code structure
- Reusability
- Separation of concerns
- Easier testing
- Follows MVC pattern

#### Q2: How do you create a resource controller?
**Answer:**
```bash
php artisan make:controller PhotoController --resource
```
This creates controller with 7 methods: index, create, store, show, edit, update, destroy.

#### Q3: What is middleware? Give examples.
**Answer:**
Middleware filters HTTP requests. Examples:
- Authentication (`auth`)
- CSRF protection
- Age verification
- Logging
- CORS handling

#### Q4: Explain RESTful resource routing.
**Answer:**
Resource routing maps HTTP verbs to CRUD operations:
- GET `/photos` → index
- POST `/photos` → store
- GET `/photos/{id}` → show
- PUT `/photos/{id}` → update
- DELETE `/photos/{id}` → destroy

#### Q5: What is Blade? List its features.
**Answer:**
Blade is Laravel's templating engine. Features:
- Template inheritance
- Control structures (if, foreach)
- Escaped output (security)
- Components
- Includes
- Stacks

#### Q6: Differentiate between `@section` and `@stack`.
**Answer:**
- **@section/@yield**: Define replaceable content areas
- **@push/@stack**: Append content (multiple pushes accumulate)

#### Q7: What are named routes? Why use them?
**Answer:**
Named routes assign names to routes for easier reference:
```php
Route::get('/profile')->name('profile');
route('profile'); // Generate URL
```
Benefits: Flexible URL changes, cleaner code, easier redirects.

#### Q8: Explain route model binding.
**Answer:**
Automatically resolves Eloquent models from route parameters:
```php
Route::get('/users/{user}', function (User $user) {
    // $user is automatically fetched
});
```

### Long Answer Questions (10-15 marks)

#### Q9: Explain template inheritance in Blade with example.
**Answer:**
[See "Template Inheritance" section - expand with complete layout example]

#### Q10: Describe RESTful resource controllers with all methods.
**Answer:**
[See "RESTful Resource Controllers" section - detail each method with code]

#### Q11: Explain advanced routing features with examples.
**Answer:**
Cover:
- Named routes
- Route groups (middleware, prefix, namespace)
- Parameter constraints
- Domain routing
- Route model binding

### Practical Questions

#### Q12: Create a blog system with resource controller.
**Answer:**

**Create controller:**
```bash
php artisan make:controller PostController --resource
php artisan make:model Post -m
```

**Routes:**
```php
Route::resource('posts', PostController::class);
```

**Controller methods:**
```php
public function index() {
    $posts = Post::latest()->paginate(10);
    return view('posts.index', compact('posts'));
}

public function create() {
    return view('posts.create');
}

public function store(Request $request) {
    $validated = $request->validate([
        'title' => 'required|max:255',
        'content' => 'required',
    ]);
    Post::create($validated);
    return redirect()->route('posts.index')->with('success', 'Created!');
}

public function show(Post $post) {
    return view('posts.show', compact('post'));
}

public function edit(Post $post) {
    return view('posts.edit', compact('post'));
}

public function update(Request $request, Post $post) {
    $post->update($request->validate([
        'title' => 'required',
        'content' => 'required',
    ]));
    return redirect()->route('posts.index')->with('success', 'Updated!');
}

public function destroy(Post $post) {
    $post->delete();
    return redirect()->route('posts.index')->with('success', 'Deleted!');
}
```

#### Q13: Create a Blade layout with header, footer, sidebar, and content sections.
**Answer:**

**Layout:** `resources/views/layouts/app.blade.php`
```blade
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'My Site')</title>
    <link rel="stylesheet" href="{{ asset('css/app.css') }}">
</head>
<body>
    @include('partials.header')
    
    <div class="container">
        @hasSection('sidebar')
            <aside>@yield('sidebar')</aside>
        @endif
        
        <main>@yield('content')</main>
    </div>
    
    @include('partials.footer')
    
    @stack('scripts')
</body>
</html>
```

**Child view:**
```blade
@extends('layouts.app')

@section('title', 'Home')

@section('sidebar')
    <h3>Links</h3>
@endsection

@section('content')
    <h1>Welcome</h1>
@endsection

@push('scripts')
    <script src="{{ asset('js/home.js') }}"></script>
@endpush
```

---

## Quick Reference

### Controller Commands
```bash
php artisan make:controller Name
php artisan make:controller Name --resource
php artisan make:controller Name --api
php artisan make:controller Name --invokable
```

### Blade Cheat Sheet
```blade
{{ $var }}           {{-- Escaped output --}}
{!! $html !!}        {{-- Unescaped --}}
@if(condition)       {{-- If statement --}}
@foreach($items as $item)  {{-- Loop --}}
@extends('layout')   {{-- Inheritance --}}
@section('name')     {{-- Section --}}
@include('partial')  {{-- Include --}}
@csrf                {{-- CSRF token --}}
```

### Route Groups
```php
Route::prefix('admin')
    ->middleware('auth')
    ->name('admin.')
    ->group(function() {
        // Routes here
    });
```

---

## Summary Checklist

After studying Unit III, you should be able to:
- ✅ Create and use controllers
- ✅ Route to controller actions
- ✅ Apply middleware to controllers
- ✅ Build RESTful resource controllers
- ✅ Use Blade templating engine
- ✅ Implement template inheritance
- ✅ Use control structures in Blade
- ✅ Create and use components
- ✅ Define named routes
- ✅ Apply route constraints
- ✅ Use route groups
- ✅ Implement domain routing
- ✅ Generate URLs programmatically

---

**Previous:** [Unit II: Request, Routing & Responses](../Unit_II_Request_Routing_Responses/02_Request_Routing_Responses.md)  
**Next:** [Exam Preparation Guide](../Exam_Preparation/README.md)
