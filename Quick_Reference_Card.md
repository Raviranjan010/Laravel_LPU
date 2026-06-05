# Laravel Quick Reference Card

A consolidated sheet of code conventions, syntax shortcuts, and common design patterns for rapid exam review.

---

## 🛣️ Routing Syntax Cheatsheet

```php
// 1. Basic Route
Route::get('/welcome', function () { return view('welcome'); });

// 2. Route calling Controller action
Route::post('/profile', [UserController::class, 'update'])->name('profile.update');

// 3. Mandatory and Optional parameters (with constraint check)
Route::get('/user/{id}/{name?}', function ($id, $name = 'guest') {
    return "User {$id} is named {$name}";
})->where(['id' => '[0-9]+', 'name' => '[a-zA-Z]+']);

// 4. Route Group (Prefix, Middleware, Name space)
Route::prefix('admin')->middleware('auth')->name('admin.')->group(function () {
    Route::get('/users', [AdminController::class, 'index'])->name('users');
});

// 5. RESTful Resource Route
Route::resource('photos', PhotoController::class);

// 6. API RESTful Resource Route (excludes create/edit view routes)
Route::apiResource('products', ProductController::class);
```

---

## 🎮 Controller Actions & Responses

```php
// Standard RESTful Actions Mapping:
public function index()             { return view('index', ['data' => Model::all()]); }
public function create()            { return view('create'); }
public function store(Request $r)    { Model::create($r->all()); return redirect()->route('index'); }
public function show($id)           { return view('show', ['item' => Model::findOrFail($id)]); }
public function edit($id)             { return view('edit', ['item' => Model::findOrFail($id)]); }
public function update(Request $r, $id) { Model::findOrFail($id)->update($r->all()); return back(); }
public function destroy($id)        { Model::destroy($id); return redirect()->route('index'); }

// Dynamic Responses:
return response()->json(['success' => true, 'data' => $data], 201); // JSON output with 201 Created status
return response('Custom Text')->header('Content-Type', 'text/plain')->cookie('user_session', 'active', 60);
return redirect()->route('profile')->with('status', 'Profile details updated!'); // Redirect with session flash
```

---

## 🗄️ Blade Directives Cheatsheet

```blade
{{-- 1. Rendering variables (escaped vs unescaped) --}}
<p>{{ $escaped_string }}</p>
<div>{!! $unescaped_html_string !!}</div>

{{-- 2. Conditionals --}}
@if($score >= 90)
    <span class="badge bg-success">Outstanding</span>
@elseif($score >= 40)
    <span class="badge bg-warning">Pass</span>
@else
    <span class="badge bg-danger">Fail</span>
@endif

{{-- 3. Loop Iterations --}}
@foreach($users as $user)
    <li>{{ $loop->iteration }} - {{ $user->name }} (First: {{ $loop->first ? 'Yes' : 'No' }})</li>
@endforeach

@forelse($tasks as $task)
    <li>{{ $task->name }}</li>
@empty
    <li>No tasks pending!</li>
@endforelse

{{-- 4. Template Inheritance --}}
{{-- In layouts/app.blade.php: --}}
@yield('content')
@stack('scripts')

{{-- In child view: --}}
@extends('layouts.app')
@section('content')
    <p>Body Content</p>
@endsection
@push('scripts')
    <script src="app.js"></script>
@endpush
```

---

## 🛡️ Form Security, Spoofing & Validation

```html
<form action="{{ route('tasks.update', $task->id) }}" method="POST" enctype="multipart/form-data">
    <!-- 1. CSRF Verification token -->
    @csrf

    <!-- 2. Method Spoofing for PUT -->
    @method('PUT')

    <!-- 3. Form input value repopulation -->
    <input type="text" name="title" value="{{ old('title', $task->title) }}" class="@error('title') is-invalid @enderror">

    <!-- 4. Inline Error rendering -->
    @error('title')
        <div class="alert alert-danger">{{ $message }}</div>
    @enderror
</form>
```

```php
// In-Controller Validation Rules:
$validated = $request->validate([
    'title' => 'required|string|max:100',
    'email' => 'required|email|unique:users,email',
    'password' => 'required|min:8|confirmed', // expects input 'password_confirmation'
    'avatar' => 'required|file|image|max:2048' // max 2MB
]);
```

---

## 🗃️ Database Migrations, Seeding & Eloquent ORM

### Migrations
```php
// Table fields definitions:
Schema::create('orders', function (Blueprint $table) {
    $table->id();
    $table->string('order_code')->unique();
    $table->integer('quantity')->default(1);
    $table->decimal('total_price', 10, 2);
    $table->foreignId('user_id')->constrained('users')->onDelete('cascade'); // Foreign key mapping
    $table->timestamps();
});
```

### Seeding & Factories
```php
// 1. In database/factories/PostFactory.php:
public function definition(): array {
    return [
        'title' => $this->faker->sentence(),
        'body' => $this->faker->paragraph(),
        'user_id' => \App\Models\User::factory()
    ];
}

// 2. In database/seeders/DatabaseSeeder.php:
public function run(): void {
    \App\Models\Post::factory(50)->create(); // Create 50 mock posts with Faker
}
```

### Query Builder vs Eloquent CRUD
```php
// DB Query Builder:
DB::table('users')->insert(['name' => 'Roy', 'email' => 'roy@lpu.in']);
$users = DB::table('users')->where('status', 'active')->orderBy('name')->get();
DB::table('users')->where('id', 1)->update(['status' => 'suspended']);
DB::table('users')->where('id', 1)->delete();

// Eloquent ORM:
User::create(['name' => 'Roy', 'email' => 'roy@lpu.in']); // Requires fillable array whitelist
$users = User::where('status', 'active')->orderBy('name')->get();
User::findOrFail(1)->update(['status' => 'suspended']);
User::destroy(1);
```

### Eloquent Relationships
```php
// 1. One-to-Many Relationship (User has many Posts)
// In User.php:
public function posts() { return $this->hasMany(Post::class); }
// In Post.php:
public function user() { return $this->belongsTo(User::class); }

// Eager loading (solves N+1 query problem):
$posts = Post::with('user')->get();
```
