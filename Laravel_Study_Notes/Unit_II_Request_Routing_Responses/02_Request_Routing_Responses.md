# Unit II: Request, Routing & Responses

## 📚 Table of Contents
- [Laravel Request Lifecycle](#laravel-request-lifecycle)
- [Basic Routing](#basic-routing)
- [Routing Parameters](#routing-parameters)
- [Understanding Views](#understanding-views-in-laravel)
- [Passing Data to Views](#passing-data-to-views)
- [Laravel Responses](#laravel-responses)
- [Redirections](#laravel-redirections)
- [Exam Questions & Answers](#exam-questions--answers)

---

## Laravel Request Lifecycle

### Overview
Understanding the request lifecycle is crucial for mastering Laravel. Every HTTP request follows a specific path through the framework.

### Request Lifecycle Steps

```
1. Public/index.php (Entry Point)
        ↓
2. HTTP Kernel
        ↓
3. Service Providers (Bootstrap)
        ↓
4. Router (Route Matching)
        ↓
5. Middleware (Filtering)
        ↓
6. Controller (Business Logic)
        ↓
7. Model (Database Interaction)
        ↓
8. View (Response Generation)
        ↓
9. Response Sent to Browser
```

### Detailed Explanation

#### Step 1: Entry Point (`public/index.php`)
All requests enter through `public/index.php`:
```php
// Load Composer autoloader
require __DIR__.'/../vendor/autoload.php';

// Bootstrap Laravel application
$app = require_once __DIR__.'/../bootstrap/app.php';

// Handle request and send response
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
$response = $kernel->handle($request = Illuminate\Http\Request::capture());
$response->send();
$kernel->terminate($request, $response);
```

#### Step 2: HTTP Kernel
The kernel handles:
- Loading middleware
- Bootstrapping the application
- Processing the request

**Global Middleware** (runs on every request):
- CheckForMaintenanceMode
- ValidatePostSize
- TrimStrings
- ConvertEmptyStringsToNull

#### Step 3: Service Providers
Service providers bootstrap all framework services:
- Database service
- Queue service
- Mail service
- Custom services

Located in: `config/app.php` → `providers` array

#### Step 4: Router
Router matches the incoming URL to defined routes in:
- `routes/web.php` (web routes)
- `routes/api.php` (API routes)

#### Step 5: Middleware
Middleware filters HTTP requests:
- Authentication check
- CSRF verification
- Input validation
- CORS handling

#### Step 6-8: Controller → Model → View
- **Controller**: Processes request
- **Model**: Interacts with database
- **View**: Generates HTML response

#### Step 9: Response
Final HTML/JSON response sent back to browser

---

## Basic Routing

### What are Routes?
Routes define URLs and map them to specific actions (closures or controllers).

### Route Files
- **routes/web.php**: Web interface routes (with session, CSRF protection)
- **routes/api.php**: API routes (stateless, prefixed with `/api`)
- **routes/console.php**: Artisan command routes
- **routes/channels.php**: Broadcasting channel routes

### Basic Route Definitions

#### GET Route
```php
use Illuminate\Support\Facades\Route;

// Simple GET route
Route::get('/hello', function () {
    return 'Hello, World!';
});
```

#### POST Route
```php
Route::post('/submit', function () {
    return 'Form submitted!';
});
```

#### Multiple HTTP Verbs
```php
// Match specific verbs
Route::match(['get', 'post'], '/form', function () {
    return 'Handles GET and POST';
});

// Match all verbs
Route::any('/any', function () {
    return 'Handles all HTTP verbs';
});
```

### Available HTTP Methods
```php
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```

### Route Examples

#### Example 1: Simple Welcome Route
```php
// routes/web.php
Route::get('/', function () {
    return view('welcome');
});
```

#### Example 2: About Page
```php
Route::get('/about', function () {
    return '<h1>About Us</h1><p>Welcome to our company!</p>';
});
```

#### Example 3: Contact Form
```php
// Display form
Route::get('/contact', function () {
    return view('contact');
});

// Handle form submission
Route::post('/contact', function () {
    // Process form data
    return 'Thank you for contacting us!';
});
```

### Viewing All Routes
```bash
php artisan route:list
```

Output:
```
+--------+----------+----------+------+---------+--------------+
| Domain | Method   | URI      | Name | Action  | Middleware   |
+--------+----------+----------+------+---------+--------------+
|        | GET|HEAD | /        |      | Closure | web          |
|        | GET|HEAD | about    |      | Closure | web          |
|        | GET|POST | contact  |      | Closure | web          |
+--------+----------+----------+------+---------+--------------+
```

---

## Routing Parameters

### Required Parameters
Capture segments from the URL:

```php
// Capture user ID
Route::get('/user/{id}', function ($id) {
    return 'User ID: ' . $id;
});

// Multiple parameters
Route::get('/posts/{postId}/comments/{commentId}', function ($postId, $commentId) {
    return "Post: {$postId}, Comment: {$commentId}";
});
```

**URL Examples:**
- `/user/123` → `$id = 123`
- `/posts/5/comments/10` → `$postId = 5`, `$commentId = 10`

### Optional Parameters
Make parameters optional by adding `?`:

```php
Route::get('/user/{name?}', function ($name = 'Guest') {
    return 'Hello, ' . $name;
});
```

**URL Examples:**
- `/user/John` → `$name = 'John'`
- `/user` → `$name = 'Guest'` (default value)

### Regular Expression Constraints
Validate parameter format using regex:

```php
// Only numeric IDs
Route::get('/user/{id}', function ($id) {
    return 'User: ' . $id;
})->where('id', '[0-9]+');

// Only alphabetic names
Route::get('/user/{name}', function ($name) {
    return 'User: ' . $name;
})->where('name', '[A-Za-z]+');

// Multiple constraints
Route::get('/user/{id}/{name}', function ($id, $name) {
    return "ID: {$id}, Name: {$name}";
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```

### Global Constraints
Define constraints in `App\Providers\RouteServiceProvider`:

```php
public function boot()
{
    Route::pattern('id', '[0-9]+');
    Route::pattern('slug', '[a-z0-9-]+');
    
    parent::boot();
}
```

Now all `{id}` parameters must be numeric across all routes.

### Encoded Forward Slashes
By default, Laravel doesn't allow `/` in parameters. To enable:

```php
Route::get('/search/{query}', function ($query) {
    return $query;
})->where('query', '.*');
```

---

## Understanding Views in Laravel

### What are Views?
Views contain the HTML served by your application. They separate presentation logic from business logic.

**Location:** `resources/views/`

### Creating Views

#### Method 1: Using Helper Function
```php
// Return a view
return view('welcome');

// File location: resources/views/welcome.blade.php
```

#### Method 2: Using View Facade
```php
use Illuminate\Support\Facades\View;

return View::make('welcome');
```

### View File Extensions
- `.blade.php` - Blade templates (recommended)
- `.php` - Plain PHP templates

### Nested Views (Subdirectories)
Use dot notation for views in subdirectories:

```
resources/views/
├── welcome.blade.php
├── pages/
│   ├── about.blade.php
│   └── contact.blade.php
└── users/
    ├── index.blade.php
    └── profile.blade.php
```

```php
// Access nested views
return view('pages.about');      // pages/about.blade.php
return view('users.profile');    // users/profile.blade.php
```

### Checking if View Exists
```php
use Illuminate\Support\Facades\View;

if (View::exists('emails.customer')) {
    // View exists
}
```

### First Available View
```php
return view()->first(['custom.admin', 'admin'], $data);
```

---

## Passing Data to Views

### Method 1: Using `with()` Method
```php
return view('greeting')->with('name', 'John');
```

In view (`greeting.blade.php`):
```blade
<h1>Hello, {{ $name }}</h1>
```

### Method 2: Passing Array
```php
return view('greeting', ['name' => 'John', 'age' => 25]);
```

In view:
```blade
<h1>Hello, {{ $name }}</h1>
<p>Age: {{ $age }}</p>
```

### Method 3: Using `compact()` Function
```php
public function show()
{
    $name = 'John';
    $age = 25;
    $city = 'New York';
    
    return view('profile', compact('name', 'age', 'city'));
}
```

### Method 4: Magic Methods
```php
return view('greeting')->withName('John')->withAge(25);
```

### Passing Complex Data

#### Arrays
```php
$users = [
    ['name' => 'John', 'email' => 'john@example.com'],
    ['name' => 'Jane', 'email' => 'jane@example.com'],
];

return view('users.index', compact('users'));
```

In view:
```blade
<ul>
    @foreach($users as $user)
        <li>{{ $user['name'] }} - {{ $user['email'] }}</li>
    @endforeach
</ul>
```

#### Eloquent Models
```php
$user = User::find(1);
return view('users.profile', compact('user'));
```

In view:
```blade
<h1>{{ $user->name }}</h1>
<p>{{ $user->email }}</p>
```

#### Collections
```php
$posts = Post::all();
return view('posts.index', compact('posts'));
```

### Complete Example

**Controller:**
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::latest()->paginate(10);
        $title = 'Latest Posts';
        $description = 'Browse our latest blog posts';
        
        return view('posts.index', compact('posts', 'title', 'description'));
    }
}
```

**View (`resources/views/posts/index.blade.php`):**
```blade
@extends('layouts.app')

@section('content')
    <h1>{{ $title }}</h1>
    <p>{{ $description }}</p>
    
    @foreach($posts as $post)
        <article>
            <h2>{{ $post->title }}</h2>
            <p>{{ Str::limit($post->body, 100) }}</p>
            <small>Posted on {{ $post->created_at->format('M d, Y') }}</small>
        </article>
    @endforeach
    
    {{ $posts->links() }}
@endsection
```

---

## Sharing Data with All Views

### Method 1: View Composers
Share data with specific views:

**Create Service Provider:**
```bash
php artisan make:provider ViewServiceProvider
```

**Register in `config/app.php`:**
```php
'providers' => [
    // ...
    App\Providers\ViewServiceProvider::class,
],
```

**In `app/Providers/ViewServiceProvider.php`:**
```php
<?php

namespace App\Providers;

use Illuminate\Support\Facades\View;
use Illuminate\Support\ServiceProvider;
use App\Models\Category;

class ViewServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Share with specific view
        View::composer('posts.index', function ($view) {
            $view->with('categories', Category::all());
        });
        
        // Share with multiple views
        View::composer(['posts.index', 'posts.show'], function ($view) {
            $view->with('recentPosts', Post::latest()->take(5)->get());
        });
        
        // Share with all views using wildcard
        View::composer('*', function ($view) {
            $view->with('siteName', config('app.name'));
        });
    }
}
```

### Method 2: View Creators
Execute immediately when view is instantiated:

```php
View::creator('profile', function ($view) {
    $view->with('user', auth()->user());
});
```

### Method 3: Using `share()` Method
In `AppServiceProvider`:

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\View;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Share with all views
        View::share('siteName', 'My Website');
        View::share('currentYear', date('Y'));
    }
}
```

---

## Laravel Responses

### Basic Responses

#### String Response
```php
Route::get('/', function () {
    return 'Hello World';
});
```

#### JSON Response
```php
Route::get('/api/users', function () {
    return response()->json([
        'name' => 'John',
        'age' => 25
    ]);
});
```

#### File Download
```php
Route::get('/download', function () {
    return response()->download(storage_path('app/file.pdf'));
});
```

### Attaching Headers

#### Add Single Header
```php
return response($content)
    ->header('Content-Type', 'text/plain');
```

#### Add Multiple Headers
```php
return response($content)
    ->header('Content-Type', 'application/json')
    ->header('X-Custom-Header', 'Custom Value')
    ->header('Cache-Control', 'no-cache');
```

#### Using `withHeaders()` Method
```php
return response($content)->withHeaders([
    'Content-Type' => 'application/json',
    'X-API-Key' => 'secret-key',
    'Cache-Control' => 'max-age=3600',
]);
```

### Attaching Cookies

#### Create Cookie
```php
return response($content)
    ->cookie('name', 'value', $minutes);
```

#### Cookie Parameters
```php
return response($content)->cookie(
    'name',      // Cookie name
    'value',     // Cookie value
    60,          // Minutes
    '/',         // Path
    '.example.com', // Domain
    true,        // Secure (HTTPS only)
    true,        // HttpOnly (not accessible via JavaScript)
    false,       // Raw
    'lax'        // SameSite
);
```

#### Cookie Examples
```php
// Simple cookie (expires in 60 minutes)
return response('Hello')->cookie('theme', 'dark', 60);

// Permanent cookie (5 years)
return response('Welcome')->cookie(
    'remember_me', 
    $token, 
    5 * 365 * 24 * 60
);

// Queue cookie (doesn't send immediately)
return response('Page')->cookie(
    cookie()->forever('language', 'en')
);
```

#### Retrieve Cookie
```php
$value = $request->cookie('name');
```

#### Delete Cookie
```php
return response('Logged out')->cookie(
    cookie()->forget('session_token')
);
```

### JSON Response

#### Basic JSON
```php
return response()->json([
    'status' => 'success',
    'data' => [
        'id' => 1,
        'name' => 'John Doe'
    ]
]);
```

#### JSON with Status Code
```php
return response()->json([
    'error' => 'Not Found'
], 404);
```

#### JSON with Headers
```php
return response()->json([
    'data' => $users
], 200, [
    'X-Total-Count' => $users->count(),
    'Cache-Control' => 'max-age=3600'
]);
```

#### JSONP Response
```php
return response()->json(['name' => 'John'])
    ->withCallback($request->input('callback'));
```

### Response Types Summary

| Method | Use Case |
|--------|----------|
| `return 'string'` | Simple text response |
| `return view()` | HTML view |
| `response()->json()` | API responses |
| `response()->download()` | File downloads |
| `response()->file()` | Display files |
| `redirect()` | Redirections |

---

## Laravel Redirections

### Basic Redirects

#### Redirect to URL
```php
return redirect('/home');
```

#### Redirect with Status Code
```php
return redirect('/home', 301); // Permanent redirect
```

### Redirect to Named Routes

#### Define Named Route
```php
Route::get('/profile', function () {
    // ...
})->name('profile');
```

#### Redirect to Named Route
```php
return redirect()->route('profile');
```

#### With Parameters
```php
Route::get('/user/{id}/profile', function ($id) {
    // ...
})->name('user.profile');

// Redirect with parameter
return redirect()->route('user.profile', ['id' => 1]);
```

### Redirect to Controller Actions

#### Redirect to Controller
```php
return redirect()->action([UserController::class, 'index']);
```

#### With Parameters
```php
return redirect()->action(
    [UserController::class, 'show'], 
    ['id' => 1]
);
```

### Redirect Back
```php
return redirect()->back();

// Or using helper
return back();
```

### Redirect with Flash Data

#### Flash Message
```php
return redirect('/dashboard')->with('status', 'Profile updated!');
```

Display in view:
```blade
@if(session('status'))
    <div class="alert alert-success">
        {{ session('status') }}
    </div>
@endif
```

#### Flash Multiple Items
```php
return redirect('/dashboard')->with([
    'success' => 'Operation completed!',
    'message' => 'Your changes have been saved.'
]);
```

### Redirect Helpers

#### `to_route()` Helper
```php
return to_route('profile');
```

#### `back()` Helper
```php
return back()->withInput();
```

#### `redirect()` Helper
```php
return redirect('/home');
```

### Complete Redirect Example

**Controller:**
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function store(Request $request)
    {
        // Validate request
        $validated = $request->validate([
            'title' => 'required|max:255',
            'body' => 'required',
        ]);
        
        // Create post
        $post = Post::create($validated);
        
        // Redirect with success message
        return redirect()
            ->route('posts.show', $post->id)
            ->with('success', 'Post created successfully!');
    }
    
    public function update(Request $request, Post $post)
    {
        // Update logic
        
        return redirect()
            ->action([PostController::class, 'index'])
            ->with('success', 'Post updated!');
    }
    
    public function destroy(Post $post)
    {
        $post->delete();
        
        return back()->with('success', 'Post deleted!');
    }
}
```

---

## Exam Questions & Answers

### Short Answer Questions (2-5 marks)

#### Q1: Explain Laravel request lifecycle briefly.
**Answer:**
1. Request enters through `public/index.php`
2. HTTP Kernel loads middleware
3. Service providers bootstrap services
4. Router matches URL to route
5. Middleware filters request
6. Controller processes logic
7. Model interacts with database
8. View generates response
9. Response sent to browser

#### Q2: What are routing parameters? Give example.
**Answer:**
Routing parameters capture URL segments:
```php
Route::get('/user/{id}', function ($id) {
    return 'User: ' . $id;
});
```
URL `/user/123` passes `123` to `$id`.

#### Q3: Differentiate between required and optional parameters.
**Answer:**
- **Required**: `/{id}` - Must be present in URL
- **Optional**: `/{name?}` - Can be omitted, has default value

#### Q4: How do you pass data to views? List methods.
**Answer:**
1. `with()` method: `view('page')->with('key', 'value')`
2. Array: `view('page', ['key' => 'value'])`
3. `compact()`: `view('page', compact('key'))`
4. Magic methods: `view('page')->withKey('value')`

#### Q5: What is the purpose of sharing data with all views?
**Answer:**
Sharing data makes variables available across all views without passing them individually. Useful for:
- Site configuration
- User information
- Navigation menus
- Footer data

#### Q6: How do you attach headers to responses?
**Answer:**
```php
return response($content)
    ->header('Content-Type', 'application/json')
    ->header('X-Custom', 'Value');
```

#### Q7: Explain cookie attachment in Laravel responses.
**Answer:**
```php
return response('Hello')
    ->cookie('name', 'value', 60); // 60 minutes
```
Parameters: name, value, minutes, path, domain, secure, httpOnly

#### Q8: What are named routes? Why use them?
**Answer:**
Named routes assign names to routes for easier reference:
```php
Route::get('/profile')->name('profile');
redirect()->route('profile');
```
Benefits:
- Easier to remember
- Changes don't break redirects
- Cleaner code

### Long Answer Questions (10-15 marks)

#### Q9: Explain different ways to pass data to views with examples.
**Answer:**
[See "Passing Data to Views" section above - expand with more examples]

#### Q10: Describe Laravel response types and their usage.
**Answer:**

**1. String Response:**
```php
return 'Simple text';
```

**2. View Response:**
```php
return view('welcome', ['name' => 'John']);
```

**3. JSON Response:**
```php
return response()->json(['data' => $array], 200);
```

**4. File Download:**
```php
return response()->download($pathToFile);
```

**5. Redirect:**
```php
return redirect('/home')->with('status', 'Success');
```

**6. With Headers:**
```php
return response($content)->header('Content-Type', 'text/html');
```

**7. With Cookies:**
```php
return response($content)->cookie('name', 'value', 60);
```

#### Q11: Explain routing parameters with constraints and examples.
**Answer:**
[See "Routing Parameters" section - expand with all constraint types]

### Practical Questions

#### Q12: Create routes for a blog with:
a) List all posts  
b) Show single post  
c) Create new post  
d) Update post  
e) Delete post

**Answer:**
```php
// routes/web.php

// List all posts
Route::get('/posts', [PostController::class, 'index'])->name('posts.index');

// Show single post
Route::get('/posts/{id}', [PostController::class, 'show'])->name('posts.show');

// Create post form
Route::get('/posts/create', [PostController::class, 'create'])->name('posts.create');

// Store new post
Route::post('/posts', [PostController::class, 'store'])->name('posts.store');

// Edit post form
Route::get('/posts/{id}/edit', [PostController::class, 'edit'])->name('posts.edit');

// Update post
Route::put('/posts/{id}', [PostController::class, 'update'])->name('posts.update');

// Delete post
Route::delete('/posts/{id}', [PostController::class, 'destroy'])->name('posts.destroy');
```

#### Q13: Write code to redirect user after login with flash message.
**Answer:**
```php
public function login(Request $request)
{
    // Authentication logic
    
    if (auth()->attempt($credentials)) {
        return redirect()
            ->route('dashboard')
            ->with('success', 'Welcome back, ' . auth()->user()->name);
    }
    
    return back()->withErrors(['email' => 'Invalid credentials']);
}
```

Display in dashboard view:
```blade
@if(session('success'))
    <div class="alert alert-success">
        {{ session('success') }}
    </div>
@endif
```

---

## Quick Reference

### Common Route Patterns
```php
// Basic route
Route::get('/url', function() {});

// With parameter
Route::get('/user/{id}', function($id) {});

// Optional parameter
Route::get('/user/{name?}', function($name = 'Guest') {});

// With constraint
Route::get('/user/{id}', function($id) {})->where('id', '[0-9]+');

// Named route
Route::get('/profile', function() {})->name('profile');

// Redirect
return redirect()->route('profile');
```

### Response Cheat Sheet
```php
// JSON
response()->json(['key' => 'value']);

// With status
response()->json(['error' => 'Not found'], 404);

// With headers
response()->json($data)->header('X-Custom', 'Value');

// Cookie
response($content)->cookie('name', 'value', 60);

// Download
response()->download($path);
```

---

## Summary Checklist

After studying Unit II, you should be able to:
- ✅ Explain Laravel request lifecycle
- ✅ Define basic routes
- ✅ Use routing parameters (required, optional, constrained)
- ✅ Create and render views
- ✅ Pass data to views using multiple methods
- ✅ Share data with all views
- ✅ Create different response types
- ✅ Attach headers and cookies to responses
- ✅ Perform various redirections
- ✅ Use flash messages with redirects

---

**Previous:** [Unit I: Getting Started](../Unit_I_Getting_Started/01_Getting_Started_MVC_Laravel.md)  
**Next:** [Unit III: Controllers, Blade & Advanced Routing](../Unit_III_Controllers_Blade_Advanced_Routing/03_Controllers_Blade_Advanced_Routing.md)
