# INT221 - MVC Programming (Laravel) - Complete Solved Questions

This document contains full, complete, and exam-ready code solutions for the 10 Laravel practical questions. It includes exact route definitions, controllers, middleware, migration files, views, expected outputs, and key viva-voce questions.

---

## Q1: Store User Preferred Language in Session & Apply Locale Using Middleware

### Question
Implement a mechanism to store the user's preferred language in a session variable named `locale` when they select a language from a dropdown. Also create a middleware that checks for the `locale session` variable and sets the application locale accordingly for subsequent requests.

### Solution

#### 1. Define routes (`routes/web.php`)
```php
use Illuminate\Support\Facades\Route;
use Illuminate\Support\Facades\App;

// Route to render the language selector page
Route::get('/', function () {
    return view('welcome');
});

// Route to update the preferred language in session and redirect back
Route::get('/language/{locale}', function ($locale) {
    // Validate that the requested locale is supported
    if (in_array($locale, ['en', 'hi', 'fr', 'es'])) {
        session(['locale' => $locale]);
    }
    return redirect()->back();
});
```

#### 2. Create the Language Selector View (`resources/views/welcome.blade.php`)
```html
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <title>Language Preference Selection</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; padding: 50px; background-color: #f4f6f9; }
        .card { background: #fff; padding: 30px; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); max-width: 400px; margin: 0 auto; text-align: center; }
        select { padding: 10px; width: 100%; border-radius: 4px; border: 1px solid #ccc; font-size: 16px; margin-top: 15px; }
        h2 { color: #333; }
        p { color: #666; font-size: 14px; }
    </style>
</head>
<body>
    <div class="card">
        <h2>Preferred Language</h2>
        <p>Current Locale: <strong>{{ strtoupper(app()->getLocale()) }}</strong></p>
        
        <form>
            <!-- Dropdown that triggers page redirect on change -->
            <select onchange="window.location.href='/language/' + this.value">
                <option value="en" {{ app()->getLocale() == 'en' ? 'selected' : '' }}>English</option>
                <option value="hi" {{ app()->getLocale() == 'hi' ? 'selected' : '' }}>Hindi (हिन्दी)</option>
                <option value="fr" {{ app()->getLocale() == 'fr' ? 'selected' : '' }}>French (Français)</option>
                <option value="es" {{ app()->getLocale() == 'es' ? 'selected' : '' }}>Spanish (Español)</option>
            </select>
        </form>
    </div>
</body>
</html>
```

#### 3. Create the Locale Middleware (`app/Http/Middleware/SetLocale.php`)
Generate this file using the CLI command:
```bash
php artisan make:middleware SetLocale
```
Add the following logic:
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\App;

class SetLocale
{
    /**
     * Handle an incoming request.
     */
    public function handle(Request $request, Closure $next)
    {
        // Check if session has a stored locale preference
        if (session()->has('locale')) {
            App::setLocale(session('locale'));
        }

        return $next($request);
    }
}
```

#### 4. Register the Middleware
Depending on your Laravel version, register it in the appropriate entry point:

##### For Laravel 11+ (`bootstrap/app.php`):
```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        \App\Http\Middleware\SetLocale::class,
    ]);
})
```

##### For Laravel 9 & 10 (`app/Http/Kernel.php`):
Add to the `$middlewareGroups['web']` array:
```php
protected $middlewareGroups = [
    'web' => [
        // ... other middleware ...
        \App\Http\Middleware\SetLocale::class,
    ],
];
```

---

## Q2: Create Form with Name, Email and Password Validation

### Question
Create a form with fields for name, email, and password. Implement validation rules.

### Solution

#### 1. Define Routes (`routes/web.php`)
```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

// Route to display registration form
Route::get('/register', function () {
    return view('register');
});

// Route to handle form submission and validation rules
Route::post('/register', function (Request $request) {
    // Validate request parameters
    $validated = $request->validate([
        'name' => 'required|string|min:3|max:50',
        'email' => 'required|email',
        'password' => 'required|string|min:8'
    ]);

    return response()->json([
        'status' => 'success',
        'message' => 'Validation Passed Successfully!',
        'data' => $validated
    ]);
});
```

#### 2. Create the Registration View (`resources/views/register.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>User Registration</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #f7f9fc; padding: 40px; display: flex; justify-content: center; }
        .form-container { background: #fff; padding: 25px; border-radius: 8px; box-shadow: 0 4px 10px rgba(0,0,0,0.05); width: 350px; }
        .form-group { margin-bottom: 15px; }
        .form-group label { display: block; margin-bottom: 5px; font-weight: bold; }
        .form-group input { width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
        .btn-submit { background-color: #4f46e5; color: #fff; width: 100%; padding: 10px; border: none; border-radius: 4px; font-size: 16px; cursor: pointer; }
        .error-msg { color: #dc2626; font-size: 12px; margin-top: 5px; }
        .success-banner { background: #d1fae5; color: #065f46; padding: 10px; border-radius: 4px; margin-bottom: 15px; text-align: center; }
    </style>
</head>
<body>
    <div class="form-container">
        <h2>Register Account</h2>
        
        <form method="POST" action="/register">
            @csrf

            <!-- Name Input Field -->
            <div class="form-group">
                <label for="name">Name</label>
                <input type="text" name="name" id="name" value="{{ old('name') }}">
                @error('name')
                    <div class="error-msg">{{ $message }}</div>
                @enderror
            </div>

            <!-- Email Input Field -->
            <div class="form-group">
                <label for="email">Email</label>
                <input type="text" name="email" id="email" value="{{ old('email') }}">
                @error('email')
                    <div class="error-msg">{{ $message }}</div>
                @enderror
            </div>

            <!-- Password Input Field -->
            <div class="form-group">
                <label for="password">Password</label>
                <input type="password" name="password" id="password">
                @error('password')
                    <div class="error-msg">{{ $message }}</div>
                @enderror
            </div>

            <button type="submit" class="btn-submit">Submit</button>
        </form>
    </div>
</body>
</html>
```

---

## Q3: Create a Route Group with prefix `/student` and create 3 routes

### Question
Create a Route Group with prefix `/student` and create 3 routes.

### Solution

#### 1. Define Route Group inside (`routes/web.php`)
```php
use Illuminate\Support\Facades\Route;

// Define Route Group with Prefix '/student'
Route::prefix('student')->group(function () {
    
    // Route 1: student/profile
    Route::get('/profile', function () {
        return "<h3>Student Profile Page</h3><p>Retrieving personal credentials...</p>";
    });

    // Route 2: student/marks
    Route::get('/marks', function () {
        return "<h3>Student Term End Marks</h3><p>Marks Sheet details: cgpa=9.2</p>";
    });

    // Route 3: student/attendance
    Route::get('/attendance', function () {
        return "<h3>Student Attendance Registry</h3><p>Current Subject attendance details: 85%</p>";
    });
});
```

#### 2. Accessible URLs mapping
*   `http://localhost:8000/student/profile`
*   `http://localhost:8000/student/marks`
*   `http://localhost:8000/student/attendance`

---

## Q4: Create another Route Group with prefix `/interest` displaying programming interests (3 routes)

### Question
Create another Route Group with prefix `/interest` that displays your interest in programming languages. Create 3 routes.

### Solution

#### 1. Define Route Group inside (`routes/web.php`)
```php
use Illuminate\Support\Facades\Route;

// Define Route Group with Prefix '/interest'
Route::prefix('interest')->group(function () {

    // Route 1: interest/php
    Route::get('/php', function () {
        return "<h2>My Interest: PHP</h2><p>PHP powers over 77% of the web. I enjoy Laravel and Composer packaging.</p>";
    });

    // Route 2: interest/laravel
    Route::get('/laravel', function () {
        return "<h2>My Interest: Laravel</h2><p>Laravel provides an amazing developer experience (DX) and a clean syntax structure.</p>";
    });

    // Route 3: interest/javascript
    Route::get('/javascript', function () {
        return "<h2>My Interest: JavaScript</h2><p>JavaScript allows me to build dynamic interactive user interfaces with React and Vue.</p>";
    });
});
```

#### 2. Accessible URLs mapping
*   `http://localhost:8000/interest/php`
*   `http://localhost:8000/interest/laravel`
*   `http://localhost:8000/interest/javascript`

---

## Q5: Named Route and Redirect After Form Submission

### Question
Implement a scenario where upon submitting a form on `/submit-info`, the user is redirected to a named route called `dashboard`.

### Solution

#### 1. Define Routes (`routes/web.php`)
```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

// 1. Named Route for Dashboard View Page
Route::get('/dashboard', function () {
    return view('dashboard');
})->name('dashboard'); // Route name declaration

// 2. Route to display form page
Route::get('/submit-info', function () {
    return view('submit_info');
});

// 3. POST submission route handling the redirect to dashboard
Route::post('/submit-info', function (Request $request) {
    // Run validation or business logic here
    $request->validate([
        'username' => 'required|string|min:3',
    ]);

    // Redirect the response back using route helper method
    return redirect()->route('dashboard')->with('success_message', 'Form Submitted! Welcome to the Dashboard.');
});
```

#### 2. Create Submit Form View (`resources/views/submit_info.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Submit Information</title>
    <style>
        body { font-family: Arial, sans-serif; padding: 40px; text-align: center; }
        form { width: 300px; margin: 0 auto; padding: 20px; border: 1px solid #ccc; border-radius: 5px; }
        input[type="text"] { width: 90%; padding: 8px; margin-bottom: 10px; }
        button { background-color: #2563eb; color: #fff; padding: 10px 15px; border: none; border-radius: 4px; cursor: pointer; }
    </style>
</head>
<body>
    <h2>User Entry Sheet</h2>
    <form action="/submit-info" method="POST">
        @csrf
        <input type="text" name="username" placeholder="Enter Username" required>
        <button type="submit">Submit Info</button>
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
<body style="font-family: Arial, sans-serif; text-align: center; padding: 50px;">
    @if(session('success_message'))
        <div style="background-color: #d1fae5; color: #065f46; padding: 15px; display: inline-block; border-radius: 5px;">
            {{ session('success_message') }}
        </div>
    @endif
    <h1>Welcome Dashboard</h1>
    <p>You have successfully redirected to the secure named route.</p>
</body>
</html>
```

---

## Q6: Migration for Products Table

### Question
Create a Migration to define a products table with columns `name` (string), `description` (text), and `price` (decimal).

### Solution

#### 1. Generate the Migration File
Run the following artisan helper command:
```bash
php artisan make:migration create_products_table
```

#### 2. Edit the Migration Code
Open the newly created migration file (located inside `database/migrations/xxxx_xx_xx_xxxxxx_create_products_table.php`) and write the table definition schema:
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id(); // Primary key (bigint auto-increment)
            $table->string('name'); // Varchar column for product name
            $table->text('description'); // Text column for product description
            $table->decimal('price', 8, 2); // Decimal column for price (total 8 digits, 2 decimal places)
            $table->timestamps(); // Created_at and Updated_at timestamps
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('products');
    }
};
```

#### 3. Execute Migration Command
Run the migration CLI to build the table structure in the configured database engine:
```bash
php artisan migrate
```

---

## Q7: Insert Three Products Using Query Builder

### Question
After running migration, use Query Builder to insert three sample product records.

### Solution

#### 1. Create Route to Execute DB Query Builder (`routes/web.php`)
We use the `DB` facade to execute Query Builder insert statement.
```php
use Illuminate\Support\Facades\Route;
use Illuminate\Support\Facades\DB;

// Route to run sample queries
Route::get('/insert-products', function () {
    
    // Using Query Builder insert helper
    DB::table('products')->insert([
        [
            'name' => 'Laptop',
            'description' => 'Dell Inspiron 15 with 16GB RAM and 512GB SSD',
            'price' => 50000.00,
            'created_at' => now(),
            'updated_at' => now()
        ],
        [
            'name' => 'Mouse',
            'description' => 'Logitech Wireless Silent Optical Mouse',
            'price' => 500.00,
            'created_at' => now(),
            'updated_at' => now()
        ],
        [
            'name' => 'Keyboard',
            'description' => 'HP Mechanical Wired Keyboard Backlit RGB',
            'price' => 1500.00,
            'created_at' => now(),
            'updated_at' => now()
        ]
    ]);

    return "<h3>Products Stored Successfully!</h3><p>Check your database table using PHPMyAdmin or database tools.</p>";
});
```

---

## Q8: Display Products in Table Format

### Question
Display the entered product data in table format.

### Solution

#### 1. Define Route (`routes/web.php`)
```php
use Illuminate\Support\Facades\Route;
use Illuminate\Support\Facades\DB;

Route::get('/products', function () {
    // Retrieve all products from the products table using DB query builder
    $products = DB::table('products')->get();

    // Pass data array variables into view template using compact method
    return view('products', compact('products'));
});
```

#### 2. Create the View File (`resources/views/products.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Products Catalogue</title>
    <style>
        body { font-family: 'Helvetica Neue', Arial, sans-serif; background-color: #f9fafb; padding: 40px; }
        h2 { text-align: center; color: #111827; }
        table { width: 80%; margin: 20px auto; border-collapse: collapse; background-color: #ffffff; box-shadow: 0 4px 6px rgba(0,0,0,0.05); border-radius: 8px; overflow: hidden; }
        th { background-color: #4f46e5; color: #ffffff; padding: 12px 15px; text-align: left; font-weight: 600; }
        td { padding: 12px 15px; border-bottom: 1px solid #e5e7eb; color: #374151; }
        tr:hover { background-color: #f3f4f6; }
        .price-align { font-weight: bold; text-align: right; }
    </style>
</head>
<body>
    <h2>Entered Product List</h2>

    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Name</th>
                <th>Description</th>
                <th style="text-align: right;">Price (INR)</th>
            </tr>
        </thead>
        <tbody>
            @foreach($products as $product)
                <tr>
                    <td>{{ $product->id }}</td>
                    <td>{{ $product->name }}</td>
                    <td>{{ $product->description }}</td>
                    <td class="price-align">₹{{ number_format($product->price, 2) }}</td>
                </tr>
            @endforeach
        </tbody>
    </table>
</body>
</html>
```

---

## Q9: File Upload Form (avatar)

### Question
Create a form with a file input field named avatar and a submit button.

### Solution

#### 1. Define Routes (`routes/web.php`)
```php
use Illuminate\Support\Facades\Route;

// Render profile view
Route::get('/upload-avatar', function () {
    return view('upload_avatar');
});
```

#### 2. Create View File (`resources/views/upload_avatar.blade.php`)
> [!IMPORTANT]
> The form attribute `enctype="multipart/form-data"` is absolutely required whenever forms handle file transmissions. If left out, `$request->file('avatar')` will receive `null`.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Profile Avatar Form</title>
    <style>
        body { font-family: sans-serif; display: flex; justify-content: center; padding: 50px; background-color: #f3f4f6; }
        .upload-card { background: #fff; padding: 25px; border-radius: 6px; box-shadow: 0 4px 8px rgba(0,0,0,0.05); width: 320px; }
        h3 { margin-bottom: 20px; color: #333; }
        input[type="file"] { margin-bottom: 15px; width: 100%; }
        button { background-color: #10b981; color: white; width: 100%; padding: 10px; border: none; border-radius: 4px; cursor: pointer; }
    </style>
</head>
<body>
    <div class="upload-card">
        <h3>Upload Profile Picture</h3>
        
        <!-- Action target URL matches Q10 storage controller -->
        <form action="/upload" method="POST" enctype="multipart/form-data">
            @csrf
            
            <input type="file" name="avatar" required accept="image/*">
            <button type="submit">Upload</button>
        </form>
    </div>
</body>
</html>
```

---

## Q10: Store Uploaded File & Display Success Message

### Question
Store the uploaded file and display success message.

### Solution

#### 1. Define POST route handler (`routes/web.php`)
```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::post('/upload', function (Request $request) {
    // 1. Validate the file input request parameters
    $request->validate([
        'avatar' => 'required|file|image|mimes:jpeg,png,jpg,gif,webp|max:2048' // Max 2MB size limit
    ]);

    // 2. Perform file validation and upload checks
    if ($request->hasFile('avatar')) {
        $file = $request->file('avatar');
        
        // 3. Storing file inside storage/app/public/avatars folder structure
        // Using automatic name hashing for standard security checks
        $path = $file->store('avatars', 'public');

        return "<h3>Success: File Uploaded Successfully!</h3><p>Stored Relative path: <strong>" . $path . "</strong></p>";
    }

    return response("No file uploaded", 400);
});
```

#### 2. Verification Steps
*   Run the command below in the terminal to create a symbolic link, making images accessible from the browser:
    ```bash
    php artisan storage:link
    ```
*   The uploaded image will be safely stored under: `storage/app/public/avatars/`
*   It is served publicly using: `http://localhost:8000/storage/` + the relative `$path`.

---

# Important Exam-focused Viva-Voce Questions

## 1. What is localization, and how is it implemented in Laravel?
Localization is the process of building multilingual websites that dynamically serve different languages. In Laravel, it is implemented using the `App::setLocale($lang)` method. Translation string key arrays are stored in folders inside `lang/` (e.g. `lang/en/labels.php`, `lang/fr/labels.php`). In Blade templates, we read values using helpers like `{{ __('labels.welcome') }}` or `@lang('labels.welcome')`.

## 2. What role does Middleware play in routing?
Middleware works as a HTTP request filter. When a request hits a route, it passes through middleware before hitting the Controller logic. It is useful for session manipulation, auth checks, setting application locale, adding response headers, or logging request metadata.

## 3. Why is the `@csrf` directive mandatory in forms?
CSRF (Cross-Site Request Forgery) is a type of attack where unauthorized commands are executed from a trusted user context. `@csrf` generates a random session token field inside forms. When forms submit, the `VerifyCsrfToken` middleware validates it. Submissions fail with a `419 Page Expired` error if the token is missing.

## 4. What is the difference between Query Builder and Eloquent ORM?
*   **Query Builder:** Uses the `DB` facade to execute SQL queries. It is faster and works with direct database arrays. Example: `DB::table('products')->get()`.
*   **Eloquent ORM:** An ActiveRecord implementation. It works with model classes and object relations. Example: `Product::all()`.

## 5. What are the advantages of using Migrations?
Migrations act as version control for databases. They allow team members to keep database structures consistent across different environments without sharing raw SQL files. You define columns in PHP files, and can run (`php artisan migrate`) or rollback changes (`php artisan migrate:rollback`).

## 6. What does `enctype="multipart/form-data"` mean, and when is it required?
This attribute tells the browser how to encode data before sending it to the server. It is mandatory for any form that contains file input fields (like image or document uploads). Without it, the browser only sends the file's filename string, and the file content is lost.

## 7. How do Route Groups help clean up route definitions?
Route Groups allow you to share common attributes, such as URI prefixes, middleware layers, subdomains, namespaces, or route name prefixes, across multiple route definitions. Instead of assigning them individually, you apply them to a single outer closure container.

## 8. What is the difference between `store()` and `storeAs()` in Laravel file uploads?
*   `store('path', 'disk')` automatically generates a unique hashed filename for the file to prevent file collisions.
*   `storeAs('path', 'filename.ext', 'disk')` allows developers to define a custom target filename for the stored file.

---

# Practical Checklist & Command Reference Card

Use this reference during practical sessions to initialize resources quickly:

| Action | Artisan CLI Command / Snippet |
|---|---|
| **Create Controller** | `php artisan make:controller ControllerName` |
| **Create Middleware** | `php artisan make:middleware SetLocale` |
| **Create Migration** | `php artisan make:migration create_products_table` |
| **Run Migrations** | `php artisan migrate` |
| **Link Public Storage** | `php artisan storage:link` |
| **Clear App Cache** | `php artisan config:clear` / `php artisan cache:clear` |
| **Start Server** | `php artisan serve` |
