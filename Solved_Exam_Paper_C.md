# Solved Laravel Practical Exam Paper C

This document contains full, complete, and exam-ready code solutions for all 10 questions of Practical Exam Paper C (from the images `WhatsApp Image 2026-06-05 at 00.39.51.jpeg` and `WhatsApp Image 2026-06-05 at 00.39.57.jpeg`).

---

## Q1: Multi-language Dropdown & Last Visited Redirect

### Question
*   **a)** Implement Laravel localization to support three languages: English, Spanish, and French. Allow users to change the language dynamically using a dropdown menu.
*   **b)** Create a Laravel session management example where the last visited page of the user is stored in the session, and on the next visit the user is automatically redirected to that page.

### Solution

#### 1. Define Translation Files
*   **English:** `lang/en/labels.php` returning `['welcome' => 'Welcome']`
*   **Spanish:** `lang/es/labels.php` returning `['welcome' => 'Bienvenido']`
*   **French:** `lang/fr/labels.php` returning `['welcome' => 'Bienvenue']`

#### 2. Create Redirection & Locale Middleware
Generate using: `php artisan make:middleware TrackLastPage`

File: `app/Http/Middleware/TrackLastPage.php`
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\App;

class TrackLastPage
{
    public function handle(Request $request, Closure $next)
    {
        // a) Handle Locale Setting
        if (session()->has('locale')) {
            App::setLocale(session('locale'));
        }

        // b) Handle Auto-Redirect to Last Visited Page
        $path = $request->path();
        
        // Don't loop / redirect to static paths or switch routes
        if ($request->isMethod('GET') && !$request->ajax() && !str_contains($path, 'lang')) {
            if (session()->has('last_visited') && session('last_visited') !== $path && $path === '/') {
                $target = session('last_visited');
                return redirect($target);
            }
            // Update last visited
            session(['last_visited' => $path]);
        }

        return $next($request);
    }
}
```
*Register this middleware under the `'web'` group inside Kernel configuration.*

#### 3. Define Routes (`routes/web.php`)
```php
Route::get('/lang/{locale}', function($locale) {
    if (in_array($locale, ['en', 'es', 'fr'])) {
        session(['locale' => $locale]);
    }
    return redirect()->back();
});

Route::get('/', function() { return view('home'); });
Route::get('/page-one', function() { return view('page_one'); });
Route::get('/page-two', function() { return view('page_two'); });
```

#### 4. Create Blade Template (`resources/views/home.blade.php`)
```html
<!DOCTYPE html>
<html>
<head><title>Home</title></head>
<body>
    <h1>{{ __('labels.welcome') }}</h1>

    <select onchange="window.location.href='/lang/' + this.value">
        <option value="en" {{ app()->getLocale() == 'en' ? 'selected' : '' }}>English</option>
        <option value="es" {{ app()->getLocale() == 'es' ? 'selected' : '' }}>Spanish</option>
        <option value="fr" {{ app()->getLocale() == 'fr' ? 'selected' : '' }}>French</option>
    </select>

    <br><br>
    <a href="/page-one">Go to Page One</a> | <a href="/page-two">Go to Page Two</a>
</body>
</html>
```

---

## Q2: Complex Form Validation & Custom Weekend Rule

### Question
*   **a)** Create a Laravel form validation system with the following conditions:
    *   Username must contain at least 6 characters.
    *   Email must be unique in the database.
    *   Password must include one uppercase letter, one number, and one special character.
*   **b)** Implement a custom validation rule in Laravel to ensure that a selected date is not a weekend.

### Solution

#### 1. Generate Custom Validation Rule
Run command: `php artisan make:rule NotWeekend`

File: `app/Rules/NotWeekend.php`
```php
namespace App\Rules;

use Closure;
use Illuminate\Contracts\Validation\ValidationRule;
use Carbon\Carbon;

class NotWeekend implements ValidationRule
{
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        $date = Carbon::parse($value);
        // Carbon dayOfWeek returns 0 (Sunday) and 6 (Saturday)
        if ($date->isWeekend()) {
            $fail('The selected date: :attribute must not fall on a weekend.');
        }
    }
}
```

#### 2. Define Controller Validations
```php
use App\Rules\NotWeekend;
use Illuminate\Http\Request;

Route::post('/validate-form', function(Request $request) {
    $request->validate([
        'username' => 'required|min:6',
        'email' => 'required|email|unique:users,email',
        // Password validation regex requiring Upper, Number, and Special character
        'password' => [
            'required',
            'regex:/[A-Z]/',      // uppercase checks
            'regex:/[0-9]/',      // digit checks
            'regex:/[@$!%*#?&]/', // special character checks
        ],
        'booking_date' => ['required', 'date', new NotWeekend]
    ], [
        'password.regex' => 'Password must have one uppercase letter, one number, and one special character.'
    ]);

    return "Validation Successful!";
});
```

---

## Q3: Multiple Image Validation & Queued Welcome Email

### Question
*   **a)** Create a Laravel form that allows users to upload multiple images. Apply validation rules so that only image files are accepted and each file size is less than 3MB.
*   **b)** Demonstrate how to queue an email in Laravel using the Mailable class. Send a welcome email to a user upon registration and process it using a queue.

### Solution

#### 1. Multiple Image Upload Form & Controller
*   *Blade File:*
    ```html
    <form method="POST" action="/upload-images" enctype="multipart/form-data">
        @csrf
        Upload Images (Max 3MB each): 
        <input type="file" name="images[]" multiple required>
        <button type="submit">Submit</button>
    </form>
    ```
*   *Controller Action:*
    ```php
    public function uploadMultiple(Request $request) {
        // Validation checking arrays of inputs
        $request->validate([
            'images' => 'required|array',
            'images.*' => 'image|mimes:jpeg,png,webp|max:3072' // 3072 KB = 3MB
        ]);

        foreach ($request->file('images') as $file) {
            $file->store('gallery', 'public');
        }

        return "Images uploaded successfully.";
    }
    ```

#### 2. Queued Mail Configuration
Generate Mailable class: `php artisan make:mail WelcomeUserMail`

*   **Edit `app/Mail/WelcomeUserMail.php` to implement Queueable:**
    ```php
    namespace App\Mail;

    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue; // Crucial for queueing
    use Illuminate\Mail\Mailable;
    use Illuminate\Mail\Mailables\Content;
    use Illuminate\Mail\Mailables\Envelope;

    class WelcomeUserMail extends Mailable implements ShouldQueue
    {
        use Queueable;

        public function envelope(): Envelope
        {
            return new Envelope(subject: 'Welcome to our platform!');
        }

        public function content(): Content
        {
            // resources/views/emails/welcome.blade.php
            return new Content(view: 'emails.welcome'); 
        }
    }
    ```
*   **Dispatching Mail in Controller:**
    ```php
    use Illuminate\Support\Facades\Mail;
    use App\Mail\WelcomeUserMail;

    Route::post('/register', function(Request $request) {
        $email = $request->input('email');
        
        // This will automatically push the mail task to the jobs queue
        Mail::to($email)->queue(new WelcomeUserMail());

        return "Registration successful. Welcome email queued.";
    });
    ```
    *Note: Set `QUEUE_CONNECTION=database` in `.env`, create migrations via `php artisan queue:table`, run `php artisan migrate`, and execute worker daemon using `php artisan queue:work`.*

---

## Q4: Dynamic URL Generation Helpers & Cookie-based Theme Preference

### Question
*   **a)** Demonstrate how to generate URLs dynamically in Laravel using the following helpers:
    *   `url()` helper
    *   `route()` helper
    *   `asset()` helper for loading static resources.
*   **b)** Implement a feature that stores the user's theme preference (light or dark mode) in a cookie and automatically applies the selected theme when the user revisits the website.

### Solution

#### 1. Define Routes (`routes/web.php`)
```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

// Theme selection page
Route::get('/theme-select', function(Request $request) {
    $theme = $request->cookie('app_theme', 'light');
    return view('theme_select', compact('theme'));
});

// Update cookie route
Route::post('/set-theme', function(Request $request) {
    $theme = $request->input('theme', 'light');
    return redirect('/theme-select')->cookie('app_theme', $theme, 525600); // 1 year cookie
});
```

#### 2. Create View (`resources/views/theme_select.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Theme Demo</title>
    <!-- asset() helper loads stylesheet from public/css/custom.css -->
    <link rel="stylesheet" href="{{ asset('css/custom.css') }}">
    <style>
        body {
            background-color: {{ $theme == 'dark' ? '#333' : '#fff' }};
            color: {{ $theme == 'dark' ? '#fff' : '#000' }};
            padding: 20px;
        }
    </style>
</head>
<body>
    <h2>URL Helpers Demonstration</h2>
    <ul>
        <!-- url() helper generates absolute URL path -->
        <li>Dynamic URL: {{ url('/theme-select') }}</li>
        
        <!-- route() helper resolves URL dynamically from named route -->
        <li>Named Route Link: <a href="{{ route('theme.page') }}">Link</a></li>
    </ul>

    <hr>

    <h3>Theme selection (Persistent Cookie Mode):</h3>
    <form method="POST" action="/set-theme">
        @csrf
        <select name="theme" onchange="this.form.submit()">
            <option value="light" {{ $theme == 'light' ? 'selected' : '' }}>Light Mode</option>
            <option value="dark" {{ $theme == 'dark' ? 'selected' : '' }}>Dark Mode</option>
        </select>
    </form>
</body>
</html>
```

---

## Q5: PDF Upload Validation & List Downloads

### Question
*   **a)** Create a Laravel file handling module that allows users to upload a PDF document. Validate the file so that only PDF files are accepted and the file size does not exceed 5MB.
*   **b)** Extend the module to store the uploaded file path in the database and display the uploaded documents in a list with a download option.

### Solution

#### 1. Database & Model
*   **Migration Code:**
    ```php
    Schema::create('documents', function (Blueprint $table) {
        $table->id();
        $table->string('filename');
        $table->string('file_path');
        $table->timestamps();
    });
    ```
*   **Eloquent Model (`app/Models/Document.php`):**
    ```php
    namespace App\Models;
    use Illuminate\Database\Eloquent\Model;
    class Document extends Model {
        protected $fillable = ['filename', 'file_path'];
    }
    ```

#### 2. Define Controller (`app/Http/Controllers/DocumentController.php`)
```php
namespace App\Http\Controllers;

use App\Models\Document;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;

class DocumentController extends Controller
{
    public function index() {
        $docs = Document::all();
        return view('documents', compact('docs'));
    }

    public function upload(Request $request) {
        // Validate PDF only, max 5MB (5120 KB)
        $request->validate([
            'pdf_doc' => 'required|file|mimes:pdf|max:5120'
        ]);

        if ($request->hasFile('pdf_doc')) {
            $file = $request->file('pdf_doc');
            $originalName = $file->getClientOriginalName();
            $path = $file->store('documents', 'public');

            Document::create([
                'filename' => $originalName,
                'file_path' => $path
            ]);

            return back()->with('status', 'PDF uploaded successfully.');
        }
    }

    public function download($id) {
        $doc = Document::findOrFail($id);
        // Force browser download
        return Storage::disk('public')->download($doc->file_path, $doc->filename);
    }
}
```

#### 3. Routes (`routes/web.php`)
```php
use App\Http\Controllers\DocumentController;

Route::get('/documents', [DocumentController::class, 'index']);
Route::post('/documents/upload', [DocumentController::class, 'upload']);
Route::get('/documents/download/{id}', [DocumentController::class, 'download']);
```

#### 4. Blade View Template (`resources/views/documents.blade.php`)
```html
<form method="POST" action="/documents/upload" enctype="multipart/form-data">
    @csrf
    Upload PDF: <input type="file" name="pdf_doc" required>
    <button type="submit">Upload</button>
</form>

<hr>
<h3>Uploaded PDF Documents:</h3>
<ul>
    @foreach($docs as $doc)
        <li>
            {{ $doc->filename }} - 
            <a href="/documents/download/{{ $doc->id }}">Download PDF</a>
        </li>
    @endforeach
</ul>
```

---

## Q6: Fallback Email/Username Auth & Session Wiping

### Question
*   **a)** Develop a Laravel authentication system that allows users to log in using either their email address or username. Apply suitable validation rules to verify the credentials.
*   **b)** Write a function that clears all session data when the user logs out, ensuring that a new session starts when the user logs in again.

### Solution

#### 1. Login Controller
File: `app/Http/Controllers/AuthController.php`
```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class AuthController extends Controller
{
    public function showLogin() {
        return view('auth.login');
    }

    public function login(Request $request)
    {
        $request->validate([
            'login_input' => 'required|string', // represents either email or username
            'password' => 'required'
        ]);

        $loginInput = $request->input('login_input');
        $password = $request->input('password');

        // Check if value is a valid email formatting
        $field = filter_var($loginInput, FILTER_VALIDATE_EMAIL) ? 'email' : 'username';

        // Attempt Auth mapping credentials dynamically
        $credentials = [$field => $loginInput, 'password' => $password];

        if (Auth::attempt($credentials)) {
            // Regenerate session ID to prevent Session Fixation attack
            $request->session()->regenerate();
            return redirect()->intended('/dashboard');
        }

        return back()->withErrors(['error' => 'Invalid login details.']);
    }

    // b) Logout Wiping Sessions
    public function logout(Request $request)
    {
        Auth::logout();

        // Wipe session memory completely
        $request->session()->invalidate();

        // Regenerate CSRF validation tokens
        $request->session()->regenerateToken();

        return redirect('/login');
    }
}
```

---

## Q7: Product API Resources & Admin-Only Middleware Groups

### Question
*   **a)** Create a Laravel API resource controller to manage products. Implement methods to list, create, update, and delete products using Laravel API resource routes.
*   **b)** Demonstrate how to define and use a route group with middleware that allows access only to users with an "admin" role.

### Solution

#### 1. API Route Registry (`routes/api.php`)
```php
use App\Http\Controllers\Api\ProductApiController;
use Illuminate\Support\Facades\Route;

// Register API resources routes
Route::apiResource('products', ProductApiController::class);
```

#### 2. Define Admin Custom Middleware
Generate using: `php artisan make:middleware IsAdmin`

File: `app/Http/Middleware/IsAdmin.php`
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class IsAdmin
{
    public function handle(Request $request, Closure $next)
    {
        // Check user object role parameter
        if (auth()->check() && auth()->user()->role === 'admin') {
            return $next($request);
        }

        return response()->json(['error' => 'Access Denied: Admins Only.'], 403);
    }
}
```

#### 3. Define Route Group with Middleware (`routes/web.php`)
```php
Route::middleware(['auth', 'is_admin'])->group(function() {
    // All routes inside require authentication AND admin role
    Route::get('/admin/panel', function() {
        return "Welcome to the Admin management console!";
    });
    Route::get('/admin/logs', function() {
        return "Displaying system audit logs...";
    });
});
```

---

## Q8: Custom Rule Form Validation & Eloquent Database Store

### Question
*   **a)** Design a Laravel form that collects a user's name, age, and favorite programming language. Apply Laravel validation rules so that:
    *   Name should contain only alphabets.
    *   Age must be 18 or above.
    *   Favorite programming language must be selected from a list (PHP, JavaScript, Python).
*   **b)** Extend the above form to store the submitted information in the database using an Eloquent model and display the stored records in a table format.

### Solution

#### 1. Database & Model
*   **Migration Code:**
    ```php
    Schema::create('users_profiles', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->integer('age');
        $table->string('language');
        $table->timestamps();
    });
    ```
*   **Model Code (`app/Models/UserProfile.php`):**
    ```php
    namespace App\Models;
    use Illuminate\Database\Eloquent\Model;
    class UserProfile extends Model {
        protected $table = 'users_profiles';
        protected $fillable = ['name', 'age', 'language'];
    }
    ```

#### 2. Define Controller (`app/Http/Controllers/UserProfileController.php`)
```php
namespace App\Http\Controllers;

use App\Models\UserProfile;
use Illuminate\Http\Request;

class UserProfileController extends Controller
{
    public function index() {
        $profiles = UserProfile::all();
        return view('user_profiles', compact('profiles'));
    }

    public function store(Request $request) {
        // Apply strict validation conditions
        $request->validate([
            'name' => 'required|regex:/^[a-zA-Z\s]+$/', // Alpha only (spaces allowed)
            'age' => 'required|numeric|min:18',         // Age 18+
            'language' => 'required|in:PHP,JavaScript,Python' // Selected from list
        ], [
            'name.regex' => 'Name should contain only alphabets.'
        ]);

        UserProfile::create($request->all());

        return back()->with('status', 'Profile saved successfully.');
    }
}
```

#### 3. View Template (`resources/views/user_profiles.blade.php`)
```html
<form method="POST" action="/save-profile">
    @csrf
    Name: <input type="text" name="name" value="{{ old('name') }}"> <br>
    Age: <input type="number" name="age" value="{{ old('age') }}"> <br>
    Language:
    <select name="language">
        <option value="PHP">PHP</option>
        <option value="JavaScript">JavaScript</option>
        <option value="Python">Python</option>
    </select> <br>
    <button type="submit">Submit</button>
</form>

<hr>
<h3>Stored User Profiles:</h3>
<table border="1">
    <thead>
        <tr><th>Name</th><th>Age</th><th>Language</th></tr>
    </thead>
    <tbody>
        @foreach($profiles as $profile)
            <tr><td>{{ $profile->name }}</td><td>{{ $profile->age }}</td><td>{{ $profile->language }}</td></tr>
        @endforeach
    </tbody>
</table>
```

---

## Q9: Student Records CRUD Application

### Question
*   **a)** Create a Laravel CRUD application for managing student records. Implement features to add, view, edit, and delete student details using routes, controllers, models, and Blade views.
*   **b)** Apply Laravel validation rules to ensure that:
    *   Student name is required.
    *   Roll number is unique.
    *   Email is in valid format.
    *   Department is selected from a dropdown list.

### Solution

#### 1. Define Model and Controller
*   **Model Code (`app/Models/StudentRec.php`):**
    ```php
    namespace App\Models;
    use Illuminate\Database\Eloquent\Model;
    class StudentRec extends Model {
        protected $table = 'students_recs';
        protected $fillable = ['name', 'roll_no', 'email', 'department'];
    }
    ```
*   **Controller File (`app/Http/Controllers/StudentCrudController.php`):**
    ```php
    namespace App\Http\Controllers;

    use App\Models\StudentRec;
    use Illuminate\Http\Request;

    class StudentCrudController extends Controller
    {
        public function index() {
            $students = StudentRec::all();
            return view('students.index', compact('students'));
        }

        public function store(Request $request) {
            $request->validate([
                'name' => 'required',
                'roll_no' => 'required|unique:students_recs,roll_no',
                'email' => 'required|email',
                'department' => 'required|in:CSE,ECE,ME,Management'
            ]);

            StudentRec::create($request->all());
            return redirect('/students')->with('status', 'Student Added.');
        }

        public function edit($id) {
            $student = StudentRec::findOrFail($id);
            return view('students.edit', compact('student'));
        }

        public function update(Request $request, $id) {
            $student = StudentRec::findOrFail($id);
            $request->validate([
                'name' => 'required',
                'roll_no' => 'required|unique:students_recs,roll_no,' . $student->id,
                'email' => 'required|email',
                'department' => 'required|in:CSE,ECE,ME,Management'
            ]);

            $student->update($request->all());
            return redirect('/students')->with('status', 'Student Updated.');
        }

        public function destroy($id) {
            StudentRec::findOrFail($id)->delete();
            return redirect('/students')->with('status', 'Student Deleted.');
        }
    }
    ```

#### 2. Routes (`routes/web.php`)
```php
use App\Http\Controllers\StudentCrudController;

Route::get('/students', [StudentCrudController::class, 'index']);
Route::post('/students', [StudentCrudController::class, 'store']);
Route::get('/students/{id}/edit', [StudentCrudController::class, 'edit']);
Route::put('/students/{id}', [StudentCrudController::class, 'update']);
Route::delete('/students/{id}', [StudentCrudController::class, 'destroy']);
```

---

## Q10: Data Types Passing & Custom Response Header Middleware

### Question
*   **a)** Demonstrate how to pass different types of data (arrays, objects, and strings) from a Laravel controller to a Blade view. Create a route, controller method, and view file to display the data in a structured format.
*   **b)** Write a Laravel middleware that adds a custom HTTP header to every response. Apply this middleware to a specific route and verify the response header.

### Solution

#### 1. Custom HTTP Response Header Middleware
Generate using: `php artisan make:middleware AddCustomHeader`

File: `app/Http/Middleware/AddCustomHeader.php`
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class AddCustomHeader
{
    public function handle(Request $request, Closure $next)
    {
        $response = $next($request);
        // Add a custom HTTP response header
        $response->headers->set('X-LPU-Institution', 'Lovely Professional University');
        return $response;
    }
}
```

#### 2. Define Controller Data Passing (`app/Http/Controllers/DataPassController.php`)
```php
namespace App\Http\Controllers;

use stdClass;

class DataPassController extends Controller
{
    public function index()
    {
        // 1. String Data
        $message = "Welcome to INT221 MVC Programming Practical!";

        // 2. Array Data
        $skills = ['Routing', 'Validation', 'Blade Templates', 'Database Storage'];

        // 3. Object Data
        $studentObj = new stdClass();
        $studentObj->name = "Ravi Ranjan";
        $studentObj->roll = "LPU01202";

        return view('data_pass', compact('message', 'skills', 'studentObj'));
    }
}
```

#### 3. Define Routes & Blade Template
*   *Route:* Apply middleware to this route.
    ```php
    use App\Http\Controllers\DataPassController;
    Route::get('/data-pass', [DataPassController::class, 'index'])
        ->middleware(AddCustomHeader::class);
    ```
*   *Blade File (`resources/views/data_pass.blade.php`):*
    ```html
    <h2>Passing Data from Controller</h2>
    <p>String message: <strong>{{ $message }}</strong></p>
    <hr>
    <p>Array list items:</p>
    <ul>
        @foreach($skills as $skill)
            <li>{{ $skill }}</li>
        @endforeach
    </ul>
    <hr>
    <p>Object student details:</p>
    <p>Student Name: {{ $studentObj->name }}</p>
    <p>Student Roll: {{ $studentObj->roll }}</p>
    ```
