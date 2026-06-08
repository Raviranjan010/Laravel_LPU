# Unit 4: URL Generation, Request Data, Emails, Localization & Sessions

## 📚 Course Outcomes Coverage
*   **CO4**: Manage cookies, emails, and sessions for convenient, secure, and robust functionality in web applications.

---

## 1. URL Generation

Laravel's URL generator helps you create clean, dynamic, and absolute URLs for your application's routes, assets, and controller actions.

### 1.1 The Current URL
You can retrieve URL information from the request:
*   `url()->current()`: Returns the URL path *without* query strings.
    *   *Example:* If browser is at `http://127.0.0.1:8000/search?q=laravel`, it returns `http://127.0.0.1:8000/search`.
*   `url()->full()`: Returns the full URL path, *including* query string variables.
    *   *Example:* Returns `http://127.0.0.1:8000/search?q=laravel`.
*   `url()->previous()`: Returns the URL of the referring page. Highly useful for building "Go Back" buttons.

### 1.2 Assets & Generation Shortcuts
*   **Asset URLs**: Generates absolute paths pointing to assets located inside the public folder (`public/`):
    ```blade
    <!-- Blade Syntax -->
    <link rel="stylesheet" href="{{ asset('css/style.css') }}">
    <script src="{{ asset('js/app.js') }}"></script>
    ```
*   **Named Route URLs**: Generates absolute URLs bound directly to route names. If the route path changes, the URL auto-updates!
    ```php
    // routes/web.php
    Route::get('/post/{id}', [PostController::class, 'show'])->name('posts.show');

    // Controller or view URL generation
    $url = route('posts.show', ['id' => 5]); // Generates: http://localhost:8000/post/5
    ```
*   **Controller Action URLs**: Resolves paths directly from controller signatures:
    ```php
    use App\Http\Controllers\UserController;

    $url = action([UserController::class, 'profile'], ['id' => 12]);
    // Generates: http://localhost:8000/user/profile/12
    ```

---

## 2. Request Data & File Uploads

HTTP requests encapsulate all input payloads and uploaded files sent by clients. Laravel's `Illuminate\Http\Request` class provides an object-oriented way to interact with these payloads.

### 2.1 Retrieving Inputs
```php
public function store(Request $request)
{
    // 1. Retrieve single input with optional fallback
    $username = $request->input('username', 'DefaultGuest');

    // 2. Retrieve input from query string parameters only (?page=2)
    $page = $request->query('page', 1);

    // 3. Retrieve input as boolean (interprets 'true', '1', 'on', 'yes' as true)
    $agreed = $request->boolean('agreed');

    // 4. Check if input is present and is NOT empty (filled)
    if ($request->filled('email')) {
        // Run logic only when email exists and is not empty
    }

    // 5. Check if input is present (even if empty)
    if ($request->has('phone')) { ... }
}
```

### 2.2 Old Inputs (Flashing Data Between Redirects)
To preserve form data when validation fails and redirecting back:
```php
// Inside Controller validation block
$request->flash(); // Flashes all inputs to session

// Or flash and redirect in one go:
return back()->withInput();
```
In Blade, retrieve the flashed input value using the `old()` helper:
```html
<div class="mb-3">
    <label>Username:</label>
    <input type="text" name="username" value="{{ old('username') }}" class="form-control">
</div>
```

### 2.3 File Uploads
To handle file uploads, the HTML `<form>` must contain the `enctype="multipart/form-data"` attribute.
```html
<form action="/upload" method="POST" enctype="multipart/form-data">
    @csrf
    <input type="file" name="avatar">
    <button type="submit">Upload</button>
</form>
```

#### Controller File Upload Handler
```php
public function upload(Request $request)
{
    // 1. Check if file exists in request
    if ($request->hasFile('avatar')) {
        $file = $request->file('avatar');
        
        // 2. Verify file is uploaded successfully without PHP temp folder errors
        if ($file->isValid()) {
            // Retrieve file metadata
            $ext = $file->getClientOriginalExtension(); // e.g. png, jpg
            $mime = $file->getMimeType(); // e.g. image/png
            $size = $file->getSize(); // in bytes

            // 3. Generate a secure, unique filename
            $filename = 'user_avatar_' . time() . '_' . uniqid() . '.' . $ext;

            // 4. Store in storage/app/public/avatars/ using the 'public' disk
            $path = $file->storeAs('avatars', $filename, 'public');

            return "File stored successfully at: " . $path;
        }
    }
    return "Invalid file upload.";
}
```

#### The Symbolic Link (`storage:link`)
By default, files stored in `storage/app/` are private and cannot be accessed by browser requests. To make files stored in `storage/app/public/` accessible, run this command:
```bash
php artisan storage:link
```
*   **What it does:** It creates a symbolic link (shortcut) from `public/storage` pointing to `storage/app/public`.
*   **Accessing the file in Blade:**
    ```blade
    <img src="{{ asset('storage/' . $path) }}" alt="Avatar">
    ```

---

## 3. Laravel Cookies

Cookies are small text files stored on the user's browser, used to track user preferences, sessions, and client-side identifiers.

### 3.1 Creating Response Cookies
To attach a cookie to an outgoing response, use the `cookie()` helper method on the `Response` object:

```php
Route::get('/set-cookie', function () {
    return response("Cookie Created")
        ->cookie(
            'username', // Cookie Name
            'Ravi',     // Cookie Value
            60          // Lifetime in minutes (60 minutes = 1 hour)
        );
});
```

#### 💡 The Response Body Visibility
*   **The Problem:** The code `return response("Cookie Created")` sends the text `"Cookie Created"` in the HTTP response body. This text **will be visible** to the user in the browser window.
*   **The Mechanic:** Laravel executes two separate actions:
    1.  Sends the string `"Cookie Created"` to be rendered as page content.
    2.  Appends a `Set-Cookie` header containing `username=Ravi; Max-Age=3600` to the HTTP headers. The browser reads this header and stores the cookie.
*   **The Solution in Real Projects:** Instead of outputting plain text, redirect the user or load a view:
    ```php
    // Creates cookie and immediately redirects user to dashboard
    return redirect('/dashboard')
        ->cookie('username', 'Ravi', 60);
    ```

### 3.2 Cookie Queuing (`Cookie::queue()`)
If you want to set a cookie but are not returning a direct response immediately (e.g., inside a controller before multiple other operations or views), queue the cookie:
```php
use Illuminate\Support\Facades\Cookie;

public function login(Request $request)
{
    // Queues the cookie to be automatically attached to the final response
    Cookie::queue('remember_token', 'xyz123', 120);

    return view('welcome'); // Cookie is sent along with this view response!
}
```

### 3.3 Critical Concept: Parameter Order
The signature for cookie creation is:
`Cookie::queue($name, $value, $minutes, $path, $domain, $secure, $httpOnly)`

**What happens if the parameters are swapped?**
```php
// Wrong swap: minutes and value
Cookie::queue('username', 60, 'Ravi');
```
*   **How Laravel interprets this:**
    *   Cookie Name = `'username'`
    *   Cookie Value = `60` (interpreted as string)
    *   Minutes = `'Ravi'` (an invalid string where an integer is expected)
*   **Consequence:** Laravel fails to calculate the cookie's expiration date. It will throw a PHP TypeError/exception or fail to set the cookie entirely because the expiration parameter must be an integer.

---

## 4. Sending Emails

Laravel provides a clean, object-oriented API for sending emails using **Mailable** classes, powered by the Symfony Mailer component.

### 4.1 SMTP Configuration (`.env`)
Email credentials and server hosts are specified inside your `.env` configuration file:
```env
MAIL_MAILER=smtp
MAIL_HOST=sandbox.smtp.mailtrap.io
MAIL_PORT=587
MAIL_USERNAME=your_smtp_username
MAIL_PASSWORD=your_smtp_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS="registrar@lpu.co.in"
MAIL_FROM_NAME="LPU Registrar Office"
```

#### 🌐 Deep-Dive: Understanding `MAIL_PORT`
*   **Is the port the same for everyone?**
    No. The port is determined by your mail service provider (Gmail, Yahoo, Outlook, Mailtrap, or a private hosting server) and the security protocol (SSL vs TLS) utilized.
*   **Common Email Ports:**
    *   `25`: The traditional, standard SMTP port (mostly blocked by modern ISPs to prevent spam transmission).
    *   `587`: **Recommended.** Standard port for secure mail submission using **TLS** (Transport Layer Security) encryption.
    *   `465`: Used for legacy SMTP over **SSL** (Secure Sockets Layer) encryption.
    *   `2525`: An alternative port offered by testing platforms (like Mailtrap) if port 587 is blocked.

#### Provider Configurations Examples:
| Provider | Hostname (`MAIL_HOST`) | Port (`MAIL_PORT`) | Encryption (`MAIL_ENCRYPTION`) |
| :--- | :--- | :--- | :--- |
| **Gmail (TLS)** | `smtp.gmail.com` | `587` | `tls` |
| **Gmail (SSL)** | `smtp.gmail.com` | `465` | `ssl` |
| **Outlook (TLS)** | `smtp.office365.com` | `587` | `tls` |
| **Yahoo (SSL)** | `smtp.mail.yahoo.com` | `465` | `ssl` |
| **Mailtrap (Testing)** | `sandbox.smtp.mailtrap.io` | `2525` or `587` | `tls` |

### 4.2 Generating and Configuring a Mailable
Use the Artisan command to generate a mailable:
```bash
php artisan make:mail WelcomeEmail
```
This file is generated at `app/Mail/WelcomeEmail.php`.

#### Class Code: `app/Mail/WelcomeEmail.php`
```php
<?php

namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Queue\SerializesModels;

class WelcomeEmail extends Mailable
{
    use Queueable, SerializesModels;

    // Public properties are automatically exposed to the email Blade view
    public $studentName;
    public $courseName;

    public function __construct($studentName, $courseName)
    {
        $this->studentName = $studentName;
        $this->courseName = $courseName;
    }

    // Configures SMTP envelope properties (Subject, Sender, etc.)
    public function envelope(): Envelope
    {
        return new Envelope(
            subject: 'Welcome to INT221 MVC Programming Course!',
        );
    }

    // Configures the template view to render the HTML body
    public function content(): Content
    {
        return new Content(
            view: 'emails.welcome', // Refers to resources/views/emails/welcome.blade.php
        );
    }
}
```

### 4.3 Creating the Email Blade Template
File: `resources/views/emails/welcome.blade.php`
```html
<!DOCTYPE html>
<html>
<head>
    <title>Course Welcome</title>
</head>
<body style="font-family: Arial, sans-serif; padding: 20px; color: #333;">
    <h2 style="color: #ea580c;">Hello {{ $studentName }},</h2>
    <p>Welcome to LPU Academic Portal. You have successfully registered for the course: <strong>{{ $courseName }}</strong>.</p>
    <p>Please log in to your dashboard to view syllabus and assignments.</p>
    <br>
    <p>Regards,<br>LPU Registrar Team</p>
</body>
</html>
```

### 4.4 Dispatched via Controller
To dispatch the mailable, use the `Mail` facade:
```php
use Illuminate\Support\Facades\Mail;
use App\Mail\WelcomeEmail;

Route::post('/register-student', function(Request $request) {
    $email = $request->input('email');
    $name = $request->input('name');
    
    // Dispatches email using the WelcomeEmail template class
    Mail::to($email)->send(new WelcomeEmail($name, 'INT221 (MVC Programming)'));

    return "Registration complete! Welcome email sent.";
});
```

---

## 5. Laravel Localization

Localization (L10n) is the process of translating your application's user interface text into multiple languages.

### 5.1 ⚠️ Critical Misconception: Translation vs. File Lookup
*   **The Misconception:** Many beginners believe that executing `App::setLocale('hi')` will automatically translate English text into Hindi using AI.
*   **The Reality:** **Laravel does NOT translate text automatically.** Laravel is not a translator engine. Instead, it works like a **dictionary finder**. You must manually create dictionary files for each language containing key-value translation pairs, and Laravel will load the appropriate key based on the active locale.

```
                  [ App::setLocale('hi') ]
                             │
                             ▼
                   Selects Lang Folder:
                       /lang/hi/
                             │
       ┌─────────────────────┴─────────────────────┐
       ▼                                           ▼
Read 'lang/hi/labels.php'                  Retrieve Hindi String:
'welcome' => 'स्वागत है'  ───────────────►  "स्वागत है"
```

### 5.2 Dynamic Language Switcher: Complete Exam Code
Let's build a fully functioning dynamic switcher program that preserves user language selection using sessions and custom middleware.

#### Step 1: Create Translation Files
Create translation files in the root `lang/` folder (folders `/en` and `/hi`).

*   **English File:** `lang/en/message.php`
    ```php
    <?php
    return [
        'welcome' => 'Welcome to LPU Academic Portal',
        'login' => 'Login to Account',
        'contact' => 'Contact Us',
    ];
    ```

*   **Hindi File:** `lang/hi/message.php`
    ```php
    <?php
    return [
        'welcome' => 'एलपीयू अकादमिक पोर्टल में आपका स्वागत है',
        'login' => 'अकाउंट में लॉगिन करें',
        'contact' => 'संपर्क करें',
    ];
    ```

#### Step 2: Create Dynamic Switcher Route
File: `routes/web.php`
```php
use Illuminate\Support\Facades\Route;
use Illuminate\Support\Facades\App;

// Route to handle language switching dynamically
Route::get('/lang/{language}', function($language) {
    if (in_array($language, ['en', 'hi'])) {
        // Store selected language in session
        session()->put('locale', $language);
    }
    return redirect()->back(); // Redirect back to previous page
});

// Main dashboard route
Route::get('/', function() {
    return view('home');
});
```

#### Step 3: Create Localization Middleware
To ensure the language setting persists across every single page request, we use a middleware.
Generate via: `php artisan make:middleware SetAppLocale`

File: `app/Http/Middleware/SetAppLocale.php`
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\App;

class SetAppLocale
{
    public function handle(Request $request, Closure $next)
    {
        // Check if session has user selected locale
        if (session()->has('locale')) {
            // Apply language to application configuration at runtime
            App::setLocale(session('locale'));
        }

        return $next($request);
    }
}
```

#### Step 4: Register Middleware in Kernel
Add the middleware to the `'web'` group in `app/Http/Kernel.php`:
```php
protected $middlewareGroups = [
    'web' => [
        // ... standard middlewares
        \App\Http\Middleware\SetAppLocale::class,
    ],
];
```

#### Step 5: Render in Blade View
File: `resources/views/home.blade.php`
```html
<!DOCTYPE html>
<html>
<head>
    <title>LPU Multilingual Portal</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body class="p-5 bg-light">
    <div class="card mx-auto p-4 shadow-sm" style="max-width: 500px;">
        <div class="d-flex justify-content-between align-items-center mb-4">
            <h5 class="m-0 text-secondary">Language Selector</h5>
            <div>
                <a href="/lang/en" class="btn btn-sm btn-outline-primary">English</a>
                <a href="/lang/hi" class="btn btn-sm btn-outline-danger">हिंदी</a>
            </div>
        </div>
        <hr>
        <!-- Retrieve localized translations using the __() helper -->
        <h1 class="text-primary h3">{{ __('message.welcome') }}</h1>
        <p class="text-muted">{{ __('message.login') }}</p>
        <button class="btn btn-dark w-100 mt-3">{{ __('message.contact') }}</button>
    </div>
</body>
</html>
```

---

## 6. Laravel Sessions

Sessions store temporary, request-independent data about a user across multiple requests.

### 6.1 Configuration & Drivers (`config/session.php`)
The storage engine is determined by `SESSION_DRIVER` inside `.env`.
*   `file`: (Default) Saves sessions as plain text files in `storage/framework/sessions/`. Excellent for local development.
*   `cookie`: Stores encrypted session payloads inside secure cookies. High client overhead.
*   `database`: Stores session records inside a relational database table (`sessions`). Useful for distributed web servers.
*   `redis`: Stores session data inside a Redis cache server. Best performance and scalability.

### 6.2 Comparison of Session Storage Helpers: `put()`, `push()`, and `flash()`

Laravel provides different ways to store session data depending on requirements:

| Feature | `put()` | `push()` | `flash()` |
| :--- | :--- | :--- | :--- |
| **Primary Purpose** | Stores a standard key-value pair. | Appends a value into an array session. | Stores a temporary value for one request. |
| **Lifetime** | Persists indefinitely until manually deleted. | Persists indefinitely until manually deleted. | Persists *only* for the next HTTP request. |
| **Target Data Type** | String, integer, boolean, or object. | Array collection. | String, integer, or message block. |
| **Common Use Case** | Logged-in user ID/role. | Shopping cart items, course collections. | Form submit validation success messages. |
| **Does it auto-delete?** | No. | No. | Yes, automatically after one request. |

#### Real-Life Analogy
*   `put()`: Placing a permanent text file in a physical locker. It stays there until you throw it away.
*   `push()`: Throwing another textbook into your school bag. The bag expands and holds all items inside.
*   `flash()`: Sticking a post-it note on a classroom board. The students read it once, and then the teacher throws it away.

```php
// 1. put() example
session()->put('username', 'Ravi'); // Session contains: ['username' => 'Ravi']

// 2. push() example
session()->push('cart', 'Laptop');
session()->push('cart', 'Mouse'); // Session contains: ['cart' => ['Laptop', 'Mouse']]

// 3. flash() example
session()->flash('status', 'Profile Updated!'); // Session contains status. Deleted on next refresh!
```

---

### 6.3 Deleting Session Data
Laravel provides multiple ways to delete session values based on scope and intent.

```
                     ┌─────────────────── Session Data ───────────────────┐
                     │                                                     │
                     ▼                                                     ▼
           [ Delete specific key ]                               [ Delete ALL data ]
                     │                                                     │
        ┌────────────┴────────────┐                                        │
        ▼                         ▼                                        ▼
    forget()                   pull()                                   flush()
 (Delete only)             (Get & Delete)                       (Wipe locker clean)
```

#### Method 1: `forget()`
Removes one or more specific keys from the session. Returns no value.
```php
// Delete single key
session()->forget('username');

// Delete multiple keys
session()->forget(['username', 'role']);
```

#### Method 2: `pull()`
Retrieves the value of a key AND deletes it from the session in a single atomic transaction.
```php
// Suppose session has: ['role' => 'Admin']
$role = session()->pull('role'); 

echo $role; // Output: Admin
// Session now has no 'role' key.
```

#### Method 3: `flush()`
Destroys all active session keys. Wipes the session locker clean.
```php
session()->flush(); 
// All session data removed. Session is empty.
```

#### Method 4: Session Security & Session IDs (`invalidate()` vs `regenerate()`)
*   `$request->session()->invalidate()`: Wipes all session data **AND** destroys the active session ID (creates a brand new session identifier). Used during **Logout** to prevent session hijacking.
*   `$request->session()->regenerate()`: Generates a brand new session ID **without deleting** the existing data inside the session. Used during **Login** to protect against session fixation attacks.

#### Complete Login & Logout Session Controller Example:
```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class AuthController extends Controller
{
    public function login(Request $request)
    {
        // 1. Authenticate user logic...
        
        // 2. Store user data in session
        $request->session()->put('user', 'Ravi');
        $request->session()->put('role', 'Student');

        // 3. Prevent session fixation attack (New session ID, keeps data)
        $request->session()->regenerate();

        return redirect('/dashboard');
    }

    public function logout(Request $request)
    {
        // 1. Clear data AND destroy session ID for safety
        $request->session()->invalidate();

        // 2. Generate a new CSRF token for the next visitor
        $request->session()->regenerateToken();

        return redirect('/login')->with('status', 'Logged Out Successfully');
    }
}
```

---

### 6.4 Common Session Mistakes & Error Avoidance

*   **Mistake 1: Syntax Arrow Call on Helper Function**
    *   ❌ *Wrong:* `session->forget('key')`
    *   ✅ *Correct:* `session()->forget('key')` or `$request->session()->forget('key')`
    *   *Why:* `session()` is a global helper function. You must call it with parentheses before invoking arrow methods.
*   **Mistake 2: Using `flush()` instead of `forget()`**
    *   ❌ *Wrong:* Calling `session()->flush()` when you only want to clear a single shopping cart item.
    *   *Why:* `flush()` clears *everything*—including the user's login session. Use `forget('cart_item_id')` for selective deletion.
*   **Mistake 3: Confusing `forget()` and `pull()`**
    *   *forget()* is a void method used purely to delete.
    *   *pull()* retrieves the value so you can display/use it one last time while simultaneously clearing it.
