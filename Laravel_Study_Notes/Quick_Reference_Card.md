# Laravel Quick Reference Card

## 🚀 Essential Commands Cheat Sheet

### Installation & Setup
```bash
composer create-project laravel/laravel project-name
cd project-name
cp .env.example .env
php artisan key:generate
php artisan serve
```

### Code Generation
```bash
php artisan make:controller Name [--resource|--api]
php artisan make:model Name [-mfs]
php artisan make:middleware Name
php artisan make:request Name
php artisan make:migration create_table_name
```

### Database
```bash
php artisan migrate
php artisan migrate:fresh --seed
php artisan db:seed
php artisan tinker
```

### Cache & Optimization
```bash
php artisan optimize:clear      # Development
php artisan optimize            # Production
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

---

## 🛣️ Routing Quick Reference

### Basic Routes
```php
Route::get('/url', function() {});
Route::post('/url', [Controller::class, 'method']);
Route::put('/url', [Controller::class, 'method']);
Route::delete('/url', [Controller::class, 'method']);
```

### Parameters
```php
// Required
Route::get('/user/{id}', function($id) {});

// Optional
Route::get('/user/{name?}', function($name = 'Guest') {});

// Constrained
Route::get('/user/{id}', function($id) {})
    ->where('id', '[0-9]+');
```

### Named Routes
```php
Route::get('/profile', function() {})->name('profile');

// Generate URL
route('profile');
redirect()->route('profile');
```

### Resource Routes
```php
Route::resource('posts', PostController::class);
// Creates: index, create, store, show, edit, update, destroy
```

### Route Groups
```php
Route::prefix('admin')
    ->middleware('auth')
    ->name('admin.')
    ->group(function() {
        Route::get('/dashboard', [AdminController::class, 'index']);
    });
```

---

## 🎮 Controllers Quick Reference

### Basic Controller
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    public function index()
    {
        return view('users.index');
    }

    public function store(Request $request)
    {
        $validated = $request->validate([
            'name' => 'required|string|max:255',
        ]);
        
        User::create($validated);
        
        return redirect()->route('users.index')
            ->with('success', 'Created!');
    }
}
```

### Middleware in Controller
```php
public function __construct()
{
    $this->middleware('auth');
    $this->middleware('admin')->only('destroy');
    $this->middleware('log')->except('index');
}
```

---

## 🗄️ Blade Templating Quick Reference

### Display Data
```blade
{{ $variable }}              {{-- Escaped --}}
{!! $html !!}                {{-- Unescaped --}}
{{ $name ?? 'Guest' }}       {{-- Default --}}
```

### Control Structures
```blade
@if($condition)
    Content
@elseif($other)
    Other
@else
    Default
@endif

@foreach($items as $item)
    {{ $loop->iteration }}. {{ $item }}
@endforeach

@forelse($items as $item)
    {{ $item }}
@empty
    No items
@endforelse
```

### Template Inheritance
```blade
{{-- Layout --}}
@yield('content')

{{-- Child --}}
@extends('layouts.app')

@section('content')
    Content here
@endsection
```

### Includes & Components
```blade
@include('partials.header')
@include('partials.alert', ['type' => 'success'])

<x-alert type="danger">
    Error message
</x-alert>
```

### Forms
```blade
<form method="POST" action="{{ route('users.store') }}">
    @csrf
    @method('PUT')
    
    <input type="text" name="name" value="{{ old('name') }}">
    @error('name')
        <span>{{ $message }}</span>
    @enderror
    
    <button type="submit">Submit</button>
</form>
```

### Assets & URLs
```blade
<link href="{{ asset('css/app.css') }}" rel="stylesheet">
<script src="{{ asset('js/app.js') }}"></script>

<a href="{{ url('/') }}">Home</a>
<a href="{{ route('profile') }}">Profile</a>
<a href="{{ action([UserController::class, 'index']) }}">Users</a>
```

---

## 📤 Responses Quick Reference

### Basic Responses
```php
return 'Simple text';
return view('welcome');
return redirect('/home');
```

### JSON Response
```php
return response()->json([
    'status' => 'success',
    'data' => $data
], 200);
```

### With Headers
```php
return response($content)
    ->header('Content-Type', 'application/json')
    ->header('X-Custom', 'Value');
```

### With Cookies
```php
return response($content)
    ->cookie('name', 'value', 60); // 60 minutes
```

### Redirects
```php
return redirect('/home');
return redirect()->route('profile');
return redirect()->back();
return back()->withInput();

// With flash message
return redirect('/home')->with('status', 'Success!');
```

---

## 🔄 Request Lifecycle

```
1. public/index.php (Entry Point)
        ↓
2. HTTP Kernel (Middleware)
        ↓
3. Service Providers (Bootstrap)
        ↓
4. Router (Match Route)
        ↓
5. Middleware (Filter)
        ↓
6. Controller (Logic)
        ↓
7. Model (Database)
        ↓
8. View (Render)
        ↓
9. Response (Send)
```

---

## 📁 Directory Structure

```
app/
├── Http/Controllers/    # Controllers
├── Models/              # Eloquent models
└── Middleware/          # Middleware

database/
├── migrations/          # Database migrations
├── seeders/            # Database seeders
└── factories/          # Model factories

resources/views/         # Blade templates
routes/
├── web.php             # Web routes
└── api.php             # API routes

public/                  # Public files
storage/                 # Logs, cache, uploads
config/                  # Configuration files
```

---

## 🔑 Important Concepts

### MVC Pattern
- **Model**: Data & business logic
- **View**: User interface
- **Controller**: Request handling

### HTTP Verbs
- **GET**: Retrieve data
- **POST**: Create new resource
- **PUT/PATCH**: Update resource
- **DELETE**: Remove resource

### Security
- Always use `@csrf` in forms
- Validate all input
- Use escaped output `{{ }}`
- Hash passwords with `bcrypt()`

---

## 💡 Common Patterns

### CRUD Operations
```php
// Index - List all
GET     /posts           → index()

// Create - Show form
GET     /posts/create    → create()

// Store - Save new
POST    /posts           → store()

// Show - Display one
GET     /posts/{id}      → show()

// Edit - Show edit form
GET     /posts/{id}/edit → edit()

// Update - Save changes
PUT     /posts/{id}      → update()

// Destroy - Delete
DELETE  /posts/{id}      → destroy()
```

### Validation
```php
$validated = $request->validate([
    'title' => 'required|string|max:255',
    'email' => 'required|email|unique:users',
    'age' => 'required|integer|min:18',
]);
```

### Eloquent Queries
```php
// Get all
User::all();

// Find by ID
User::find(1);

// Where clause
User::where('active', 1)->get();

// Create
User::create(['name' => 'John']);

// Update
$user->update(['name' => 'Jane']);

// Delete
$user->delete();
```

---

## ⚡ Quick Tips

### Do's ✅
- Use resource controllers for CRUD
- Apply middleware for protection
- Validate all user input
- Use named routes
- Follow naming conventions
- Keep controllers thin

### Don'ts ❌
- Don't put business logic in controllers
- Don't use raw SQL (use Eloquent)
- Don't forget CSRF tokens
- Don't expose sensitive data
- Don't hardcode configuration
- Don't skip validation

---

## 🎯 Exam Checklist

Before exam, ensure you can:
- [ ] Explain MVC architecture
- [ ] Install Laravel project
- [ ] Create routes with parameters
- [ ] Build controllers (basic & resource)
- [ ] Use Blade templating
- [ ] Pass data to views
- [ ] Create different responses
- [ ] Apply middleware
- [ ] Implement template inheritance
- [ ] Use route groups
- [ ] Generate URLs
- [ ] Write CRUD operations

---

## 📞 Emergency Help

### Common Errors

**"Class not found"**
```bash
composer dump-autoload
```

**"Route not defined"**
```bash
php artisan route:clear
php artisan route:list
```

**"Permission denied"**
```bash
chmod -R 775 storage bootstrap/cache
```

**"CSRF token mismatch"**
- Add `@csrf` to forms
- Check `.env` APP_URL

---

**Print this page for quick reference during study and exams!**

---

**Complete Guide:** [Main README](../README.md) | [Commands Reference](Commands_Reference.md)
