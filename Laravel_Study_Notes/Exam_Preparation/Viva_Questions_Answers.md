# 🔥 Complete Viva Questions & Answers - Laravel

## 📋 200+ Essential Viva Questions with Answers

Organized by topic with practical examples and folder locations.

---

## Table of Contents
- [Basic Laravel Questions](#basic-laravel-questions)
- [File Structure & Folders](#file-structure--folders)
- [Routing Questions](#routing-questions)
- [Controller Questions](#controller-questions)
- [Blade & Views Questions](#blade--views-questions)
- [Middleware Questions](#middleware-questions)
- [Cookie & Session Questions](#cookie--session-questions)
- [Database & Migration Questions](#database--migration-questions)
- [Artisan Commands Questions](#artisan-commands-questions)
- [Validation Questions](#validation-questions)
- [Advanced Questions](#advanced-questions)

---

## Basic Laravel Questions

### Q1: What is Laravel?
**Answer:** Laravel is a free, open-source PHP web framework following MVC architectural pattern, created by Taylor Otwell in 2011.

### Q2: What does MVC stand for?
**Answer:** Model-View-Controller
- **Model**: Handles data and database operations
- **View**: Handles UI/presentation
- **Controller**: Handles user requests and coordinates Model & View

### Q3: Who created Laravel?
**Answer:** Taylor Otwell

### Q4: When was Laravel released?
**Answer:** June 2011

### Q5: What is the latest version of Laravel?
**Answer:** Laravel 11 (as of 2024-2025)

### Q6: What are the requirements for Laravel?
**Answer:**
- PHP 8.1 or higher
- Composer
- Database (MySQL, PostgreSQL, SQLite)
- Web server (Apache, Nginx)

### Q7: What is Composer?
**Answer:** Dependency manager for PHP that manages libraries and handles autoloading.

### Q8: What is Artisan?
**Answer:** Laravel's command-line interface (CLI) that provides helpful commands for development.

### Q9: What is Eloquent ORM?
**Answer:** Laravel's built-in ORM (Object-Relational Mapping) that provides Active Record implementation for database operations.

### Q10: What is Blade?
**Answer:** Laravel's templating engine that allows clean, simple syntax for creating views.

---

## File Structure & Folders

### Q11: Where do we define routes in Laravel?
**Answer:** 
- **Web routes:** `routes/web.php`
- **API routes:** `routes/api.php`
- **Console routes:** `routes/console.php`
- **Channel routes:** `routes/channels.php`

### Q12: Where are controllers stored?
**Answer:** `app/Http/Controllers/`

### Q13: Where are models stored?
**Answer:** `app/Models/`

### Q14: Where are views stored?
**Answer:** `resources/views/`

### Q15: Where are middleware files stored?
**Answer:** `app/Http/Middleware/`

### Q16: Where are migrations stored?
**Answer:** `database/migrations/`

### Q17: Where are seeders stored?
**Answer:** `database/seeders/`

### Q18: Where are factories stored?
**Answer:** `database/factories/`

### Q19: Where is the configuration stored?
**Answer:** `config/` directory

### Q20: Where is the entry point of Laravel application?
**Answer:** `public/index.php`

### Q21: Where are environment variables stored?
**Answer:** `.env` file in root directory

### Q22: Where are log files stored?
**Answer:** `storage/logs/laravel.log`

### Q23: Where are cached views stored?
**Answer:** `storage/framework/views/`

### Q24: Where are session files stored?
**Answer:** `storage/framework/sessions/`

### Q25: Where is the service provider registered?
**Answer:** `config/app.php` in the `providers` array

### Q26: Where do you register middleware?
**Answer:** `app/Http/Kernel.php` in `$routeMiddleware` or `$middleware` array

### Q27: What is the vendor folder?
**Answer:** Contains all Composer dependencies (DO NOT MODIFY)

### Q28: Where are language files stored?
**Answer:** `resources/lang/` or `lang/` (in newer versions)

### Q29: Where are CSS/JS assets stored?
**Answer:** 
- Source files: `resources/css/`, `resources/js/`
- Compiled files: `public/css/`, `public/js/`

### Q30: Where are tests stored?
**Answer:** `tests/` directory (Feature/ and Unit/ subdirectories)

---

## Routing Questions

### Q31: What is routing in Laravel?
**Answer:** Routing maps URLs to specific actions (closures or controller methods).

### Q32: How do you define a basic GET route?
**Answer:**
```php
Route::get('/url', function() {
    return 'Response';
});
```

### Q33: What are route parameters?
**Answer:** Variables captured from URL segments:
```php
Route::get('/user/{id}', function($id) {
    return $id;
});
```

### Q34: How do you make a parameter optional?
**Answer:** Add `?` and provide default value:
```php
Route::get('/user/{name?}', function($name = 'Guest') {
    return $name;
});
```

### Q35: How do you add constraints to route parameters?
**Answer:**
```php
Route::get('/user/{id}', function($id) {
    return $id;
})->where('id', '[0-9]+');
```

### Q36: What is a named route?
**Answer:** A route with an assigned name for easier reference:
```php
Route::get('/profile', function() {})->name('profile');
```

### Q37: How do you generate URL from named route?
**Answer:**
```php
route('profile');
```

### Q38: What is route grouping?
**Answer:** Grouping multiple routes with shared attributes:
```php
Route::middleware(['auth'])->group(function() {
    Route::get('/dashboard', function() {});
});
```

### Q39: What is route prefixing?
**Answer:** Adding common prefix to group of routes:
```php
Route::prefix('admin')->group(function() {
    Route::get('/dashboard', function() {});
});
```

### Q40: What is fallback routing?
**Answer:** A catch-all route that handles unmatched URLs:
```php
Route::fallback(function() {
    return "Page Not Found";
});
```

### Q41: Where should fallback route be placed?
**Answer:** ALWAYS at the END of routes file, after all other routes.

### Q42: How do you redirect to a route?
**Answer:**
```php
return redirect()->route('profile');
```

### Q43: What is route caching?
**Answer:** Caching routes for faster loading in production:
```bash
php artisan route:cache
```

### Q44: How do you list all routes?
**Answer:**
```bash
php artisan route:list
```

### Q45: What are the different HTTP methods in routing?
**Answer:**
- `Route::get()` - Retrieve data
- `Route::post()` - Create data
- `Route::put()` - Update/replace data
- `Route::patch()` - Partial update
- `Route::delete()` - Delete data
- `Route::any()` - All methods
- `Route::match()` - Specific methods

### Q46: How do you pass multiple constraints?
**Answer:**
```php
Route::get('/user/{id}/{slug}', function($id, $slug) {
    // ...
})->where(['id' => '[0-9]+', 'slug' => '[a-z-]+']);
```

### Q47: What is resource routing?
**Answer:** Single line that creates all CRUD routes:
```php
Route::resource('posts', PostController::class);
```

### Q48: What routes does resource routing create?
**Answer:**
- GET `/posts` → index
- GET `/posts/create` → create
- POST `/posts` → store
- GET `/posts/{id}` → show
- GET `/posts/{id}/edit` → edit
- PUT `/posts/{id}` → update
- DELETE `/posts/{id}` → destroy

### Q49: How do you create a route that accepts only numeric ID?
**Answer:**
```php
Route::get('/product/{id}', function($id) {
    return $id;
})->where('id', '[0-9]+');
```

### Q50: How do you create a route with alphabetic constraint?
**Answer:**
```php
Route::get('/user/{name}', function($name) {
    return $name;
})->where('name', '[A-Za-z]+');
```

---

## Controller Questions

### Q51: What is a controller?
**Answer:** A class that groups related request handling logic.

### Q52: Where are controllers stored?
**Answer:** `app/Http/Controllers/`

### Q53: How do you create a controller?
**Answer:**
```bash
php artisan make:controller UserController
```

### Q54: How do you create a resource controller?
**Answer:**
```bash
php artisan make:controller PostController --resource
```

### Q55: What methods does a resource controller have?
**Answer:**
1. `index()` - List all
2. `create()` - Show create form
3. `store()` - Save new
4. `show()` - Display one
5. `edit()` - Show edit form
6. `update()` - Update
7. `destroy()` - Delete

### Q56: How do you route to a controller?
**Answer:**
```php
Route::get('/users', [UserController::class, 'index']);
```

### Q57: What is an invokable controller?
**Answer:** A controller with single action:
```bash
php artisan make:controller ShowProfile --invokable
```

### Q58: How do you apply middleware in controller?
**Answer:**
```php
public function __construct() {
    $this->middleware('auth');
}
```

### Q59: What is dependency injection in controllers?
**Answer:** Laravel automatically injects required dependencies:
```php
public function store(Request $request) {
    // $request is automatically injected
}
```

### Q60: How do you return a view from controller?
**Answer:**
```php
return view('users.index', compact('users'));
```

### Q61: How do you redirect from controller?
**Answer:**
```php
return redirect()->route('users.index')
    ->with('success', 'Created!');
```

### Q62: What is route model binding?
**Answer:** Automatically resolves Eloquent models:
```php
Route::get('/users/{user}', function(User $user) {
    // $user automatically fetched
});
```

### Q63: How do you validate request in controller?
**Answer:**
```php
$validated = $request->validate([
    'name' => 'required|string|max:255',
]);
```

### Q64: What is the difference between basic and resource controller?
**Answer:**
- **Basic:** Custom methods as needed
- **Resource:** Predefined CRUD methods following RESTful convention

### Q65: How do you create an API controller?
**Answer:**
```bash
php artisan make:controller API/UserController --api
```

---

## Blade & Views Questions

### Q66: What is Blade?
**Answer:** Laravel's templating engine for creating views.

### Q67: What is the file extension for Blade templates?
**Answer:** `.blade.php`

### Q68: Where are Blade views stored?
**Answer:** `resources/views/`

### Q69: How do you display a variable in Blade?
**Answer:**
```blade
{{ $variable }}
```

### Q70: What is the difference between `{{ }}` and `{!! !!}`?
**Answer:**
- `{{ }}` - Escapes HTML (safe from XSS)
- `{!! !!}` - Does NOT escape (use carefully)

### Q71: How do you write an if statement in Blade?
**Answer:**
```blade
@if($condition)
    Content
@endif
```

### Q72: How do you write a foreach loop in Blade?
**Answer:**
```blade
@foreach($items as $item)
    {{ $item }}
@endforeach
```

### Q73: What is template inheritance?
**Answer:** Defining a base layout and extending it in child views.

### Q74: What directive is used to extend a layout?
**Answer:** `@extends('layouts.app')`

### Q75: What is the difference between @section and @yield?
**Answer:**
- `@section` - Defines content in child view
- `@yield` - Defines placeholder in layout

### Q76: How do you include a sub-view?
**Answer:**
```blade
@include('partials.header')
```

### Q77: What is @csrf?
**Answer:** Generates CSRF token field for form protection.

### Q78: What is @method used for?
**Answer:** Method spoofing for PUT, PATCH, DELETE in forms:
```blade
@method('PUT')
```

### Q79: How do you access old input values?
**Answer:**
```blade
value="{{ old('name') }}"
```

### Q80: How do you display validation errors?
**Answer:**
```blade
@error('name')
    <span>{{ $message }}</span>
@enderror
```

### Q81: What is @forelse?
**Answer:** Foreach with empty check:
```blade
@forelse($items as $item)
    {{ $item }}
@empty
    No items
@endforelse
```

### Q82: What is the $loop variable?
**Answer:** Provides loop information:
- `$loop->index` - Current index (0-based)
- `$loop->iteration` - Current iteration (1-based)
- `$loop->count` - Total items
- `$loop->first` - Is first iteration?
- `$loop->last` - Is last iteration?

### Q83: How do you generate asset URL?
**Answer:**
```blade
{{ asset('css/style.css') }}
```

### Q84: What is @stack and @push?
**Answer:**
- `@stack` - Defines stack in layout
- `@push` - Adds content to stack

### Q85: How do you write comments in Blade?
**Answer:**
```blade
{{-- This is a comment --}}
```

---

## Middleware Questions

### Q86: What is middleware?
**Answer:** Filters that process HTTP requests before reaching controllers.

### Q87: Where are middleware files stored?
**Answer:** `app/Http/Middleware/`

### Q88: How do you create middleware?
**Answer:**
```bash
php artisan make:middleware CheckAge
```

### Q89: Where do you register middleware?
**Answer:** `app/Http/Kernel.php`

### Q90: What is the handle method in middleware?
**Answer:** The main method that processes the request:
```php
public function handle(Request $request, Closure $next) {
    // Logic here
    return $next($request);
}
```

### Q91: How do you apply middleware to routes?
**Answer:**
```php
Route::get('/profile', function() {})->middleware('auth');
```

### Q92: How do you apply middleware to controller?
**Answer:**
```php
public function __construct() {
    $this->middleware('auth');
}
```

### Q93: What are Laravel's default middleware groups?
**Answer:**
- **web** - Session, CSRF, cookies
- **api** - Rate limiting, bindings

### Q94: What is the difference between $middleware and $routeMiddleware?
**Answer:**
- `$middleware` - Runs on ALL requests (global)
- `$routeMiddleware` - Applied to specific routes

### Q95: Give examples of common middleware.
**Answer:**
- `auth` - Authentication check
- `csrf` - CSRF protection
- `cors` - Cross-Origin Resource Sharing
- `throttle` - Rate limiting
- `verified` - Email verification

### Q96: How do you pass parameters to middleware?
**Answer:**
```php
public function handle(Request $request, Closure $next, $role) {
    if ($role !== 'admin') {
        return response('Access Denied', 403);
    }
    return $next($request);
}

// Route:
Route::get('/admin', function() {})->middleware('role:admin');
```

### Q97: How do you check if middleware is working?
**Answer:** Add `dd()` or `echo` statements and test the route.

### Q98: Can you apply multiple middleware to one route?
**Answer:** Yes:
```php
Route::get('/profile', function() {})->middleware(['auth', 'verified']);
```

### Q99: What happens if middleware returns a response?
**Answer:** Request stops and that response is sent (doesn't reach controller).

### Q100: How do you exclude middleware from specific methods?
**Answer:**
```php
public function __construct() {
    $this->middleware('auth')->except('index', 'show');
}
```

---

## Cookie & Session Questions

### Q101: What is a cookie?
**Answer:** Small piece of data stored in user's browser.

### Q102: How do you create a cookie?
**Answer:**
```php
return response('Message')->cookie('name', 'value', 60);
```

### Q103: How do you retrieve a cookie?
**Answer:**
```php
$value = $request->cookie('name');
```

### Q104: How do you delete a cookie?
**Answer:**
```php
return response('Message')->cookie(Cookie::forget('name'));
```

### Q105: What is the Cookie facade?
**Answer:** `Illuminate\Support\Facades\Cookie` - Provides static methods for cookie operations.

### Q106: How do you queue a cookie?
**Answer:**
```php
Cookie::queue('name', 'value', 60);
```

### Q107: What is a session?
**Answer:** Server-side storage of user data across multiple requests.

### Q108: How do you store data in session?
**Answer:**
```php
session(['key' => 'value']);
// or
$request->session()->put('key', 'value');
```

### Q109: How do you retrieve session data?
**Answer:**
```php
$value = session('key');
// or
$value = $request->session()->get('key');
```

### Q110: How do you delete session data?
**Answer:**
```php
session()->forget('key');
```

### Q111: What is flash data?
**Answer:** Session data available only for the next request:
```php
return redirect('/home')->with('status', 'Success!');
```

### Q112: What is the difference between cookie and session?
**Answer:**
- **Cookie:** Stored in browser, less secure, size limit 4KB
- **Session:** Stored on server, more secure, no size limit

### Q113: Where are session files stored?
**Answer:** `storage/framework/sessions/`

### Q114: How do you check if cookie exists?
**Answer:**
```php
if ($request->hasCookie('name')) {
    // Cookie exists
}
```

### Q115: What are cookie parameters?
**Answer:**
```php
cookie('name', 'value', $minutes, $path, $domain, $secure, $httpOnly)
```

---

## Database & Migration Questions

### Q116: What is a migration?
**Answer:** Version control for database schema.

### Q117: Where are migrations stored?
**Answer:** `database/migrations/`

### Q118: How do you create a migration?
**Answer:**
```bash
php artisan make:migration create_users_table
```

### Q119: How do you run migrations?
**Answer:**
```bash
php artisan migrate
```

### Q120: How do you rollback migrations?
**Answer:**
```bash
php artisan migrate:rollback
```

### Q121: What is a seeder?
**Answer:** Populates database with sample data.

### Q122: Where are seeders stored?
**Answer:** `database/seeders/`

### Q123: How do you run seeders?
**Answer:**
```bash
php artisan db:seed
```

### Q124: What is Eloquent?
**Answer:** Laravel's ORM for database operations.

### Q125: How do you retrieve all records?
**Answer:**
```php
User::all();
```

### Q126: How do you find a record by ID?
**Answer:**
```php
User::find(1);
```

### Q127: How do you create a record?
**Answer:**
```php
User::create(['name' => 'John', 'email' => 'john@example.com']);
```

### Q128: How do you update a record?
**Answer:**
```php
$user->update(['name' => 'Jane']);
```

### Q129: How do you delete a record?
**Answer:**
```php
$user->delete();
```

### Q130: What is $fillable in model?
**Answer:** Array of fields that can be mass-assigned:
```php
protected $fillable = ['name', 'email'];
```

---

## Artisan Commands Questions

### Q131: What is Artisan?
**Answer:** Laravel's command-line interface.

### Q132: How do you start development server?
**Answer:**
```bash
php artisan serve
```

### Q133: How do you create a controller?
**Answer:**
```bash
php artisan make:controller UserController
```

### Q134: How do you create a model with migration?
**Answer:**
```bash
php artisan make:model User -m
```

### Q135: How do you clear all caches?
**Answer:**
```bash
php artisan optimize:clear
```

### Q136: How do you list all routes?
**Answer:**
```bash
php artisan route:list
```

### Q137: How do you create middleware?
**Answer:**
```bash
php artisan make:middleware CheckAge
```

### Q138: What command generates application key?
**Answer:**
```bash
php artisan key:generate
```

### Q139: How do you enable maintenance mode?
**Answer:**
```bash
php artisan down
```

### Q140: How do you disable maintenance mode?
**Answer:**
```bash
php artisan up
```

---

## Validation Questions

### Q141: How do you validate request data?
**Answer:**
```php
$validated = $request->validate([
    'name' => 'required|string|max:255',
    'email' => 'required|email',
]);
```

### Q142: What is the difference between required and nullable?
**Answer:**
- `required` - Field must be present and not empty
- `nullable` - Field can be null

### Q143: How do you add custom error messages?
**Answer:**
```php
$request->validate([
    'email' => 'required|email',
], [
    'email.required' => 'Email is required',
    'email.email' => 'Please enter valid email',
]);
```

### Q144: How do you display validation errors?
**Answer:**
```blade
@error('email')
    <span>{{ $message }}</span>
@enderror
```

### Q145: What is the unique validation rule?
**Answer:**
```php
'email' => 'required|email|unique:users'
```

### Q146: What is confirmed validation rule?
**Answer:** Checks if field matches `{field}_confirmation`:
```php
'password' => 'required|confirmed'
// Requires password_confirmation field
```

### Q147: How do you validate file uploads?
**Answer:**
```php
'photo' => 'required|image|mimes:jpeg,png|max:2048'
```

### Q148: What does max:255 mean?
**Answer:** Maximum length of 255 characters.

### Q149: How do you validate minimum length?
**Answer:**
```php
'password' => 'required|min:8'
```

### Q150: How do you redirect back with validation errors?
**Answer:** Laravel does this automatically when validation fails.

---

## Advanced Questions

### Q151: What is service provider?
**Answer:** Bootstraps application services and registers bindings.

### Q152: What is service container?
**Answer:** Laravel's dependency injection container.

### Q153: What is facade?
**Answer:** Static interface to classes available in the service container.

### Q154: What is the purpose of .env file?
**Answer:** Stores environment-specific configuration (database credentials, API keys, etc.)

### Q155: What is route model binding?
**Answer:** Automatically injects model instances into routes/controllers.

### Q156: What is the difference between PUT and PATCH?
**Answer:**
- **PUT** - Complete replacement of resource
- **PATCH** - Partial update of resource

### Q157: What is CSRF protection?
**Answer:** Cross-Site Request Forgery protection using tokens.

### Q158: How do you generate signed URLs?
**Answer:**
```php
URL::signedRoute('route.name');
```

### Q159: What are Laravel packages?
**Answer:** Reusable code libraries that can be added to Laravel applications.

### Q160: What is queue in Laravel?
**Answer:** System for processing time-consuming tasks in the background.

### Q161: How do you cache configuration?
**Answer:**
```bash
php artisan config:cache
```

### Q162: What is the difference between web.php and api.php?
**Answer:**
- **web.php** - Web routes with session, CSRF protection
- **api.php** - API routes, stateless, prefixed with /api

### Q163: What is the N+1 query problem?
**Answer:** Inefficient database queries in relationships, solved using eager loading:
```php
User::with('posts')->get();
```

### Q164: What is eager loading?
**Answer:** Loading relationships in a single query to avoid N+1 problem.

### Q165: How do you define relationship in Eloquent?
**Answer:**
```php
// One to Many
public function posts() {
    return $this->hasMany(Post::class);
}

// Belongs To
public function user() {
    return $this->belongsTo(User::class);
}
```

### Q166: What is polymorphic relationship?
**Answer:** A relationship where a model can belong to multiple other models.

### Q167: What is Laravel Mix?
**Answer:** Wrapper around Webpack for compiling assets (CSS, JS).

### Q168: What is the difference between compact() and with()?
**Answer:**
- `compact('var1', 'var2')` - Creates array from variables
- `with('key', 'value')` - Chainable method to pass data

### Q169: How do you share data with all views?
**Answer:**
```php
View::share('key', 'value');
```

### Q170: What is Blade component?
**Answer:** Reusable view element with its own logic:
```blade
<x-alert type="success">Message</x-alert>
```

---

## 🎯 Practical Viva Questions (Most Important)

### Q171: If you get "Class not found" error, what do you do?
**Answer:**
```bash
composer dump-autoload
```

### Q172: If route is not working, what do you check?
**Answer:**
1. Check route definition in `routes/web.php`
2. Run `php artisan route:list`
3. Clear route cache: `php artisan route:clear`

### Q173: If view is not found, what do you check?
**Answer:**
1. Check file name and location in `resources/views/`
2. Clear view cache: `php artisan view:clear`
3. Check view name in controller

### Q174: How do you debug in Laravel?
**Answer:**
```php
dd($variable);  // Dump and die
dump($variable); // Dump without stopping
```

### Q175: Where do you check errors?
**Answer:** `storage/logs/laravel.log`

### Q176: If database migration fails, what do you do?
**Answer:**
1. Check database connection in `.env`
2. Check migration file syntax
3. Run `php artisan migrate:status`

### Q177: How do you check Laravel version?
**Answer:**
```bash
php artisan --version
```

### Q178: What command creates complete CRUD?
**Answer:**
```bash
php artisan make:model Post -mfs
php artisan make:controller PostController --resource
```

### Q179: How do you access current route name?
**Answer:**
```php
Route::currentRouteName();
```

### Q180: How do you get current URL?
**Answer:**
```php
url()->current();
url()->full();
```

---

## 🔥 Most Asked Viva Questions (Top 20)

1. **Where do we define routes?** → `routes/web.php`
2. **Where are controllers stored?** → `app/Http/Controllers/`
3. **Where are views stored?** → `resources/views/`
4. **How do you create a controller?** → `php artisan make:controller Name`
5. **What is middleware?** → HTTP request filter
6. **Where do you register middleware?** → `app/Http/Kernel.php`
7. **How do you pass data to view?** → `compact()`, `with()`, array
8. **What is Blade?** → Laravel's templating engine
9. **How do you display variable in Blade?** → `{{ $variable }}`
10. **What is @csrf?** → CSRF token for form protection
11. **How do you create a cookie?** → `response()->cookie()`
12. **How do you retrieve cookie?** → `$request->cookie('name')`
13. **How do you validate form?** → `$request->validate()`
14. **How do you redirect?** → `redirect()->route()`
15. **What is compact()?** → Creates array from variables
16. **What is implode()?** → Joins array elements with string
17. **Where are migrations?** → `database/migrations/`
18. **How do you run migrations?** → `php artisan migrate`
19. **What is fallback route?** → Catch-all for unmatched URLs
20. **Where should fallback be placed?** → At the END of routes file

---

## 💡 Tips for Viva

### ✅ Do's:
- Speak confidently and clearly
- Give examples when possible
- Mention file locations
- Explain with practical scenarios
- If don't know, say "I'm not sure but I think..."

### ❌ Don'ts:
- Don't panic if you don't know
- Don't give wrong information
- Don't be too brief
- Don't argue with examiner

### 🎯 Common Viva Scenarios:

**Scenario 1: "Show me how to create a route"**
```php
// Answer:
// File: routes/web.php
Route::get('/hello', function() {
    return 'Hello World';
});
```

**Scenario 2: "Where would you create a controller?"**
```bash
# Answer:
# Command:
php artisan make:controller UserController

# Location: app/Http/Controllers/UserController.php
```

**Scenario 3: "How to pass array to view?"**
```php
# Answer:
$cities = ['Delhi', 'Mumbai', 'Bangalore'];
return view('cities', compact('cities'));

// In view:
@foreach($cities as $city)
    {{ $city }}
@endforeach
```

**Scenario 4: "What if route constraint fails?"**
```
# Answer:
// Returns 404 Not Found error
// Example: /user/abc fails if constraint is [0-9]+
```

---

## 📝 Quick Reference: File Locations

| Component | Location |
|-----------|----------|
| Routes | `routes/web.php` |
| Controllers | `app/Http/Controllers/` |
| Models | `app/Models/` |
| Views | `resources/views/` |
| Middleware | `app/Http/Middleware/` |
| Migrations | `database/migrations/` |
| Seeders | `database/seeders/` |
| Factories | `database/factories/` |
| Config | `config/` |
| Environment | `.env` |
| Logs | `storage/logs/` |
| Public files | `public/` |
| Entry point | `public/index.php` |
| Kernel | `app/Http/Kernel.php` |
| Service Providers | `app/Providers/` |

---

**Perfect for viva preparation! Practice these daily! 🎓**

---

**Back to:** [Study Guide](../STUDY_GUIDE.md) | [Practical Questions](High_Probability_Practical_Questions.md)
