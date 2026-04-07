# 🔥 High-Probability Practical Exam Questions & Solutions

## 📋 Complete Solutions for Expected Exam Patterns

This document contains **100% practical solutions** for all expected exam question types with step-by-step implementations.

---

## Table of Contents
- [1. Cookie/Session Based Questions](#1-cookiesession-based-questions)
- [2. Array + View + Compact](#2-array--view--compact)
- [3. Routing + URL Traversing + Constraints](#3-routing--url-traversing--constraints)
- [4. Fallback + Group Routing](#4-fallback--group-routing)
- [5. Middleware Logic](#5-middleware-logic)
- [6. Controller + Constraints](#6-controller--constraints)
- [7. Form + Validation](#7-form--validation)
- [8. Redirect + Flash Message](#8-redirect--flash-message)
- [9. Response Types](#9-response-types)
- [10. Mixed Logic Questions](#10-mixed-logic-questions)
- [Specific Exam Questions Solutions](#specific-exam-questions-solutions)

---

## 1. Cookie/Session Based Questions

### ✅ Question 1.1: Create cookie "crucial-data", display it, then delete it

**File:** `routes/web.php`

```php
use Illuminate\Support\Facades\Route;
use Illuminate\Support\Facades\Cookie;

// Create cookie
Route::get('/cookie/create', function() {
    $cookie = Cookie::make('crucial-data', 'ImportantValue', 60); // 60 minutes
    
    return response('Cookie "crucial-data" created with value: ImportantValue')
        ->cookie($cookie);
});

// Display cookie
Route::get('/cookie/show', function(\Illuminate\Http\Request $request) {
    $value = $request->cookie('crucial-data');
    
    if ($value) {
        return response('Cookie Value: ' . $value);
    }
    
    return response('Cookie not found!');
});

// Delete cookie
Route::get('/cookie/delete', function() {
    $cookie = Cookie::forget('crucial-data');
    
    return response('Cookie "crucial-data" has been deleted')
        ->cookie($cookie);
});
```

**Testing Steps:**
1. Visit: `http://localhost:8000/cookie/create` → Cookie created
2. Visit: `http://localhost:8000/cookie/show` → Shows value
3. Visit: `http://localhost:8000/cookie/delete` → Cookie deleted
4. Visit: `http://localhost:8000/cookie/show` → Shows "Cookie not found!"

---

### ✅ Question 1.2: Store user name in session and display on another route

**File:** `routes/web.php`

```php
use Illuminate\Support\Facades\Route;
use Illuminate\Support\Facades\Session;

// Store name in session
Route::get('/session/store/{name}', function($name) {
    session(['username' => $name]);
    
    return "Name '{$name}' stored in session!";
});

// Display name from session
Route::get('/session/show', function() {
    $name = session('username');
    
    if ($name) {
        return "Hello, {$name}! Welcome back.";
    }
    
    return "No name found in session.";
});

// Clear session
Route::get('/session/clear', function() {
    session()->forget('username');
    return "Session cleared!";
});
```

**Testing:**
1. `http://localhost:8000/session/store/John` → Stores "John"
2. `http://localhost:8000/session/show` → Displays "Hello, John! Welcome back."

---

### ✅ Question 1.3: Create cookie with 5 min expiry and check if exists

```php
Route::get('/cookie/set-5min', function() {
    return response('Cookie set for 5 minutes')
        ->cookie('temp_data', 'TemporaryValue', 5); // 5 minutes
});

Route::get('/cookie/check', function(\Illuminate\Http\Request $request) {
    if ($request->hasCookie('temp_data')) {
        $value = $request->cookie('temp_data');
        return "Cookie exists! Value: {$value}";
    }
    
    return "Cookie does not exist or has expired.";
});
```

---

### ✅ Question 1.4: Queue a cookie and retrieve using request

```php
Route::get('/cookie/queue', function() {
    // Queue cookie (doesn't send immediately)
    Cookie::queue('queued_cookie', 'QueuedValue', 30);
    
    return response('Cookie queued successfully!');
});

Route::get('/cookie/retrieve', function(\Illuminate\Http\Request $request) {
    $value = $request->cookie('queued_cookie');
    
    if ($value) {
        return "Retrieved queued cookie: {$value}";
    }
    
    return "No queued cookie found.";
});
```

---

### ✅ Question 1.5: Store multiple cookies and display all

```php
Route::get('/cookies/multiple', function() {
    return response('Multiple cookies set!')
        ->cookie('user', 'John', 60)
        ->cookie('role', 'admin', 60)
        ->cookie('theme', 'dark', 60)
        ->cookie('language', 'en', 60);
});

Route::get('/cookies/display-all', function(\Illuminate\Http\Request $request) {
    $cookies = [
        'user' => $request->cookie('user'),
        'role' => $request->cookie('role'),
        'theme' => $request->cookie('theme'),
        'language' => $request->cookie('language'),
    ];
    
    $output = "<h2>All Cookies:</h2><ul>";
    
    foreach ($cookies as $key => $value) {
        $output .= "<li><strong>{$key}:</strong> {$value}</li>";
    }
    
    $output .= "</ul>";
    
    return response($output);
});
```

---

## 2. Array + View + Compact

### ✅ Question 2.1: Array of 10 cricketers using compact() and implode()

**Step 1:** Create route
**File:** `routes/web.php`

```php
use Illuminate\Support\Facades\Route;

Route::get('/cricketers', function() {
    $cricketers = [
        'Virat Kohli',
        'Rohit Sharma',
        'MS Dhoni',
        'Sachin Tendulkar',
        'KL Rahul',
        'Hardik Pandya',
        'Jasprit Bumrah',
        'Ravindra Jadeja',
        'Shikhar Dhawan',
        'Rishabh Pant'
    ];
    
    return view('field', compact('cricketers'));
});
```

**Step 2:** Create view
**File:** `resources/views/field.blade.php`

```blade
<!DOCTYPE html>
<html>
<head>
    <title>Cricketers List</title>
</head>
<body>
    <h1>Top 10 Cricketers</h1>
    
    <h2>Using implode():</h2>
    <p>{{ implode(', ', $cricketers) }}</p>
    
    <h2>Using foreach loop:</h2>
    <ol>
        @foreach($cricketers as $index => $cricketer)
            <li>{{ $index + 1 }}. {{ $cricketer }}</li>
        @endforeach
    </ol>
    
    <h2>Total Cricketers: {{ count($cricketers) }}</h2>
</body>
</html>
```

**Test:** Visit `http://localhost:8000/cricketers`

---

### ✅ Question 2.2: Associative array (name, age, email) in Blade

**Route:**
```php
Route::get('/user-info', function() {
    $user = [
        'name' => 'Ravi Ranjan',
        'age' => 22,
        'email' => 'ravi@example.com'
    ];
    
    return view('userinfo', compact('user'));
});
```

**View:** `resources/views/userinfo.blade.php`
```blade
<!DOCTYPE html>
<html>
<head>
    <title>User Information</title>
</head>
<body>
    <h1>User Profile</h1>
    
    <table border="1" cellpadding="10">
        <tr>
            <th><strong>Name</strong></th>
            <td>{{ $user['name'] }}</td>
        </tr>
        <tr>
            <th><strong>Age</strong></th>
            <td>{{ $user['age'] }} years</td>
        </tr>
        <tr>
            <th><strong>Email</strong></th>
            <td>{{ $user['email'] }}</td>
        </tr>
    </table>
</body>
</html>
```

---

### ✅ Question 2.3: Array of products with @foreach

**Route:**
```php
Route::get('/products', function() {
    $products = [
        ['name' => 'Laptop', 'price' => 50000, 'brand' => 'Dell'],
        ['name' => 'Mobile', 'price' => 20000, 'brand' => 'Samsung'],
        ['name' => 'Tablet', 'price' => 30000, 'brand' => 'Apple'],
        ['name' => 'Watch', 'price' => 15000, 'brand' => 'Fossil'],
    ];
    
    return view('products', compact('products'));
});
```

**View:** `resources/views/products.blade.php`
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Products</title>
</head>
<body>
    <h1>Product List</h1>
    
    <table border="1" cellpadding="10">
        <thead>
            <tr>
                <th>S.No</th>
                <th>Product Name</th>
                <th>Price</th>
                <th>Brand</th>
            </tr>
        </thead>
        <tbody>
            @foreach($products as $index => $product)
                <tr>
                    <td>{{ $index + 1 }}</td>
                    <td>{{ $product['name'] }}</td>
                    <td>₹{{ number_format($product['price']) }}</td>
                    <td>{{ $product['brand'] }}</td>
                </tr>
            @endforeach
        </tbody>
    </table>
    
    <h3>Total Products: {{ count($products) }}</h3>
</body>
</html>
```

---

### ✅ Question 2.4: Sort array and display sorted values

**Route:**
```php
Route::get('/cities-sorted', function() {
    $cities = ['Delhi', 'Mumbai', 'Bangalore', 'Chennai', 'Kolkata'];
    
    // Sort alphabetically
    $sortedCities = $cities;
    sort($sortedCities);
    
    return view('cities', compact('cities', 'sortedCities'));
});
```

**View:** `resources/views/cities.blade.php`
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Cities</title>
</head>
<body>
    <h1>Cities List</h1>
    
    <h2>Original Order:</h2>
    <p>{{ implode(', ', $cities) }}</p>
    
    <h2>Sorted Order:</h2>
    <p>{{ implode(', ', $sortedCities) }}</p>
</body>
</html>
```

---

### ✅ Question 2.5: Pass array and count elements

**Route:**
```php
Route::get('/count-items', function() {
    $items = ['Apple', 'Banana', 'Orange', 'Grapes', 'Mango', 'Pineapple'];
    
    return view('count', compact('items'));
});
```

**View:** `resources/views/count.blade.php`
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Count Items</title>
</head>
<body>
    <h1>Items List</h1>
    
    <ul>
        @foreach($items as $item)
            <li>{{ $item }}</li>
        @endforeach
    </ul>
    
    <h2>Total Items: {{ count($items) }}</h2>
    <p>Array has {{ sizeof($items) }} elements</p>
</body>
</html>
```

---

## 3. Routing + URL Traversing + Constraints

### ✅ Question 3.1: Route /product/electronics/123 with numeric constraint

```php
Route::get('/product/{category}/{id}', function($category, $id) {
    return "Category: {$category}, Product ID: {$id}";
})->where('id', '[0-9]+');
```

**Test URLs:**
- ✅ `http://localhost:8000/product/electronics/123` → Works
- ❌ `http://localhost:8000/product/electronics/abc` → 404 Error (not numeric)

---

### ✅ Question 3.2: Route /category/{type}/{id} with constraints

```php
Route::get('/category/{type}/{id}', function($type, $id) {
    return "Type: {$type}, ID: {$id}";
})->where(['type' => '[A-Za-z]+', 'id' => '[0-9]+']);
```

**Test:**
- ✅ `/category/books/100` → Works
- ❌ `/category/123/books` → Fails (type must be alphabets)

---

### ✅ Question 3.3: Route /a/b/{section}/{code} with fixed constraints

```php
Route::get('/a/b/{section}/{code}', function($section, $code) {
    return "Section: {$section}, Code: {$code}";
})->where(['section' => 'testing', 'code' => '[0-9]+']);
```

**Test:**
- ✅ `/a/b/testing/888` → Works
- ❌ `/a/b/production/888` → Fails (section must be "testing")
- ❌ `/a/b/testing/abc` → Fails (code must be numeric)

---

### ✅ Question 3.4: Optional parameter with default value

```php
Route::get('/greet/{name?}', function($name = 'Guest') {
    return "Hello, {$name}!";
});
```

**Test:**
- `/greet/John` → "Hello, John!"
- `/greet` → "Hello, Guest!" (default value)

---

### ✅ Question 3.5: Slug constraint [a-z-]+

```php
Route::get('/post/{slug}', function($slug) {
    return "Post Slug: {$slug}";
})->where('slug', '[a-z-]+');
```

**Test:**
- ✅ `/post/my-first-post` → Works
- ❌ `/post/My-Post-123` → Fails (uppercase and numbers not allowed)

---

## 4. Fallback + Group Routing

### ✅ Question 4.1: Fallback route with group routing and reverse name

**File:** `routes/web.php`

```php
use Illuminate\Support\Facades\Route;

// Group routing without prefix
Route::middleware(['web'])->group(function () {
    
    Route::get('/user/{name}/{country}', function($name, $country) {
        return "Welcome {$name} from {$country}!";
    });
    
    Route::get('/about', function() {
        return "About Us Page";
    });
    
    Route::get('/contact', function() {
        return "Contact Us Page";
    });
    
    // Reverse string outside fallback
    Route::get('/reverse/{text}', function($text) {
        return strrev($text);
    });
    
});

// Fallback route (MUST be at the end)
Route::fallback(function() {
    return "Page Not Found - 404";
});
```

**Testing:**
- `/user/John/USA` → "Welcome John from USA!"
- `/about` → "About Us Page"
- `/reverse/Raviranjan` → "najnarivaR"
- `/any-random-url` → "Page Not Found - 404"

---

### ✅ Question 4.2: Group with middleware and fallback inside

```php
Route::middleware(['auth'])->group(function () {
    
    Route::get('/dashboard', function() {
        return "Dashboard";
    });
    
    Route::get('/profile', function() {
        return "Profile";
    });
    
    // This fallback only applies to routes within this group
    Route::fallback(function() {
        return "Dashboard page not found";
    });
    
});
```

---

### ✅ Question 4.3: Custom 404 page using fallback

**Route:**
```php
Route::fallback(function() {
    return view('errors.404');
});
```

**View:** `resources/views/errors/404.blade.php`
```blade
<!DOCTYPE html>
<html>
<head>
    <title>404 - Page Not Found</title>
</head>
<body>
    <h1>404 - Page Not Found</h1>
    <p>Sorry, the page you are looking for could not be found.</p>
    <a href="{{ url('/') }}">Go Home</a>
</body>
</html>
```

---

## 5. Middleware Logic

### ✅ Question 5.1: Middleware accepting name and country

**Step 1:** Create middleware
```bash
php artisan make:middleware CheckUserDetails
```

**Step 2:** Edit middleware
**File:** `app/Http/Middleware/CheckUserDetails.php`

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckUserDetails
{
    public function handle(Request $request, Closure $next)
    {
        $name = $request->input('name');
        $country = $request->input('country');
        
        // Check if name or country is missing
        if (empty($name) || empty($country)) {
            return response('Forbidden, Middleware, Middleware activated', 403);
        }
        
        // Both are present
        return response('Hi you have successfully login');
    }
}
```

**Step 3:** Register middleware
**File:** `app/Http/Kernel.php`

```php
protected $routeMiddleware = [
    // ... other middleware
    'check.user' => \App\Http\Middleware\CheckUserDetails::class,
];
```

**Step 4:** Create routes
**File:** `routes/web.php`

```php
// Middleware route
Route::get('/user/check', function() {
    return "This will not be reached";
})->middleware('check.user');

// Alternative: Without middleware, check in closure
Route::get('/user/verify', function(\Illuminate\Http\Request $request) {
    $name = $request->input('name');
    $country = $request->input('country');
    
    if (empty($name) || empty($country)) {
        return response('Forbidden, Middleware, Middleware activated', 403);
    }
    
    return response('Hi you have successfully login');
});
```

**Testing:**
- ❌ `/user/verify?name=John` → "Forbidden, Middleware, Middleware activated"
- ❌ `/user/verify?country=USA` → "Forbidden, Middleware, Middleware activated"
- ✅ `/user/verify?name=John&country=USA` → "Hi you have successfully login"

---

### ✅ Question 5.2: Role-based middleware

**Create:** `php artisan make:middleware CheckRole`

**File:** `app/Http/Middleware/CheckRole.php`
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckRole
{
    public function handle(Request $request, Closure $next)
    {
        $role = $request->input('role');
        
        if ($role !== 'admin') {
            return response('Access Denied', 403);
        }
        
        return $next($request);
    }
}
```

**Route:**
```php
Route::get('/admin/dashboard', function() {
    return "Welcome to Admin Dashboard";
})->middleware('check.role');
```

**Test:**
- ❌ `/admin/dashboard?role=user` → "Access Denied"
- ✅ `/admin/dashboard?role=admin` → "Welcome to Admin Dashboard"

---

### ✅ Question 5.3: Age validation middleware

```php
Route::get('/enter-site', function(\Illuminate\Http\Request $request) {
    $age = $request->input('age');
    
    if ($age < 18) {
        return response('Access Denied - You must be 18+', 403);
    }
    
    return response('Welcome to the site!');
});
```

---

## 6. Controller + Constraints

### ✅ Question 6.1: Controller with num(), Alphanum(), uid() methods

**Step 1:** Create controller
```bash
php artisan make:controller ConstraintController
```

**Step 2:** Edit controller
**File:** `app/Http/Controllers/ConstraintController.php`

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class ConstraintController extends Controller
{
    // Method 1: Numeric constraint
    public function num($id)
    {
        return "Numeric ID: {$id}";
    }
    
    // Method 2: Alphanumeric constraint
    public function Alphanum($value)
    {
        return "Alphanumeric Value: {$value}";
    }
    
    // Method 3: Fixed length constraint (UID = 6 digits)
    public function uid($uid)
    {
        return "User UID: {$uid}";
    }
}
```

**Step 3:** Define routes with constraints
**File:** `routes/web.php`

```php
use App\Http\Controllers\ConstraintController;
use Illuminate\Support\Facades\Route;

// Route 1: Numeric only
Route::get('/num/{id}', [ConstraintController::class, 'num'])
    ->where('id', '[0-9]+');

// Route 2: Alphanumeric (letters + numbers)
Route::get('/alphanum/{value}', [ConstraintController::class, 'Alphanum'])
    ->where('value', '[A-Za-z0-9]+');

// Route 3: Fixed length (exactly 6 digits)
Route::get('/uid/{uid}', [ConstraintController::class, 'uid'])
    ->where('uid', '[0-9]{6}');
```

**Testing:**

**For num():**
- ✅ `/num/123` → "Numeric ID: 123"
- ❌ `/num/abc` → 404 Error

**For Alphanum():**
- ✅ `/alphanum/abc123` → "Alphanumeric Value: abc123"
- ✅ `/alphanum/ABC` → Works
- ❌ `/alphanum/abc@123` → 404 Error (special chars not allowed)

**For uid():**
- ✅ `/uid/123456` → "User UID: 123456"
- ❌ `/uid/12345` → 404 Error (only 5 digits)
- ❌ `/uid/1234567` → 404 Error (7 digits)

---

### ✅ Question 6.2: Email format validation via route

```php
Route::get('/email/{email}', function($email) {
    return "Email: {$email}";
})->where('email', '[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}');
```

---

### ✅ Question 6.3: Multiple parameter validation in controller

```php
// Controller
public function showProduct($category, $id, $slug)
{
    return "Category: {$category}, ID: {$id}, Slug: {$slug}";
}

// Route with multiple constraints
Route::get('/product/{category}/{id}/{slug}', [ProductController::class, 'showProduct'])
    ->where([
        'category' => '[a-z]+',
        'id' => '[0-9]+',
        'slug' => '[a-z0-9-]+'
    ]);
```

---

## 7. Form + Validation (VERY IMPORTANT)

### ✅ Question 7.1: Registration form with validation

**Step 1:** Create routes
```php
// Show form
Route::get('/register', function() {
    return view('register');
});

// Process form
Route::post('/register', function(\Illuminate\Http\Request $request) {
    $validated = $request->validate([
        'name' => 'required|string|max:255',
        'email' => 'required|email|unique:users',
        'password' => 'required|min:8|confirmed',
    ], [
        'name.required' => 'Name is required',
        'email.required' => 'Email is required',
        'email.email' => 'Please enter a valid email',
        'email.unique' => 'Email already exists',
        'password.required' => 'Password is required',
        'password.min' => 'Password must be at least 8 characters',
        'password.confirmed' => 'Password confirmation does not match',
    ]);
    
    return redirect('/login')->with('success', 'Registration successful!');
});
```

**Step 2:** Create view
**File:** `resources/views/register.blade.php`

```blade
<!DOCTYPE html>
<html>
<head>
    <title>Registration</title>
</head>
<body>
    <h1>Register</h1>
    
    @if($errors->any())
        <div style="color: red;">
            <ul>
                @foreach($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        </div>
    @endif
    
    <form method="POST" action="/register">
        @csrf
        
        <div>
            <label>Name:</label>
            <input type="text" name="name" value="{{ old('name') }}">
        </div>
        
        <div>
            <label>Email:</label>
            <input type="email" name="email" value="{{ old('email') }}">
        </div>
        
        <div>
            <label>Password:</label>
            <input type="password" name="password">
        </div>
        
        <div>
            <label>Confirm Password:</label>
            <input type="password" name="password_confirmation">
        </div>
        
        <button type="submit">Register</button>
    </form>
</body>
</html>
```

---

### ✅ Question 7.2: Login form validation

```php
Route::get('/login', function() {
    return view('login');
});

Route::post('/login', function(\Illuminate\Http\Request $request) {
    $validated = $request->validate([
        'email' => 'required|email',
        'password' => 'required|min:6',
    ]);
    
    return "Login successful!";
});
```

---

### ✅ Question 7.3: Display all errors using $errors

```blade
@if($errors->any())
    <div style="background: #ffe6e6; padding: 15px; border: 1px solid red;">
        <h3>Validation Errors:</h3>
        <ul>
            @foreach($errors->all() as $error)
                <li style="color: red;">{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

---

## 8. Redirect + Flash Message

### ✅ Question 8.1: Redirect with success message

```php
Route::post('/submit-form', function(\Illuminate\Http\Request $request) {
    // Process form
    
    return redirect('/success')
        ->with('success', 'Form submitted successfully!');
});

Route::get('/success', function() {
    $message = session('success');
    return "Status: {$message}";
});
```

---

### ✅ Question 8.2: Redirect back with input

```php
Route::post('/register', function(\Illuminate\Http\Request $request) {
    $validated = $request->validate([
        'email' => 'required|email',
    ]);
    
    // If validation fails, redirects back with old input
    return redirect('/dashboard');
})->name('register');
```

---

### ✅ Question 8.3: Redirect to named route

```php
Route::get('/profile', function() {
    return "Profile Page";
})->name('profile');

Route::get('/go-to-profile', function() {
    return redirect()->route('profile');
});
```

---

### ✅ Question 8.4: Flash multiple messages

```php
Route::post('/update', function(\Illuminate\Http\Request $request) {
    return redirect('/dashboard')
        ->with('success', 'Profile updated!')
        ->with('info', 'Please verify your email')
        ->with('warning', 'Complete your profile');
});
```

---

## 9. Response Types

### ✅ Question 9.1: Return JSON response

```php
Route::get('/api/user', function() {
    return response()->json([
        'status' => 'success',
        'data' => [
            'id' => 1,
            'name' => 'John Doe',
            'email' => 'john@example.com'
        ]
    ], 200);
});
```

---

### ✅ Question 9.2: Return file download

```php
Route::get('/download/report', function() {
    return response()->download(storage_path('app/report.pdf'));
});
```

---

### ✅ Question 9.3: Return response with headers

```php
Route::get('/api/data', function() {
    return response()->json(['data' => 'value'])
        ->header('Content-Type', 'application/json')
        ->header('X-API-Key', 'secret-key-123')
        ->header('Cache-Control', 'max-age=3600');
});
```

---

### ✅ Question 9.4: Return custom status code

```php
Route::get('/not-found', function() {
    return response()->json([
        'error' => 'Resource not found'
    ], 404);
});

Route::get('/server-error', function() {
    return response()->json([
        'error' => 'Internal Server Error'
    ], 500);
});
```

---

### ✅ Question 9.5: Return response with cookie

```php
Route::get('/set-data', function() {
    return response('Cookie set!')
        ->cookie('user_preference', 'dark_mode', 60)
        ->header('X-Custom', 'Value');
});
```

---

## 10. Mixed Logic Questions

### ✅ Question 10.1: Route → pass array → validate → redirect

```php
// Route with array
Route::get('/students', function() {
    $students = ['John', 'Jane', 'Bob'];
    
    return view('students', compact('students'));
});

// Validation and redirect
Route::post('/students/add', function(\Illuminate\Http\Request $request) {
    $validated = $request->validate([
        'name' => 'required|string|max:255',
    ]);
    
    return redirect('/students')
        ->with('success', 'Student added: ' . $validated['name']);
});
```

---

### ✅ Question 10.2: Middleware + route constraint combined

```php
// Middleware
Route::get('/secure/{id}', function($id) {
    return "Secure ID: {$id}";
})->middleware('auth')->where('id', '[0-9]+');
```

---

### ✅ Question 10.3: Controller + view + validation + redirect

```php
// Controller
class PostController extends Controller
{
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
}

// Routes
Route::get('/posts/create', [PostController::class, 'create']);
Route::post('/posts', [PostController::class, 'store'])->name('posts.store');
Route::get('/posts', [PostController::class, 'index'])->name('posts.index');
```

---

## Specific Exam Questions Solutions

### 🎯 Q1: Create cookie "crucial-data", get value, delete it

**Complete Solution:**

**File:** `routes/web.php`
```php
use Illuminate\Support\Facades\Route;
use Illuminate\Support\Facades\Cookie;

// Create cookie
Route::get('/cookie/create-crucial', function() {
    return response('Cookie "crucial-data" created')
        ->cookie('crucial-data', 'ImportantValue123', 60);
});

// Get cookie value on browser
Route::get('/cookie/get-crucial', function(\Illuminate\Http\Request $request) {
    $value = $request->cookie('crucial-data');
    
    if ($value) {
        return "Cookie 'crucial-data' Value: " . $value;
    }
    
    return "Cookie 'crucial-data' not found!";
});

// Delete cookie
Route::get('/cookie/delete-crucial', function() {
    return response('Cookie "crucial-data" deleted')
        ->cookie(Cookie::forget('crucial-data'));
});
```

**Testing Steps:**
1. Visit `/cookie/create-crucial` → Cookie created
2. Visit `/cookie/get-crucial` → Shows "ImportantValue123"
3. Visit `/cookie/delete-crucial` → Cookie deleted
4. Visit `/cookie/get-crucial` again → Shows "not found"

---

### 🎯 Q2: Array of 10 cricketers with compact() and implode()

**Route:**
```php
Route::get('/cricketers', function() {
    $cricketers = [
        'Virat Kohli',
        'Rohit Sharma',
        'MS Dhoni',
        'Sachin Tendulkar',
        'KL Rahul',
        'Hardik Pandya',
        'Jasprit Bumrah',
        'Ravindra Jadeja',
        'Shikhar Dhawan',
        'Rishabh Pant'
    ];
    
    return view('field', compact('cricketers'));
});
```

**View:** `resources/views/field.blade.php`
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Cricketers</title>
</head>
<body>
    <h1>Top 10 Cricketers</h1>
    
    <h2>Using implode():</h2>
    <p>{{ implode(', ', $cricketers) }}</p>
    
    <ol>
        @foreach($cricketers as $cricketer)
            <li>{{ $cricketer }}</li>
        @endforeach
    </ol>
</body>
</html>
```

---

### 🎯 Q3: URL traversal a/b/c/testing/888 with fixed constraints

**Route:**
```php
Route::get('/a/b/c/{section}/{code}', function($section, $code) {
    return "Section: {$section}, Code: {$code}";
})->where(['section' => 'testing', 'code' => '[0-9]+']);
```

**Test:**
- ✅ `/a/b/c/testing/888` → Works
- ❌ `/a/b/c/production/888` → Fails
- ❌ `/a/b/c/testing/abc` → Fails

---

### 🎯 Q4: Fallback routing with group routing and reverse name

**Route:**
```php
use Illuminate\Support\Facades\Route;

// Group routing
Route::middleware(['web'])->group(function() {
    
    Route::get('/user', function() {
        return "User Page";
    });
    
    Route::get('/reverse/{text}', function($text) {
        return strrev($text);
    });
    
    // More routes here...
    
});

// Fallback (MUST be last)
Route::fallback(function() {
    return "Page Not Found - 404";
});
```

**Reverse name example:**
```php
Route::get('/reverse-name', function() {
    $name = "Raviranjan";
    return "Original: {$name}, Reversed: " . strrev($name);
});
```

---

### 🎯 Q5: Middleware with name and country validation

**File:** `routes/web.php`

```php
Route::get('/middleware-check', function(\Illuminate\Http\Request $request) {
    $name = $request->input('name');
    $country = $request->input('country');
    
    if (empty($name) || empty($country)) {
        return response('Forbidden, Middleware, Middleware activated', 403);
    }
    
    return response('Hi you have successfully login');
});
```

**Testing:**
- ❌ `/middleware-check?name=John` → "Forbidden, Middleware, Middleware activated"
- ❌ `/middleware-check?country=USA` → "Forbidden, Middleware, Middleware activated"
- ✅ `/middleware-check?name=John&country=USA` → "Hi you have successfully login"

---

### 🎯 Q6: Controller with num(), Alphanum(), uid() methods

**Controller:** `app/Http/Controllers/ConstraintController.php`
```php
<?php

namespace App\Http\Controllers;

class ConstraintController extends Controller
{
    public function num($id)
    {
        return "Numeric ID: {$id}";
    }
    
    public function Alphanum($value)
    {
        return "Alphanumeric: {$value}";
    }
    
    public function uid($uid)
    {
        return "User UID: {$uid}";
    }
}
```

**Routes:**
```php
use App\Http\Controllers\ConstraintController;

// Numeric constraint
Route::get('/num/{id}', [ConstraintController::class, 'num'])
    ->where('id', '[0-9]+');

// Alphanumeric constraint
Route::get('/alphanum/{value}', [ConstraintController::class, 'Alphanum'])
    ->where('value', '[A-Za-z0-9]+');

// Fixed length constraint (6 digits)
Route::get('/uid/{uid}', [ConstraintController::class, 'uid'])
    ->where('uid', '[0-9]{6}');
```

---

## 📊 Quick Reference Table

| Question Type | Key Concepts | Important Functions |
|---------------|--------------|---------------------|
| Cookie | Create, Get, Delete | `Cookie::make()`, `request()->cookie()`, `Cookie::forget()` |
| Session | Store, Retrieve | `session()`, `session(['key' => 'value'])` |
| Array + View | compact(), implode() | `compact()`, `implode()`, `count()` |
| Routing | Parameters, Constraints | `->where()`, `{param}`, `{param?}` |
| Fallback | Catch-all route | `Route::fallback()` |
| Middleware | Validation, Access Control | `handle()`, `$request->input()` |
| Controller | Methods, Constraints | `where()`, regex patterns |
| Validation | Form validation | `$request->validate()`, `$errors` |
| Redirect | Flash messages | `redirect()->with()`, `back()` |
| Response | JSON, Headers, Cookies | `response()->json()`, `->header()`, `->cookie()` |

---

## 🎯 Exam Tips

### ✅ Do's:
- Always place `Route::fallback()` at the END
- Test constraints with valid and invalid URLs
- Use `compact()` for passing multiple variables
- Always add `@csrf` in forms
- Return proper status codes (200, 404, 500)

### ❌ Don'ts:
- Don't put fallback route before other routes
- Don't forget to register middleware in Kernel.php
- Don't hardcode values when using arrays
- Don't skip validation in forms
- Don't forget to test all edge cases

### 🔥 Hot Topics for Exam:
1. Cookie creation and deletion (90% chance)
2. Array + compact + implode (85% chance)
3. Route constraints (80% chance)
4. Fallback routing (75% chance)
5. Middleware logic (70% chance)
6. Form validation (95% chance)
7. Controller with constraints (75% chance)

---

**Ready to ace your practical exam! 🚀**

---

**Back to:** [Main Study Guide](../STUDY_GUIDE.md) | [Exam Preparation](../Exam_Preparation/README.md)
