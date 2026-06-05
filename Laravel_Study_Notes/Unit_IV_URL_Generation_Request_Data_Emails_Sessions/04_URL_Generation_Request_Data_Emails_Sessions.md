# Unit IV: URL Generation, Request Data, Emails, Localization, & Sessions

## 📚 Table of Contents
- [URL Generation](#url-generation)
- [Request Data & File Uploads](#request-data--file-uploads)
- [Sending Emails](#sending-emails)
- [Laravel Localization](#laravel-localization)
- [Laravel Sessions](#laravel-sessions)
- [Common Mistakes to Avoid (Gotchas)](#common-mistakes-to-avoid-gotchas)
- [Keywords to Remember](#keywords-to-remember)
- [Exam Practice Questions](#exam-practice-questions)

---

## URL Generation

Laravel provides several helper functions to generate URLs for your application. This prevents hardcoding URLs, ensuring that if routes change in `routes/web.php`, the generated URLs update automatically throughout your application.

### 1. Generating Basic URLs
The `url()` helper generates a standard HTTP/HTTPS URL to a given path:

```php
// If the app runs on http://localhost:8000
$url = url('/profile'); // http://localhost:8000/profile
```

### 2. The Current URL
You can retrieve the current URL using the `url()` helper or the `Request` object:

| Helper / Method | Output | Description |
|-----------------|--------|-------------|
| `url()->current()` | `http://localhost:8000/profile` | Returns current URL **without** query parameters. |
| `url()->full()` | `http://localhost:8000/profile?ref=exam` | Returns current URL **with** query parameters. |
| `url()->previous()` | `http://localhost:8000/dashboard` | Returns the URL of the request that redirected to the current page. |

### 3. Generating URLs for Named Routes
If a route has a name, you should use the `route()` helper. This is the **industry standard best practice**.

```php
// In routes/web.php
Route::get('/user/settings/profile', [UserController::class, 'settings'])->name('user.settings');

// In a controller or view
$url = route('user.settings'); // http://localhost:8000/user/settings/profile
```

#### Passing Parameters to Named Routes:
```php
// In routes/web.php
Route::get('/posts/{id}/comments/{comment_id}', [CommentController::class, 'show'])->name('posts.comments.show');

// In a controller
$url = route('posts.comments.show', ['id' => 1, 'comment_id' => 45]);
// Output: http://localhost:8000/posts/1/comments/45

// Passing extra elements (appended as query string)
$url = route('posts.comments.show', ['id' => 1, 'comment_id' => 45, 'sortBy' => 'newest']);
// Output: http://localhost:8000/posts/1/comments/45?sortBy=newest
```

### 4. Generating URLs for Controller Actions
The `action()` helper generates URLs for controller actions directly:

```php
use App\Http\Controllers\UserController;

$url = action([UserController::class, 'index']);
// Output: http://localhost:8000/users

// Passing parameters:
$url = action([UserController::class, 'show'], ['id' => 5]);
// Output: http://localhost:8000/users/5
```

### 5. Generating Asset URLs
Asset URLs point to files stored in the `public/` directory (e.g., CSS, JavaScript, images):

```php
// In Blade layout
<link rel="stylesheet" href="{{ asset('css/styles.css') }}">
<!-- Output: <link rel="stylesheet" href="http://localhost:8000/css/styles.css"> -->

<img src="{{ asset('images/logo.png') }}" alt="Logo">
<!-- Output: <img src="http://localhost:8000/images/logo.png" alt="Logo"> -->
```

> [!TIP]
> If your application is served over HTTPS, use the `secure_asset()` helper instead of `asset()` to enforce SSL encryption.

---

## Request Data & File Uploads

When a user submits a form or makes an API request, Laravel captures the payload and presents it via the `Illuminate\Http\Request` class.

### 1. Retrieving Input Values

Let's assume the user submits a form with `username` and `email` fields:

```php
public function store(Request $request)
{
    // 1. Retrieve all inputs as an associative array
    $allInputs = $request->all();

    // 2. Retrieve a specific input (with fallback default value)
    $username = $request->input('username');
    $role = $request->input('role', 'subscriber'); // Defaults to 'subscriber' if not provided

    // 3. Check if input is present
    if ($request->has('email')) {
        // Runs if "email" exists (even if empty)
    }

    if ($request->filled('email')) {
        // Runs only if "email" is present AND not empty
    }

    // 4. Retrieve subset of data
    $onlyData = $request->only(['username', 'email']);
    $exceptData = $request->except(['password']);
}
```

### 2. Retrieving Old Input (Form Repopulation)
When validation fails, you want to redirect the user back to the form with their previously entered inputs still filled.

*   **Flashing to Session:** Laravel's validator does this automatically, or you can do it manually in controllers:
    ```php
    $request->flash(); // Flashes all inputs to session for the next request
    $request->flashOnly(['username']);
    ```
*   **Retrieving in Blade:** Use the `old()` helper inside form input value attributes.
    ```html
    <input type="text" name="username" value="{{ old('username') }}">
    <input type="text" name="email" value="{{ old('email', 'default@example.com') }}">
    ```

### 3. Handling Uploaded Files
To handle files, the HTML form must include `enctype="multipart/form-data"`.

```php
public function uploadAvatar(Request $request)
{
    // Check if file is uploaded
    if ($request->hasFile('avatar')) {
        
        // Retrieve file object (instance of Illuminate\Http\UploadedFile)
        $file = $request->file('avatar');
        
        // Check if file upload was successful without errors
        if ($file->isValid()) {
            
            // Get file metadata
            $originalName = $file->getClientOriginalName();
            $extension = $file->getClientOriginalExtension();
            $size = $file->getSize(); // in bytes
            $mime = $file->getMimeType(); // e.g., image/png

            // Generate a unique filename
            $filename = 'avatar_' . time() . '.' . $extension;

            // Store the file inside "storage/app/public/avatars"
            // The method returns the path relative to the disk root
            $path = $file->storeAs('avatars', $filename, 'public');

            return back()->with('success', 'File uploaded successfully! Path: ' . $path);
        }
    }

    return back()->withErrors('No valid file was uploaded.');
}
```

#### File Storage Disks Visualized:
```
File Uploaded → Request Object → file() → storeAs()
                                            │
         ┌──────────────────────────────────┼──────────────────────────────────┐
         ▼                                  ▼                                  ▼
Disk: 'local'                        Disk: 'public'                     Disk: 's3'
(storage/app/...)                    (storage/app/public/...)           (Amazon Web Services S3)
*Private files                       *Public assets (needs link)        *Cloud Storage
```

To make the public files accessible, create a symlink:
```bash
php artisan storage:link
```
This links `public/storage` to `storage/app/public` so files can be accessed via `asset('storage/avatars/filename.png')`.

### 4. Retrieving Cookies
You can retrieve cookies using the `Request` object or the `Cookie` facade:

```php
// Method 1: Using Request
$value = $request->cookie('theme');

// Method 2: Using Cookie Facade
use Illuminate\Support\Facades\Cookie;
$value = Cookie::get('theme');
```

---

## Sending Emails

Laravel provides a clean, clean email API built on top of the popular Symfony Mailer component. Emails are configured in `.env` and sent using reusable **Mailable** classes.

### 1. SMTP Mail Configuration (.env)
Before sending emails, configure your SMTP server settings in the `.env` file:

```env
MAIL_MAILER=smtp
MAIL_HOST=sandbox.smtp.mailtrap.io   # Example: Mailtrap for testing
MAIL_PORT=2525
MAIL_USERNAME=your_smtp_username
MAIL_PASSWORD=your_smtp_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS="no-reply@example.com"
MAIL_FROM_NAME="Laravel Academy"
```

### 2. Creating a Mailable Class
Generate a mailable class using Artisan:
```bash
php artisan make:mail WelcomeEmail
```
This generates a file in `app/Mail/WelcomeEmail.php`. Open it and set it up:

```php
<?php

namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Mail\Mailables\Address;
use Illuminate\Queue\SerializesModels;

class WelcomeEmail extends Mailable
{
    use Queueable, SerializesModels;

    public $user; // Public variables are automatically available in the Blade template!

    public function __construct($user)
    {
        $this->user = $user;
    }

    // Define the envelope details (subject, sender)
    public function envelope(): Envelope
    {
        return new Envelope(
            from: new Address('no-reply@example.com', 'Laravel LPU App'),
            subject: 'Welcome to Our Platform!',
        );
    }

    // Define the content details (Blade view)
    public function content(): Content
    {
        return new Content(
            view: 'emails.welcome', // Refers to resources/views/emails/welcome.blade.php
        );
    }
}
```

### 3. Creating the Email Blade View
Create `resources/views/emails/welcome.blade.php`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome</title>
</head>
<body style="font-family: Arial, sans-serif; padding: 20px;">
    <h2>Hello, {{ $user['name'] }}! 👋</h2>
    <p>Thank you for registering on our platform. We are thrilled to have you join us.</p>
    <p>Your registered email is: <strong>{{ $user['email'] }}</strong></p>
    <br>
    <p>Regards,<br>Development Team</p>
</body>
</html>
```

### 4. Sending the Email (Controller Action)
Now send the mailable class using the `Mail` facade:

```php
use Illuminate\Support\Facades\Mail;
use App\Mail\WelcomeEmail;

public function registerUser()
{
    $user = [
        'name' => 'Amit Kumar',
        'email' => 'amit.lpu@example.com'
    ];

    // Send email to recipient
    Mail::to($user['email'])->send(new WelcomeEmail($user));

    return "Email sent successfully!";
}
```

---

## Laravel Localization

Localization allows your application to support multiple languages. This is done by creating translation files in the `resources/lang` (or `lang` in newer versions) directory.

### 1. Directory Structure

```
lang/
├── en/                  # English translations
│   ├── messages.php
│   └── auth.php
└── hi/                  # Hindi translations
    ├── messages.php
    └── auth.php
```

### 2. Creating a Translation File
Let's create `lang/en/messages.php`:
```php
return [
    'welcome' => 'Welcome to our application!',
    'login_prompt' => 'Please login to continue.',
    'greet' => 'Hello, :name!', // String parameter placeholder
];
```

And create the Hindi version in `lang/hi/messages.php`:
```php
return [
    'welcome' => 'हमारे एप्लिकेशन में आपका स्वागत है!',
    'login_prompt' => 'आगे बढ़ने के लिए कृपया लॉगिन करें।',
    'greet' => 'नमस्ते, :name!',
];
```

### 3. Displaying Translated Strings in Blade
Use the `__` (double underscore) helper function in your views:

```blade
<h1>{{ __('messages.welcome') }}</h1>
<p>{{ __('messages.login_prompt') }}</p>

<!-- Translating with parameters -->
<h3>{{ __('messages.greet', ['name' => 'Amit']) }}</h3>
```

### 4. Changing Locale Programmatically
You can change the language dynamically inside a middleware, controller, or route:

```php
use Illuminate\Support\Facades\App;

Route::get('/language/{locale}', function ($locale) {
    if (in_array($locale, ['en', 'hi'])) {
        // Change locale in current runtime session
        App::setLocale($locale);
        // Save to session so next requests load this language
        session(['locale' => $locale]);
    }
    return redirect()->back();
});
```

To make it persistent across all pages, register a custom middleware to read the locale from the session:
```php
public function handle(Request $request, Closure $next)
{
    if (session()->has('locale')) {
        App::setLocale(session('locale'));
    }
    return $next($request);
}
```

---

## Laravel Sessions

Sessions store state/data across multiple requests made by a specific user. Unlike database records, session data is temporary and expires.

### Session Drivers Configuration
Laravel supports multiple session drivers, configured in `.env` under `SESSION_DRIVER`:

| Driver | Storage Location | Performance | Good For |
|--------|------------------|-------------|----------|
| `file` (Default) | `storage/framework/sessions/` | Moderate | Local Development |
| `cookie` | Encrypted in client cookies | Good | Stateless Apps (no server storage) |
| `database` | Saved in a database table | Moderate | Tracking active user sessions |
| `redis` | In-memory cache database | **Excellent** | Large-scale Production Apps |

---

### Working with Session Data

#### 1. Storing Data in Session
You can store data using the global `session()` helper or request object:

```php
// Method 1: Using session() helper (Most common)
session(['cart_items' => [12, 15, 18]]);
session(['user_role' => 'admin']);

// Method 2: Using Request Object
$request->session()->put('logged_in_time', now());
```

#### 2. Accessing Data from Session
```php
// Retrieve data
$role = session('user_role'); // Returns 'admin'

// Retrieve with a fallback default value
$theme = session('theme', 'light');

// Retrieve using Request Object
$cart = $request->session()->get('cart_items');

// Check if an item exists in session
if (session()->has('user_role')) {
    // Runs if "user_role" is present AND not null
}

if (session()->exists('user_role')) {
    // Runs if "user_role" is defined in session, even if it is null
}
```

#### 3. Deleting Data from Session
```php
// Remove a specific key
session()->forget('theme');

// Remove multiple keys
session()->forget(['theme', 'cart_items']);

// Retrieve and immediately delete a value (Pull method)
$items = session()->pull('cart_items'); 

// Delete all session data (Clear out session)
session()->flush();
```

#### 4. Flash Data (Temporary Session)
Sometimes you only need to store an item for the **very next request** (like a success alert after submitting a form). Use flash sessions:

```php
// In Controller
$request->session()->flash('status', 'Task completed successfully!');
return redirect('/dashboard');

// In Blade View (Next request only)
@if (session('status'))
    <div class="alert alert-success">
        {{ session('status') }}
    </div>
@endif
```

---

## Common Mistakes to Avoid (Gotchas)

### ❌ Hardcoding URLs in Blade Views
*Don't do this:*
```html
<a href="/user/settings/profile">Settings</a>
```
If you change the route signature tomorrow to `/user/options`, this link breaks.
*Do this:*
```html
<a href="{{ route('user.settings') }}">Settings</a>
```

### ❌ Forgetting `enctype` on File Upload Forms
If you submit a file input without the form having `enctype="multipart/form-data"`, the file upload will fail, and `$request->file('avatar')` will return `null`.

### ❌ Mixing up `session()->has()` and `session()->exists()`
*   `has()` returns `true` only if the key is present **and is not null**.
*   `exists()` returns `true` if the key is present, **even if its value is null**.

### ❌ Directly using PHP's native `$_SESSION` superglobal
Laravel abstracts session management. Using PHP `session_start()` or `$_SESSION` bypasses Laravel's middleware, CSRF protections, security layers, and drivers, leading to major authentication and session sync issues. Always use `session()` helper or `Session` facade.

---

## Keywords to Remember

1.  **Mailable**: A class in Laravel that configures all settings (from, subject, view, attachments) required to build and dispatch an email.
2.  **Symlink**: A symbolic link created between `public/storage` and `storage/app/public` so files uploaded securely in the storage directory can be accessed in browser views.
3.  **Locale**: The language settings of the application (e.g., `en`, `hi`, `fr`) managed dynamically to serve localized translations.
4.  **Old Input**: Request values flashed to the session temporarily to allow form fields to be repopulated after validation errors.
5.  **Session Pull**: A method that retrieves a value from the session storage and deletes it in a single atomic action.

---

## Exam Practice Questions

### Short Answer Questions (2-5 Marks)
1.  **Explain the difference between `url()->current()` and `url()->full()`.**
    *Answer:* `current()` returns the current URL path excluding query parameters (e.g., `http://test.com/index`). `full()` returns the entire URL path including the query parameter string (e.g., `http://test.com/index?page=2`).
2.  **Write code to store a file named `report.pdf` uploaded via input `user_report` into a folder called `documents` inside the `public` storage disk.**
    ```php
    if ($request->hasFile('user_report')) {
        $path = $request->file('user_report')->storeAs('documents', 'report.pdf', 'public');
    }
    ```
3.  **Explain the role of the `old()` helper function in Laravel form design.**
    *Answer:* It retrieves form inputs flashed to the session from the previous failed request, allowing input elements to hold their previous values during form validation errors instead of forcing users to retype everything.
4.  **List four different session drivers supported by Laravel.**
    *Answer:* `file`, `cookie`, `database`, `redis`, `memcached`, `array`.

### Long Answer Questions (10 Marks)
1.  **Describe in detail how file uploads are handled in Laravel. Provide the controller code and Blade view code for a profile picture upload form with validation.**
    *(Hint: Refer to the Request Data section. Make sure the view has `enctype="multipart/form-data"` and controller does `hasFile()`, validation, and `storeAs()` storage actions.)*
2.  **Explain Laravel Localization mechanism. Design an English and Hindi translation system showing greeting messages, and explain how to switch the local dynamically.**
    *(Hint: Provide directory layout of `/lang/en/` and `/lang/hi/`, demonstrate `__('filename.key')`, and show setting locale using `App::setLocale($lang)` in a route or controller.)*
