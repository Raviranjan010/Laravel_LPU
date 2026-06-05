# Code Examples Repository

## 📝 Practical Laravel Code Examples

Complete working examples for all topics covered in Units I-III.

---

## Table of Contents
- [Unit I Examples](#unit-i-examples)
- [Unit II Examples](#unit-ii-examples)
- [Unit III Examples](#unit-iii-examples)
- [Unit IV Examples](#unit-iv-examples)
- [Unit V Examples](#unit-v-examples)
- [Unit VI Examples](#unit-vi-examples)
- [Complete Applications](#complete-applications)

---

## Unit I Examples

### Example 1: Basic Laravel Setup

**Step-by-step setup commands:**
```bash
# Create project
composer create-project laravel/laravel blog-demo
cd blog-demo

# Environment setup
copy .env.example .env
php artisan key:generate

# Configure database in .env
# DB_CONNECTION=mysql
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=blog_demo
# DB_USERNAME=root
# DB_PASSWORD=

# Run migrations
php artisan migrate

# Start server
php artisan serve
```

Visit: `http://localhost:8000`

---

## Unit II Examples

### Example 2: Basic Routing

**File:** `routes/web.php`

```php
<?php

use Illuminate\Support\Facades\Route;

// Simple GET route
Route::get('/', function () {
    return view('welcome');
});

// Route with parameter
Route::get('/hello/{name}', function ($name) {
    return "Hello, {$name}!";
});

// Optional parameter
Route::get('/greet/{name?}', function ($name = 'Guest') {
    return "Welcome, {$name}!";
});

// Multiple parameters
Route::get('/user/{id}/post/{postId}', function ($id, $postId) {
    return "User ID: {$id}, Post ID: {$postId}";
});

// Constrained parameter (numeric only)
Route::get('/product/{id}', function ($id) {
    return "Product ID: {$id}";
})->where('id', '[0-9]+');

// POST route
Route::post('/submit', function () {
    return 'Form submitted successfully!';
});

// Multiple HTTP verbs
Route::match(['get', 'post'], '/form', function () {
    return 'Handles both GET and POST';
});

// Any HTTP verb
Route::any('/any', function () {
    return 'Handles all HTTP methods';
});
```

### Example 3: Views and Data Passing

**Controller:** `app/Http\Controllers/PageController.php`
```php
<?php

namespace App\Http\Controllers;

class PageController extends Controller
{
    public function home()
    {
        $title = 'Home Page';
        $description = 'Welcome to our website';
        $features = ['Fast', 'Secure', 'Reliable'];
        
        return view('pages.home', compact('title', 'description', 'features'));
    }
    
    public function about()
    {
        $team = [
            ['name' => 'John Doe', 'role' => 'CEO'],
            ['name' => 'Jane Smith', 'role' => 'CTO'],
            ['name' => 'Bob Johnson', 'role' => 'Developer'],
        ];
        
        return view('pages.about', compact('team'));
    }
}
```

**View:** `resources/views/pages/home.blade.php`
```blade
@extends('layouts.app')

@section('title', $title)

@section('content')
    <h1>{{ $title }}</h1>
    <p>{{ $description }}</p>
    
    <h2>Features:</h2>
    <ul>
        @foreach($features as $feature)
            <li>{{ $feature }}</li>
        @endforeach
    </ul>
@endsection
```

**View:** `resources/views/pages/about.blade.php`
```blade
@extends('layouts.app')

@section('title', 'About Us')

@section('content')
    <h1>About Us</h1>
    
    <h2>Our Team:</h2>
    <div class="team">
        @forelse($team as $member)
            <div class="team-member">
                <h3>{{ $member['name'] }}</h3>
                <p>{{ $member['role'] }}</p>
            </div>
        @empty
            <p>No team members yet.</p>
        @endforelse
    </div>
@endsection
```

**Routes:** `routes/web.php`
```php
use App\Http\Controllers\PageController;

Route::get('/', [PageController::class, 'home'])->name('home');
Route::get('/about', [PageController::class, 'about'])->name('about');
```

### Example 4: Responses with Headers and Cookies

**File:** `routes/web.php`

```php
use Illuminate\Support\Facades\Route;

// JSON Response
Route::get('/api/user', function () {
    return response()->json([
        'id' => 1,
        'name' => 'John Doe',
        'email' => 'john@example.com'
    ]);
});

// JSON with status code
Route::get('/api/error', function () {
    return response()->json([
        'error' => 'Not Found',
        'message' => 'The requested resource was not found'
    ], 404);
});

// Response with headers
Route::get('/download/info', function () {
    return response('File content')
        ->header('Content-Type', 'text/plain')
        ->header('Content-Disposition', 'attachment; filename="file.txt"')
        ->header('Cache-Control', 'no-cache');
});

// Response with cookies
Route::get('/set-preference', function () {
    return response('Preference saved')
        ->cookie('theme', 'dark', 525600) // 1 year
        ->cookie('language', 'en', 525600);
});

// File download
Route::get('/download/report', function () {
    return response()->download(storage_path('app/reports/report.pdf'));
});

// Redirect with flash message
Route::get('/logout', function () {
    // Logout logic here
    
    return redirect('/')
        ->with('success', 'You have been logged out successfully!');
});
```

---

## Unit III Examples

### Example 5: Basic Controller

**Create Controller:**
```bash
php artisan make:controller UserController
```

**File:** `app/Http/Controllers/UserController.php`
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
        $users = User::latest()->paginate(10);
        return view('users.index', compact('users'));
    }

    /**
     * Display the specified user.
     */
    public function show(User $user)
    {
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
            'password' => 'required|min:8|confirmed',
        ]);

        $user = User::create([
            'name' => $validated['name'],
            'email' => $validated['email'],
            'password' => bcrypt($validated['password']),
        ]);

        return redirect()->route('users.show', $user)
            ->with('success', 'User created successfully!');
    }
}
```

**Routes:** `routes/web.php`
```php
use App\Http\Controllers\UserController;

Route::get('/users', [UserController::class, 'index'])->name('users.index');
Route::get('/users/{user}', [UserController::class, 'show'])->name('users.show');
Route::post('/users', [UserController::class, 'store'])->name('users.store');
```

### Example 6: Resource Controller (Complete CRUD)

**Create Controller:**
```bash
php artisan make:controller PostController --resource
php artisan make:model Post -m
```

**Migration:** `database/migrations/xxxx_create_posts_table.php`
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('content');
            $table->string('slug')->unique();
            $table->boolean('published')->default(false);
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('posts');
    }
};
```

**Model:** `app/Models/Post.php`
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Post extends Model
{
    use HasFactory;

    protected $fillable = [
        'title',
        'content',
        'slug',
        'published',
    ];

    protected $casts = [
        'published' => 'boolean',
    ];
}
```

**Controller:** `app/Http/Controllers/PostController.php`
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;
use Illuminate\Support\Str;

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
            'title' => 'required|string|max:255',
            'content' => 'required|string',
            'published' => 'boolean',
        ]);

        $validated['slug'] = Str::slug($validated['title']);

        Post::create($validated);

        return redirect()->route('posts.index')
            ->with('success', 'Post created successfully!');
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
            'title' => 'required|string|max:255',
            'content' => 'required|string',
            'published' => 'boolean',
        ]);

        $validated['slug'] = Str::slug($validated['title']);

        $post->update($validated);

        return redirect()->route('posts.index')
            ->with('success', 'Post updated successfully!');
    }

    public function destroy(Post $post)
    {
        $post->delete();

        return redirect()->route('posts.index')
            ->with('success', 'Post deleted successfully!');
    }
}
```

**Routes:** `routes/web.php`
```php
use App\Http\Controllers\PostController;

Route::resource('posts', PostController::class);
```

**Views:**

`resources/views/posts/index.blade.php`
```blade
@extends('layouts.app')

@section('title', 'All Posts')

@section('content')
    <div class="container">
        <h1>All Posts</h1>
        
        @if(session('success'))
            <div class="alert alert-success">
                {{ session('success') }}
            </div>
        @endif
        
        <a href="{{ route('posts.create') }}" class="btn btn-primary">
            Create New Post
        </a>
        
        <table class="table">
            <thead>
                <tr>
                    <th>Title</th>
                    <th>Status</th>
                    <th>Created</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                @forelse($posts as $post)
                    <tr>
                        <td>{{ $post->title }}</td>
                        <td>
                            @if($post->published)
                                <span class="badge bg-success">Published</span>
                            @else
                                <span class="badge bg-secondary">Draft</span>
                            @endif
                        </td>
                        <td>{{ $post->created_at->format('M d, Y') }}</td>
                        <td>
                            <a href="{{ route('posts.show', $post) }}" class="btn btn-sm btn-info">
                                View
                            </a>
                            <a href="{{ route('posts.edit', $post) }}" class="btn btn-sm btn-warning">
                                Edit
                            </a>
                            <form action="{{ route('posts.destroy', $post) }}" 
                                  method="POST" 
                                  style="display:inline;">
                                @csrf
                                @method('DELETE')
                                <button type="submit" 
                                        class="btn btn-sm btn-danger"
                                        onclick="return confirm('Are you sure?')">
                                    Delete
                                </button>
                            </form>
                        </td>
                    </tr>
                @empty
                    <tr>
                        <td colspan="4">No posts found.</td>
                    </tr>
                @endforelse
            </tbody>
        </table>
        
        {{ $posts->links() }}
    </div>
@endsection
```

`resources/views/posts/create.blade.php`
```blade
@extends('layouts.app')

@section('title', 'Create Post')

@section('content')
    <div class="container">
        <h1>Create New Post</h1>
        
        <form action="{{ route('posts.store') }}" method="POST">
            @csrf
            
            <div class="form-group">
                <label for="title">Title</label>
                <input type="text" 
                       name="title" 
                       id="title" 
                       class="form-control"
                       value="{{ old('title') }}"
                       required>
                @error('title')
                    <div class="text-danger">{{ $message }}</div>
                @enderror
            </div>
            
            <div class="form-group">
                <label for="content">Content</label>
                <textarea name="content" 
                          id="content" 
                          class="form-control"
                          rows="10"
                          required>{{ old('content') }}</textarea>
                @error('content')
                    <div class="text-danger">{{ $message }}</div>
                @enderror
            </div>
            
            <div class="form-group">
                <label>
                    <input type="checkbox" name="published" value="1">
                    Publish immediately
                </label>
            </div>
            
            <button type="submit" class="btn btn-primary">Create Post</button>
            <a href="{{ route('posts.index') }}" class="btn btn-secondary">Cancel</a>
        </form>
    </div>
@endsection
```

### Example 7: Middleware

**Create Middleware:**
```bash
php artisan make:middleware CheckAge
php artisan make:middleware AdminMiddleware
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
        if ($request->input('age') < 18) {
            return redirect('/underage')
                ->with('error', 'You must be 18 or older.');
        }

        return $next($request);
    }
}
```

**File:** `app/Http/Middleware/AdminMiddleware.php`
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class AdminMiddleware
{
    public function handle(Request $request, Closure $next)
    {
        if (!auth()->check() || !auth()->user()->is_admin) {
            abort(403, 'Unauthorized access.');
        }

        return $next($request);
    }
}
```

**Register in:** `app/Http/Kernel.php`
```php
protected $routeMiddleware = [
    // ...
    'check.age' => \App\Http\Middleware\CheckAge::class,
    'admin' => \App\Http\Middleware\AdminMiddleware::class,
];
```

**Use in Routes:**
```php
Route::get('/enter', function() {
    return 'Welcome!';
})->middleware('check.age');

Route::get('/admin/dashboard', [AdminController::class, 'index'])
    ->middleware('admin');
```

**Use in Controller:**
```php
public function __construct()
{
    $this->middleware('auth');
    $this->middleware('admin')->only(['destroy', 'edit']);
}
```

### Example 8: Blade Template Inheritance

**Layout:** `resources/views/layouts/app.blade.php`
```blade
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@yield('title', 'My Application')</title>
    <link rel="stylesheet" href="{{ asset('css/app.css') }}">
</head>
<body>
    {{-- Header --}}
    @include('partials.header')
    
    {{-- Navigation --}}
    <nav class="navbar">
        <div class="container">
            <a href="{{ url('/') }}" class="logo">{{ config('app.name') }}</a>
            <ul class="nav-links">
                <li><a href="{{ route('home') }}">Home</a></li>
                <li><a href="{{ route('about') }}">About</a></li>
                <li><a href="{{ route('contact') }}">Contact</a></li>
                @auth
                    <li><a href="{{ route('dashboard') }}">Dashboard</a></li>
                @else
                    <li><a href="{{ route('login') }}">Login</a></li>
                @endauth
            </ul>
        </div>
    </nav>
    
    {{-- Flash Messages --}}
    <div class="container">
        @if(session('success'))
            <div class="alert alert-success">
                {{ session('success') }}
            </div>
        @endif
        
        @if(session('error'))
            <div class="alert alert-danger">
                {{ session('error') }}
            </div>
        @endif
    </div>
    
    {{-- Main Content --}}
    <main class="container">
        @hasSection('sidebar')
            <div class="row">
                <aside class="col-md-3">
                    @yield('sidebar')
                </aside>
                <div class="col-md-9">
                    @yield('content')
                </div>
            </div>
        @else
            @yield('content')
        @endif
    </main>
    
    {{-- Footer --}}
    @include('partials.footer')
    
    {{-- Scripts --}}
    @stack('scripts')
</body>
</html>
```

**Partial:** `resources/views/partials/header.blade.php`
```blade
<header class="site-header">
    <div class="container">
        <h1>Welcome to {{ config('app.name') }}</h1>
    </div>
</header>
```

**Partial:** `resources/views/partials/footer.blade.php`
```blade
<footer class="site-footer">
    <div class="container">
        <p>&copy; {{ date('Y') }} {{ config('app.name') }}. All rights reserved.</p>
    </div>
</footer>
```

**Child View:** `resources/views/home.blade.php`
```blade
@extends('layouts.app')

@section('title', 'Home Page')

@section('content')
    <div class="hero">
        <h2>Welcome to Our Website</h2>
        <p>This is the home page content.</p>
        <a href="{{ route('about') }}" class="btn btn-primary">Learn More</a>
    </div>
    
    <div class="features">
        <div class="feature">
            <h3>Feature 1</h3>
            <p>Description of feature 1.</p>
        </div>
        <div class="feature">
            <h3>Feature 2</h3>
            <p>Description of feature 2.</p>
        </div>
    </div>
@endsection

@push('scripts')
    <script src="{{ asset('js/home.js') }}"></script>
@endpush
```

### Example 9: Advanced Routing

**File:** `routes/web.php`

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\Admin\DashboardController;
use App\Http\Controllers\Admin\UserController;

// Named routes
Route::get('/profile', [ProfileController::class, 'show'])
    ->name('profile');

// Route groups with middleware
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/dashboard', [DashboardController::class, 'index'])
        ->name('dashboard');
    
    Route::get('/settings', [SettingsController::class, 'edit'])
        ->name('settings.edit');
    
    Route::put('/settings', [SettingsController::class, 'update'])
        ->name('settings.update');
});

// Admin routes with prefix and namespace
Route::prefix('admin')
    ->middleware(['auth', 'admin'])
    ->name('admin.')
    ->group(function () {
        Route::get('/dashboard', [DashboardController::class, 'index'])
            ->name('dashboard');
        
        Route::resource('users', UserController::class);
        
        Route::get('/reports', [ReportController::class, 'index'])
            ->name('reports');
    });

// API routes with prefix
Route::prefix('api/v1')->group(function () {
    Route::get('/users', [API\UserController::class, 'index']);
    Route::get('/users/{id}', [API\UserController::class, 'show']);
});

// Domain routing
Route::domain('{account}.myapp.com')->group(function () {
    Route::get('/dashboard', [AccountController::class, 'dashboard']);
});

// Subdomain routing
Route::domain('api.myapp.com')->group(function () {
    Route::get('/users', [API\UserController::class, 'index']);
});
```

---

## Unit IV Examples

### Example 10: File Upload, Sessions, & Localization

A practical application demonstrating file upload handling, session storage, and locale switching.

**Route (`routes/web.php`):**
```php
use App\Http\Controllers\ProfileController;

Route::middleware('web')->group(function () {
    Route::get('/profile', [ProfileController::class, 'show'])->name('profile');
    Route::post('/profile/upload', [ProfileController::class, 'upload'])->name('profile.upload');
    Route::get('/locale/{lang}', [ProfileController::class, 'switchLang'])->name('locale.switch');
    Route::post('/cart/add', [ProfileController::class, 'addToCart'])->name('cart.add');
    Route::get('/cart/clear', [ProfileController::class, 'clearCart'])->name('cart.clear');
});
```

**Controller (`app/Http/Controllers/ProfileController.php`):**
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\App;

class ProfileController extends Controller
{
    public function show()
    {
        return view('profile');
    }

    public function upload(Request $request)
    {
        $request->validate([
            'username' => 'required|string|max:50',
            'avatar' => 'required|image|mimes:jpeg,png,jpg,gif|max:2048',
        ]);

        if ($request->hasFile('avatar')) {
            $file = $request->file('avatar');
            $fileName = time() . '_' . $file->getClientOriginalName();
            $path = $file->storeAs('avatars', $fileName, 'public');

            // Store file path and username in Session
            session([
                'username' => $request->input('username'),
                'avatar_path' => '/storage/' . $path
            ]);

            return back()->with('success', 'Profile updated successfully!');
        }

        return back()->withErrors(['avatar' => 'Upload failed.']);
    }

    public function switchLang($lang)
    {
        if (in_array($lang, ['en', 'hi'])) {
            session(['locale' => $lang]);
        }
        return back();
    }

    public function addToCart(Request $request)
    {
        $productId = $request->input('product_id');
        $cart = session()->get('cart', []);
        $cart[] = $productId;
        
        session()->put('cart', $cart);

        return back()->with('success', 'Item added to session-based cart!');
    }

    public function clearCart()
    {
        session()->forget('cart');
        return back()->with('success', 'Cart cleared from session.');
    }
}
```

**Blade View (`resources/views/profile.blade.php`):**
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Profile, Sessions & Localization</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body class="p-5">
    <div class="container max-w-md">
        <!-- Lang switcher -->
        <div class="text-end mb-4">
            <a href="{{ route('locale.switch', 'en') }}" class="btn btn-sm btn-outline-primary">English</a>
            <a href="{{ route('locale.switch', 'hi') }}" class="btn btn-sm btn-outline-danger">हिंदी</a>
        </div>

        <h3>{{ __('messages.welcome') }}</h3>

        <!-- Profile display using sessions -->
        @if(session()->has('username'))
            <div class="card mb-4 p-3 text-center">
                <img src="{{ asset(session('avatar_path')) }}" class="rounded-circle mx-auto" style="width: 100px; height: 100px; object-fit: cover;">
                <h4>{{ session('username') }}</h4>
            </div>
        @endif

        @if(session('success'))
            <div class="alert alert-success">{{ session('success') }}</div>
        @endif

        <form action="{{ route('profile.upload') }}" method="POST" enctype="multipart/form-data" class="card p-4">
            @csrf
            <div class="mb-3">
                <label>Username</label>
                <input type="text" name="username" class="form-control" value="{{ old('username') }}">
                @error('username') <span class="text-danger">{{ $message }}</span> @enderror
            </div>
            <div class="mb-3">
                <label>Avatar Image</label>
                <input type="file" name="avatar" class="form-control">
                @error('avatar') <span class="text-danger">{{ $message }}</span> @enderror
            </div>
            <button type="submit" class="btn btn-success">Upload and Set Session</button>
        </form>

        <hr class="my-5">

        <h4>Session Cart (Count: {{ count(session('cart', [])) }})</h4>
        <ul>
            @foreach(session('cart', []) as $item)
                <li>Product ID: {{ $item }}</li>
            @endforeach
        </ul>
        <form action="{{ route('cart.add') }}" method="POST" class="d-inline">
            @csrf
            <input type="hidden" name="product_id" value="{{ rand(100, 999) }}">
            <button class="btn btn-sm btn-primary">Add Random Item</button>
        </form>
        <a href="{{ route('cart.clear') }}" class="btn btn-sm btn-danger">Clear Cart</a>
    </div>
</body>
</html>
```

---

## Unit V Examples

### Example 11: Form Request Validation & Custom Rule

Demonstrates advanced validation handling using standard rules, customized messages, and a custom Artisan rule class.

**Command to generate custom rule:**
```bash
php artisan make:rule Uppercase
```

**Custom Rule Class (`app/Rules/Uppercase.php`):**
```php
<?php

namespace App\Rules;

use Closure;
use Illuminate\Contracts\Validation\ValidationRule;

class Uppercase implements ValidationRule
{
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        if (strtoupper($value) !== $value) {
            $fail("The :attribute field must be strictly uppercase.");
        }
    }
}
```

**Form Request (`app/Http/Requests/RegisterRequest.php`):**
```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use App\Rules\Uppercase;

class RegisterRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'username' => ['required', 'string', 'min:3', new Uppercase],
            'email' => ['required', 'email', 'unique:users,email'],
            'password' => ['required', 'string', 'min:8', 'confirmed'],
        ];
    }

    public function messages(): array
    {
        return [
            'username.required' => 'We need to know your username!',
            'email.unique' => 'This email belongs to another registered account.',
        ];
    }
}
```

**Controller Handler (`app/Http/Controllers/AuthController.php`):**
```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\RegisterRequest;
use App\Models\User;

class AuthController extends Controller
{
    public function showForm()
    {
        return view('auth.register');
    }

    public function process(RegisterRequest $request)
    {
        // Data is already validated automatically
        $validated = $request->validated();

        User::create([
            'name' => $validated['username'],
            'email' => $validated['email'],
            'password' => bcrypt($validated['password']),
        ]);

        return redirect('/login')->with('success', 'Registration complete!');
    }
}
```

---

## Unit VI Examples

### Example 12: Database Migrations, Seeding, and Query Builder

Demonstrates schema setup, database seeding with factory, and CRUD queries using the `DB` facade.

**Migration (`database/migrations/2026_06_05_000000_create_products_table.php`):**
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up()
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->decimal('price', 8, 2);
            $table->integer('stock');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('products');
    }
};
```

**Seeder (`database/seeders/ProductSeeder.php`):**
```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class ProductSeeder extends Seeder
{
    public function run()
    {
        DB::table('products')->insert([
            ['name' => 'Laptop', 'price' => 74999.00, 'stock' => 15, 'created_at' => now()],
            ['name' => 'Smart Phone', 'price' => 24999.00, 'stock' => 30, 'created_at' => now()],
            ['name' => 'Headphones', 'price' => 1999.00, 'stock' => 50, 'created_at' => now()],
        ]);
    }
}
```

**Query Builder Controller (`app/Http/Controllers/ProductQueryController.php`):**
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;

class ProductQueryController extends Controller
{
    public function index()
    {
        // Retrieve products with stock > 0
        $products = DB::table('products')
            ->where('stock', '>', 0)
            ->orderBy('price', 'asc')
            ->get();

        return view('products.index', compact('products'));
    }

    public function create(Request $request)
    {
        // Insert product
        DB::table('products')->insert([
            'name' => $request->input('name'),
            'price' => $request->input('price'),
            'stock' => $request->input('stock'),
            'created_at' => now()
        ]);

        return back()->with('success', 'Product inserted via Query Builder!');
    }

    public function updatePrice($id, Request $request)
    {
        // Update product price
        DB::table('products')
            ->where('id', $id)
            ->update(['price' => $request->input('price')]);

        return back()->with('success', 'Price updated.');
    }

    public function destroy($id)
    {
        // Delete product
        DB::table('products')->where('id', $id)->delete();
        return back()->with('success', 'Product deleted.');
    }
}
```

### Example 13: Eloquent ORM & REST API Controller

Demonstrates Eloquent model definitions, one-to-many relationships, and creating standard API controllers with JSON outputs.

**Eloquent Models:**
```php
// app/Models/Author.php
class Author extends Model
{
    protected $fillable = ['name', 'bio'];

    public function books()
    {
        return $this->hasMany(Book::class);
    }
}

// app/Models/Book.php
class Book extends Model
{
    protected $fillable = ['title', 'isbn', 'author_id'];

    public function author()
    {
        return $this->belongsTo(Author::class);
    }
}
```

**API Route (`routes/api.php`):**
```php
use App\Http\Controllers\Api\BookApiController;

Route::apiResource('books', BookApiController::class);
```

**API Controller (`app/Http/Controllers/Api/BookApiController.php`):**
```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Book;
use Illuminate\Http\Request;

class BookApiController extends Controller
{
    public function index()
    {
        // Retrieve books with author relationship loaded
        $books = Book::with('author')->get();
        return response()->json(['success' => true, 'data' => $books], 200);
    }

    public function store(Request $request)
    {
        $validated = $request->validate([
            'title' => 'required|string|max:255',
            'isbn' => 'required|string|unique:books,isbn',
            'author_id' => 'required|exists:authors,id'
        ]);

        $book = Book::create($validated);
        return response()->json(['success' => true, 'message' => 'Book created!', 'data' => $book], 201);
    }

    public function show($id)
    {
        $book = Book::with('author')->find($id);

        if (!$book) {
            return response()->json(['success' => false, 'message' => 'Book not found'], 404);
        }

        return response()->json(['success' => true, 'data' => $book], 200);
    }

    public function update(Request $request, $id)
    {
        $book = Book::find($id);

        if (!$book) {
            return response()->json(['success' => false, 'message' => 'Book not found'], 404);
        }

        $book->update($request->all());
        return response()->json(['success' => true, 'message' => 'Book updated!', 'data' => $book], 200);
    }

    public function destroy($id)
    {
        $deleted = Book::destroy($id);

        if (!$deleted) {
            return response()->json(['success' => false, 'message' => 'Book not found'], 404);
        }

        return response()->json(['success' => true, 'message' => 'Book removed.'], 200);
    }
}
```

---

## Complete Applications

### Example 14: Simple Blog Application

**Complete structure for a blog with CRUD operations.**

See individual examples above for:
- Resource controller (Example 6)
- Blade templates (Example 8)
- Routes (Example 9)
- Middleware (Example 7)

**Additional Features:**

**Search Functionality:**
```php
// Controller
public function search(Request $request)
{
    $query = $request->input('q');
    
    $posts = Post::where('title', 'like', "%{$query}%")
        ->orWhere('content', 'like', "%{$query}%")
        ->paginate(10);
    
    return view('posts.search', compact('posts', 'query'));
}

// Route
Route::get('/search', [PostController::class, 'search'])->name('posts.search');
```

**Categories:**
```php
// Migration
Schema::create('categories', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('slug')->unique();
    $table->timestamps();
});

// Model relationship
class Post extends Model
{
    public function category()
    {
        return $this->belongsTo(Category::class);
    }
}

class Category extends Model
{
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}
```

---

## Running the Examples

1. **Create new Laravel project**
   ```bash
   composer create-project laravel/laravel examples
   cd examples
   ```

2. **Copy example code** into appropriate files

3. **Run migrations**
   ```bash
   php artisan migrate
   ```

4. **Start server**
   ```bash
   php artisan serve
   ```

5. **Visit URLs** in browser to test

---

## Tips for Practice

1. **Type all code manually** - Don't copy-paste
2. **Test each example** - Make sure it works
3. **Modify examples** - Add your own features
4. **Debug errors** - Learn from mistakes
5. **Combine examples** - Build larger applications

---

**Back to:** [Main Index](../README.md) | [Exam Preparation](../Exam_Preparation/README.md)
