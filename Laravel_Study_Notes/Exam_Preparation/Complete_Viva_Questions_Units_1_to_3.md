# 🎯 Complete Viva Questions Bank - Units 1 to 3

## 📋 Comprehensive Laravel Viva Preparation

**Coverage**: Unit I, II, III  
**Total Questions**: 150+  
**Includes**: Basic, Advanced, Confusing Questions with Answers

---

## 🔥 UNIT I: Getting Started with MVC Laravel Framework

### Section 1.1: MVC Framework Basics

**Q1: What is MVC?**  
**Answer**: MVC (Model-View-Controller) is an architectural pattern that separates an application into three components:
- **Model**: Manages data and business logic
- **View**: Handles UI/presentation layer
- **Controller**: Processes user input and coordinates Model & View

**Q2: What is the flow of MVC?**  
**Answer**:  
```
User Request → Controller → Model (Database)
                   ↓
               View (Response)
```

**Q3: What are the benefits of MVC?**  
**Answer**:
- Separation of concerns
- Code reusability
- Easy maintenance
- Parallel development
- Better testability

**Q4: Where does the request first arrive in MVC?**  
**Answer**: Controller

**Q5: Which component handles database operations?**  
**Answer**: Model

---

### Section 1.2: Laravel Framework Overview

**Q6: What is Laravel?**  
**Answer**: Laravel is a free, open-source PHP web framework created by Taylor Otwell in 2011, following the MVC architectural pattern.

**Q7: Who created Laravel and when?**  
**Answer**: Taylor Otwell in June 2011

**Q8: What is the latest version of Laravel?**  
**Answer**: Laravel 11 (as of 2024-2025)

**Q9: List 5 key features of Laravel**  
**Answer**:
1. Eloquent ORM
2. Blade templating engine
3. Artisan CLI
4. Built-in authentication
5. Database migrations

**Q10: What is Eloquent ORM?**  
**Answer**: Eloquent is Laravel's built-in Object-Relational Mapping (ORM) that provides Active Record implementation for database operations.

**Q11: What is Blade?**  
**Answer**: Blade is Laravel's lightweight, powerful templating engine that doesn't restrict you from using plain PHP code.

**Q12: What are Laravel's security features?**  
**Answer**:
- CSRF protection
- Password hashing
- SQL injection prevention
- XSS protection

---

### Section 1.3: Composer

**Q13: What is Composer?**  
**Answer**: Composer is a dependency manager for PHP that allows you to declare and manage libraries your project depends on.

**Q14: What is the difference between composer.json and composer.lock?**  
**Answer**:
- **composer.json**: Defines required packages and version constraints
- **composer.lock**: Records exact installed versions for consistency across environments

**Q15: What command installs dependencies?**  
**Answer**: `composer install`

**Q16: What command updates dependencies?**  
**Answer**: `composer update`

**Q17: What is autoloading in Composer?**  
**Answer**: Autoloading automatically loads PHP classes without manual `require` statements, using PSR-4 standard.

**Q18: What command dumps autoload files?**  
**Answer**: `composer dump-autoload`

---

### Section 1.4: Installation

**Q19: What are the prerequisites for Laravel?**  
**Answer**:
- PHP >= 8.1
- Composer
- Web Server (Apache/Nginx)
- Database (MySQL/PostgreSQL/SQLite)

**Q20: What are the three methods to install Laravel?**  
**Answer**:
1. Via Composer: `composer create-project laravel/laravel project-name`
2. Via Laravel Installer: `laravel new project-name`
3. Via Git Clone: `git clone https://github.com/laravel/laravel.git`

**Q21: What is the purpose of .env file?**  
**Answer**: The .env file stores environment-specific configuration like database credentials, API keys, and debug mode status. It keeps sensitive data out of version control.

**Q22: What command generates the application key?**  
**Answer**: `php artisan key:generate`

**Q23: What file should you copy to create .env?**  
**Answer**: `.env.example`

---

### Section 1.5: Directory Structure

**Q24: Where are controllers stored?**  
**Answer**: `app/Http/Controllers/`

**Q25: Where are models stored?**  
**Answer**: `app/Models/`

**Q26: Where are views stored?**  
**Answer**: `resources/views/`

**Q27: Where are routes defined?**  
**Answer**: `routes/web.php` (web routes) and `routes/api.php` (API routes)

**Q28: Where is the entry point of Laravel?**  
**Answer**: `public/index.php`

**Q29: What is the purpose of the vendor folder?**  
**Answer**: The vendor folder contains all Composer dependencies. **DO NOT MODIFY** this folder.

**Q30: Where are migrations stored?**  
**Answer**: `database/migrations/`

**Q31: Where are log files stored?**  
**Answer**: `storage/logs/laravel.log`

**Q32: Where are session files stored?**  
**Answer**: `storage/framework/sessions/`

**Q33: Where is the application configuration stored?**  
**Answer**: `config/` directory

**Q34: What is stored in the public folder?**  
**Answer**: Publicly accessible files like CSS, JS, images, and index.php (entry point)

**Q35: Where are middleware files stored?**  
**Answer**: `app/Http/Middleware/`

---

### Section 1.6: Artisan CLI

**Q36: What is Artisan?**  
**Answer**: Artisan is Laravel's command-line interface (CLI) that provides helpful commands for development.

**Q37: How do you start the development server?**  
**Answer**: `php artisan serve`

**Q38: How do you create a controller?**  
**Answer**: `php artisan make:controller UserController`

**Q39: How do you create a resource controller?**  
**Answer**: `php artisan make:controller UserController --resource`

**Q40: How do you create a model with migration?**  
**Answer**: `php artisan make:model User -m`

**Q41: What does the -m flag stand for?**  
**Answer**: Migration

**Q42: How do you create a model with migration, factory, and seeder?**  
**Answer**: `php artisan make:model User -mfs`

**Q43: What command runs migrations?**  
**Answer**: `php artisan migrate`

**Q44: How do you rollback the last migration?**  
**Answer**: `php artisan migrate:rollback`

**Q45: What command clears all caches?**  
**Answer**: `php artisan optimize:clear`

**Q46: How do you list all routes?**  
**Answer**: `php artisan route:list`

**Q47: How do you enable maintenance mode?**  
**Answer**: `php artisan down`

**Q48: How do you disable maintenance mode?**  
**Answer**: `php artisan up`

**Q49: What command creates middleware?**  
**Answer**: `php artisan make:middleware CheckAge`

**Q50: How do you check Laravel version?**  
**Answer**: `php artisan --version`

---

### 🔴 CONFUSING QUESTIONS - Unit I

**Q51: What's the difference between `composer install` and `composer update`?**  
**Answer**:
- **composer install**: Installs exact versions from composer.lock (safe for production)
- **composer update**: Updates packages to latest versions per composer.json and updates .lock file

**Q52: What's the difference between Model and Controller?**  
**Answer**:
- **Model**: Represents data and business logic, handles database operations
- **Controller**: Handles user requests, coordinates between Model and View

**Q53: Why do we need both .env and .env.example?**  
**Answer**:
- **.env**: Contains actual credentials (NEVER commit to Git)
- **.env.example**: Template file with placeholder values (commit to Git for reference)

**Q54: What's the difference between `app/Http/Kernel.php` and `app/Console/Kernel.php`?**  
**Answer**:
- **Http/Kernel.php**: Handles HTTP middleware (web requests)
- **Console/Kernel.php**: Handles Artisan commands and task scheduling

**Q55: Is Laravel a framework or a library?**  
**Answer**: Laravel is a **framework** (provides structure and calls your code), not a library (you call library code).

**Q56: What's the difference between artisan serve and a production server?**  
**Answer**:
- **artisan serve**: Development server (PHP's built-in server), not for production
- **Production server**: Apache/Nginx with proper configuration for security and performance

---

## 🔥 UNIT II: Request, Routing & Responses

### Section 2.1: Request Lifecycle

**Q57: Explain Laravel request lifecycle in brief**  
**Answer**:
1. Request enters through `public/index.php`
2. HTTP Kernel loads middleware
3. Service providers bootstrap services
4. Router matches URL to route
5. Middleware filters request
6. Controller processes logic
7. Model interacts with database
8. View generates response
9. Response sent to browser

**Q58: What is the entry point of all HTTP requests?**  
**Answer**: `public/index.php`

**Q59: What are service providers?**  
**Answer**: Service providers bootstrap all framework services (database, queue, mail, etc.). Located in `config/app.php` providers array.

**Q60: What is the HTTP Kernel?**  
**Answer**: The HTTP Kernel handles loading middleware, bootstrapping the application, and processing requests.

---

### Section 2.2: Basic Routing

**Q61: What are routes in Laravel?**  
**Answer**: Routes define URLs and map them to specific actions (closures or controllers).

**Q62: What are the different route files in Laravel?**  
**Answer**:
- `routes/web.php` - Web routes (with session, CSRF)
- `routes/api.php` - API routes (stateless)
- `routes/console.php` - Artisan commands
- `routes/channels.php` - Broadcasting channels

**Q63: How do you define a GET route?**  
**Answer**:
```php
Route::get('/hello', function () {
    return 'Hello World';
});
```

**Q64: What HTTP methods are available in routing?**  
**Answer**:
- GET, POST, PUT, PATCH, DELETE, OPTIONS
- `Route::match()` - Multiple specific methods
- `Route::any()` - All methods

**Q65: How do you view all routes?**  
**Answer**: `php artisan route:list`

---

### Section 2.3: Routing Parameters

**Q66: What are routing parameters?**  
**Answer**: Routing parameters capture URL segments:
```php
Route::get('/user/{id}', function ($id) {
    return 'User: ' . $id;
});
```

**Q67: What's the difference between required and optional parameters?**  
**Answer**:
- **Required**: `/{id}` - Must be present
- **Optional**: `/{name?}` - Can be omitted, must have default value

**Q68: How do you add constraints to parameters?**  
**Answer**:
```php
Route::get('/user/{id}', function ($id) {
    return $id;
})->where('id', '[0-9]+');
```

**Q69: What regex pattern allows only alphabets?**  
**Answer**: `[A-Za-z]+`

**Q70: What regex pattern allows alphanumeric?**  
**Answer**: `[A-Za-z0-9]+`

**Q71: What regex pattern allows only numbers?**  
**Answer**: `[0-9]+`

**Q72: How do you add multiple constraints?**  
**Answer**:
```php
->where(['id' => '[0-9]+', 'name' => '[a-z]+'])
```

---

### Section 2.4: Views

**Q73: What are views in Laravel?**  
**Answer**: Views contain the HTML served by your application, separating presentation from business logic.

**Q74: Where are views stored?**  
**Answer**: `resources/views/`

**Q75: What is the file extension for Blade templates?**  
**Answer**: `.blade.php`

**Q76: How do you return a view?**  
**Answer**: `return view('welcome');`

**Q77: How do you access nested views?**  
**Answer**: Using dot notation: `view('pages.about')` for `pages/about.blade.php`

**Q78: How do you check if a view exists?**  
**Answer**:
```php
if (View::exists('emails.customer')) {
    // View exists
}
```

---

### Section 2.5: Passing Data to Views

**Q79: What are the methods to pass data to views?**  
**Answer**:
1. `with()` method: `view('page')->with('key', 'value')`
2. Array: `view('page', ['key' => 'value'])`
3. `compact()`: `view('page', compact('key'))`
4. Magic methods: `view('page')->withKey('value')`

**Q80: What is compact() function?**  
**Answer**: `compact()` creates an array from variables and their values:
```php
$name = 'John';
$age = 25;
compact('name', 'age'); // ['name' => 'John', 'age' => 25]
```

**Q81: What's the difference between `with()` and `compact()`?**  
**Answer**:
- **with()**: Chainable method, passes one variable at a time
- **compact()**: Creates array from variable names, can pass multiple at once

**Q82: How do you share data with all views?**  
**Answer**:
1. View Composers
2. View::share() in AppServiceProvider
3. View Creators

**Q83: What is a View Composer?**  
**Answer**: View Composers share data with specific views or groups of views when they are rendered.

---

### Section 2.6: Responses

**Q84: What are the different response types in Laravel?**  
**Answer**:
- String response
- View response
- JSON response
- File download
- Redirect
- Response with headers/cookies

**Q85: How do you return a JSON response?**  
**Answer**:
```php
return response()->json(['name' => 'John', 'age' => 25]);
```

**Q86: How do you return JSON with status code?**  
**Answer**:
```php
return response()->json(['error' => 'Not Found'], 404);
```

**Q87: How do you attach headers to response?**  
**Answer**:
```php
return response($content)
    ->header('Content-Type', 'application/json')
    ->header('X-Custom', 'Value');
```

**Q88: How do you create a cookie in response?**  
**Answer**:
```php
return response('Hello')->cookie('theme', 'dark', 60);
```

**Q89: What are the cookie parameters?**  
**Answer**: name, value, minutes, path, domain, secure, httpOnly

**Q90: How do you retrieve a cookie?**  
**Answer**: `$value = $request->cookie('name');`

**Q91: How do you delete a cookie?**  
**Answer**:
```php
return response('Bye')->cookie(cookie()->forget('name'));
```

**Q92: How do you download a file?**  
**Answer**:
```php
return response()->download(storage_path('app/file.pdf'));
```

---

### Section 2.7: Redirections

**Q93: How do you redirect to a URL?**  
**Answer**: `return redirect('/home');`

**Q94: How do you redirect to a named route?**  
**Answer**: `return redirect()->route('profile');`

**Q95: How do you redirect with parameters?**  
**Answer**:
```php
return redirect()->route('user.profile', ['id' => 1]);
```

**Q96: How do you redirect back?**  
**Answer**: `return redirect()->back();` or `return back();`

**Q97: What is flash data?**  
**Answer**: Flash data is session data available only for the next request:
```php
return redirect('/home')->with('status', 'Success!');
```

**Q98: How do you display flash message in view?**  
**Answer**:
```blade
@if(session('status'))
    <div>{{ session('status') }}</div>
@endif
```

**Q99: How do you redirect to a controller action?**  
**Answer**:
```php
return redirect()->action([UserController::class, 'index']);
```

**Q100: What are named routes?**  
**Answer**: Named routes assign names to routes for easier reference:
```php
Route::get('/profile')->name('profile');
```

---

### 🔴 CONFUSING QUESTIONS - Unit II

**Q101: What's the difference between `redirect()` and `back()`?**  
**Answer**:
- **redirect()**: Redirects to specific URL/route
- **back()**: Redirects to previous URL (shortcut for `redirect()->back()`)

**Q102: What's the difference between route parameters and query strings?**  
**Answer**:
- **Route parameters**: Part of URL path `/user/123` → `{id}`
- **Query strings**: After `?` in URL `/user?id=123` → `$request->query('id')`

**Q103: What's the difference between `response()->json()` and `json_encode()`?**  
**Answer**:
- **response()->json()**: Laravel response with proper headers
- **json_encode()**: PHP function that only converts to JSON string

**Q104: What's the difference between `view()` and `View::make()`?**  
**Answer**: No difference! `view()` is a helper function that calls `View::make()` internally.

**Q105: What's the difference between `redirect()` and `Redirect::to()`?**  
**Answer**: No difference! `redirect()` is a helper that calls `Redirect::to()`.

**Q106: Can you use `return` without any value in a route?**  
**Answer**: Yes, but it will return an empty response. Better to return something meaningful.

**Q107: What happens if you don't specify a default value for optional parameter?**  
**Answer**: Laravel throws an error. Optional parameters MUST have default values.

---

## 🔥 UNIT III: Controllers, Blade & Advanced Routing

### Section 3.1: Controllers

**Q108: What are controllers?**  
**Answer**: Controllers group related request handling logic into a single class for better organization.

**Q109: Where are controllers stored?**  
**Answer**: `app/Http/Controllers/`

**Q110: How do you create a basic controller?**  
**Answer**: `php artisan make:controller UserController`

**Q111: How do you create an invokable controller?**  
**Answer**: `php artisan make:controller ShowProfile --invokable`

**Q112: What is an invokable controller?**  
**Answer**: A controller with a single action using `__invoke()` method.

**Q113: How do you route to a controller?**  
**Answer**:
```php
Route::get('/users', [UserController::class, 'index']);
```

**Q114: What are controller best practices?**  
**Answer**:
- Keep controllers thin
- Use type hinting
- Return appropriate responses
- Follow RESTful conventions
- Don't put business logic in controllers

**Q115: What's the difference between basic and resource controller?**  
**Answer**:
- **Basic**: Custom methods as needed
- **Resource**: Predefined CRUD methods (index, create, store, show, edit, update, destroy)

---

### Section 3.2: Controller Routing

**Q116: How do you define multiple routes to a controller?**  
**Answer**:
```php
Route::get('/users', [UserController::class, 'index']);
Route::get('/users/{id}', [UserController::class, 'show']);
Route::post('/users', [UserController::class, 'store']);
```

**Q117: What is the advantage of controller over closure routes?**  
**Answer**:
- Better organization
- Reusability
- Easier testing
- Follows MVC pattern

---

### Section 3.3: Controller Middleware

**Q118: What is middleware?**  
**Answer**: Middleware filters HTTP requests entering your application (authentication, authorization, logging, etc.)

**Q119: How do you apply middleware to a controller?**  
**Answer**:
```php
public function __construct() {
    $this->middleware('auth');
}
```

**Q120: How do you apply middleware to specific methods only?**  
**Answer**:
```php
$this->middleware('auth')->only('index', 'show');
$this->middleware('log')->except('store');
```

**Q121: How do you create custom middleware?**  
**Answer**:
```bash
php artisan make:middleware CheckAge
```

**Q122: Where do you register custom middleware?**  
**Answer**: In `app/Http/Kernel.php` in `$routeMiddleware` array

**Q123: What is the `handle()` method in middleware?**  
**Answer**: The main method that processes the request:
```php
public function handle(Request $request, Closure $next) {
    // Logic here
    return $next($request);
}
```

**Q124: What are Laravel's default middleware groups?**  
**Answer**:
- **web**: Session, CSRF, cookies
- **api**: Rate limiting, bindings

---

### Section 3.4: RESTful Resource Controllers

**Q125: What are RESTful resource controllers?**  
**Answer**: Resource controllers handle all CRUD operations with conventional method names.

**Q126: What 7 methods does a resource controller have?**  
**Answer**:
1. `index()` - List all
2. `create()` - Show create form
3. `store()` - Save new
4. `show()` - Display one
5. `edit()` - Show edit form
6. `update()` - Update
7. `destroy()` - Delete

**Q127: How do you create a resource controller?**  
**Answer**: `php artisan make:controller PhotoController --resource`

**Q128: How do you define a resource route?**  
**Answer**: `Route::resource('photos', PhotoController::class);`

**Q129: What routes does `Route::resource()` create?**  
**Answer**: 7 routes for index, create, store, show, edit, update, destroy

**Q130: How do you create an API resource controller?**  
**Answer**:
```bash
php artisan make:controller API/PhotoController --api
Route::apiResource('photos', PhotoController::class);
```

**Q131: How many routes does apiResource create?**  
**Answer**: 5 routes (excludes create and edit)

**Q132: How do you exclude specific resource routes?**  
**Answer**:
```php
Route::resource('photos', PhotoController::class)->except(['create', 'edit']);
```

**Q133: How do you include only specific resource routes?**  
**Answer**:
```php
Route::resource('photos', PhotoController::class)->only(['index', 'show']);
```

---

### Section 3.5: Blade Templating

**Q134: What is Blade?**  
**Answer**: Blade is Laravel's simple yet powerful templating engine.

**Q135: How do you display a variable in Blade?**  
**Answer**: `{{ $variable }}` (escaped, safe from XSS)

**Q136: How do you display unescaped HTML?**  
**Answer**: `{!! $htmlContent !!}` (use carefully!)

**Q137: What's the difference between `{{ }}` and `{!! !!}`?**  
**Answer**:
- **{{ }}**: Escapes HTML (prevents XSS attacks)
- **{!! !!}**: Does NOT escape (dangerous with user input)

**Q138: How do you write an if statement in Blade?**  
**Answer**:
```blade
@if($condition)
    Content
@endif
```

**Q139: How do you write a foreach loop in Blade?**  
**Answer**:
```blade
@foreach($items as $item)
    {{ $item }}
@endforeach
```

**Q140: What is @forelse?**  
**Answer**: Foreach with empty check:
```blade
@forelse($items as $item)
    {{ $item }}
@empty
    No items
@endforelse
```

**Q141: What is the $loop variable?**  
**Answer**: Provides loop information:
- `$loop->index` (0-based)
- `$loop->iteration` (1-based)
- `$loop->first`
- `$loop->last`
- `$loop->count`

**Q142: What is template inheritance?**  
**Answer**: Defining a base layout and extending it in child views.

**Q143: What directive extends a layout?**  
**Answer**: `@extends('layouts.app')`

**Q144: What's the difference between @section and @yield?**  
**Answer**:
- **@section**: Defines content in child view
- **@yield**: Defines placeholder in layout

**Q145: How do you include a sub-view?**  
**Answer**: `@include('partials.header')`

**Q146: What is @csrf?**  
**Answer**: Generates CSRF token field for form protection.

**Q147: What is @method used for?**  
**Answer**: Method spoofing for PUT, PATCH, DELETE in forms: `@method('PUT')`

**Q148: What are Blade components?**  
**Answer**: Reusable view elements with their own logic: `<x-alert type="success">`

**Q149: What's the difference between @push/@stack and @section/@yield?**  
**Answer**:
- **@section/@yield**: Replaceable content areas
- **@push/@stack**: Append content (multiple pushes accumulate)

**Q150: How do you write comments in Blade?**  
**Answer**: `{{-- This is a comment --}}`

---

### Section 3.6: Advanced Routing

**Q151: What are named routes?**  
**Answer**: Routes with assigned names for easier reference:
```php
Route::get('/profile')->name('profile');
```

**Q152: How do you generate URL from named route?**  
**Answer**: `route('profile')`

**Q153: What are route groups?**  
**Answer**: Grouping multiple routes with shared attributes (middleware, prefix, namespace).

**Q154: How do you create a route group with middleware?**  
**Answer**:
```php
Route::middleware(['auth'])->group(function () {
    Route::get('/dashboard', function () {});
});
```

**Q155: How do you add prefix to route group?**  
**Answer**:
```php
Route::prefix('admin')->group(function () {
    Route::get('/dashboard', function () {});
});
```

**Q156: What is route model binding?**  
**Answer**: Automatically resolves Eloquent models from route parameters:
```php
Route::get('/users/{user}', function (User $user) {
    // $user automatically fetched
});
```

**Q157: What's the difference between implicit and explicit binding?**  
**Answer**:
- **Implicit**: Laravel auto-resolves by ID
- **Explicit**: You define custom resolution logic in RouteServiceProvider

**Q158: What is domain routing?**  
**Answer**: Routing based on domain/subdomain:
```php
Route::domain('{account}.myapp.com')->group(function () {});
```

---

### Section 3.7: URL Generation

**Q159: How do you get the current URL?**  
**Answer**: `url()->current()`

**Q160: How do you get the full URL with query string?**  
**Answer**: `url()->full()`

**Q161: How do you get the previous URL?**  
**Answer**: `url()->previous()`

**Q162: What's the difference between `url()`, `route()`, and `action()`?**  
**Answer**:
- **url()**: Generates URL from path
- **route()**: Generates URL from named route
- **action()**: Generates URL from controller action

**Q163: How do you generate asset URL?**  
**Answer**: `asset('css/style.css')`

---

### 🔴 CONFUSING QUESTIONS - Unit III

**Q164: What's the difference between Controller and Closure in routes?**  
**Answer**:
- **Closure**: Simple function in route file (good for simple routes)
- **Controller**: Organized class with methods (better for complex logic)

**Q165: What's the difference between `--resource` and `--api` flags?**  
**Answer**:
- **--resource**: Creates 7 methods (includes create/edit forms)
- **--api**: Creates 5 methods (excludes create/edit, for API only)

**Q166: What's the difference between `@include` and `@extends`?**  
**Answer**:
- **@include**: Includes a sub-view (like partial)
- **@extends**: Extends a layout (template inheritance)

**Q167: What's the difference between `compact('var')` and `['var' => $var]`?**  
**Answer**: Both achieve same result. `compact()` is cleaner when passing multiple variables.

**Q168: What's the difference between middleware in route vs controller constructor?**  
**Answer**:
- **Route middleware**: Applied to specific route
- **Controller constructor**: Applied to multiple/all methods in controller

**Q169: What's the difference between `only()` and `except()` in resource routes?**  
**Answer**:
- **only()**: Include ONLY these routes
- **except()**: Exclude these routes, include rest

**Q170: What's the difference between `{{ }}` and `@{{ }}` in Blade?**  
**Answer**:
- **{{ }}**: Renders PHP variable
- **@{{ }}**: Escapes and shows literal `{{ }}` (useful for JavaScript frameworks like Vue)

**Q171: Can you use PHP code in Blade?**  
**Answer**: Yes! Blade doesn't restrict plain PHP. You can use `@php` blocks or even regular `<?php ?>` tags.

**Q172: What's the difference between `route()` and `redirect()->route()`?**  
**Answer**:
- **route()**: Generates URL string
- **redirect()->route()**: Performs actual redirect to that route

**Q173: What's the difference between resource controller and RESTful controller?**  
**Answer**: They are the SAME thing in Laravel. "Resource controller" is Laravel's term for RESTful CRUD controller.

---

## 🎯 MOST ASKED VIVA QUESTIONS (Top 30)

1. **What is MVC?** → Model-View-Controller pattern
2. **Where are routes defined?** → routes/web.php
3. **Where are controllers stored?** → app/Http/Controllers/
4. **Where are views stored?** → resources/views/
5. **What is Composer?** → PHP dependency manager
6. **What is Artisan?** → Laravel CLI
7. **How do you create a controller?** → php artisan make:controller
8. **What is middleware?** → HTTP request filter
9. **Where do you register middleware?** → app/Http/Kernel.php
10. **How do you pass data to view?** → compact(), with(), array
11. **What is Blade?** → Laravel templating engine
12. **How do you display variable in Blade?** → {{ $variable }}
13. **What is @csrf?** → CSRF token protection
14. **What is compact()?** → Creates array from variables
15. **What are named routes?** → Routes with names for easy reference
16. **How do you redirect?** → redirect()->route()
17. **What is resource controller?** → CRUD controller with 7 methods
18. **What's the difference between {{ }} and {!! !!}?** → Escaped vs unescaped
19. **What is route model binding?** → Auto-resolves models from parameters
20. **What is .env file?** → Environment configuration
21. **How do you start server?** → php artisan serve
22. **What is entry point?** → public/index.php
23. **How do you run migrations?** → php artisan migrate
24. **What is flash data?** → Session data for next request only
25. **What are route parameters?** → URL segments captured as variables
26. **What is template inheritance?** → Extending layouts in child views
27. **What's the difference between @section and @yield?** → Content vs placeholder
28. **What is Eloquent ORM?** → Laravel's database ORM
29. **How do you create a cookie?** → response()->cookie()
30. **What is fallback route?** → Catch-all for unmatched URLs

---

## 💡 VIVA TIPS

### ✅ Do's:
- Speak confidently and clearly
- Give examples when possible
- Mention file locations
- Explain with practical scenarios
- If unsure, say "I think..." instead of guessing

### ❌ Don'ts:
- Don't panic if you don't know
- Don't give wrong information
- Don't be too brief
- Don't argue with examiner
- Don't memorize without understanding

### 🎯 Common Viva Scenarios:

**Scenario 1: "Show me how to create a route"**
```php
Route::get('/hello', function() {
    return 'Hello World';
});
```

**Scenario 2: "How to pass array to view?"**
```php
$users = ['John', 'Jane', 'Bob'];
return view('users', compact('users'));
```

**Scenario 3: "What if route constraint fails?"**
Returns 404 Not Found error

**Scenario 4: "Where is the entry point?"**
`public/index.php`

---

## 📝 Quick Reference: File Locations

| Component | Location |
|-----------|----------|
| Routes | routes/web.php |
| Controllers | app/Http/Controllers/ |
| Models | app/Models/ |
| Views | resources/views/ |
| Middleware | app/Http/Middleware/ |
| Migrations | database/migrations/ |
| Config | config/ |
| Environment | .env |
| Logs | storage/logs/ |
| Public files | public/ |
| Entry point | public/index.php |
| Kernel | app/Http/Kernel.php |

---

**Perfect for viva preparation! Practice these daily! 🎓**

**Back to:** [Main Study Guide](../STUDY_GUIDE.md) | [Exam Preparation](../Exam_Preparation/README.md)
