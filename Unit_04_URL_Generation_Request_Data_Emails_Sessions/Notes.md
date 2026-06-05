# Unit 4: URL Generation, Request Data, Emails, Localization & Sessions

## 📚 Course Outcomes Coverage
*   **CO4**: Manage cookies, emails, sessions for convenient and robust functionality.

---

## URL Generation

Laravel's URL generator helps you create clean, dynamic links for your application.

### 1. The Current URL
You can retrieve URL information from the request:
*   `url()->current()`: Returns the URL path without query strings.
*   `url()->full()`: Returns the full URL path, including query string variables.
*   `url()->previous()`: Returns the URL of the referring page (useful for Go Back actions).

### 2. Assets & Generation Shortcuts
*   **Asset URLs**: Generates absolute paths pointing to assets located inside the `public/` folder:
    ```blade
    <script src="{{ asset('js/app.js') }}"></script>
    ```
*   **Named Route URLs**: Generates URLs bound directly to route names:
    ```php
    $url = route('posts.show', ['id' => 5]); // http://localhost:8000/posts/5
    ```
*   **Controller Action URLs**: Resolves paths directly from controller signatures:
    ```php
    $url = action([UserController::class, 'profile'], ['id' => 12]);
    ```

---

## Request Data & File Uploads

HTTP requests encapsulate all input payloads and uploaded files sent by clients.

### 1. Retrieving Inputs
```php
public function store(Request $request)
{
    // Retrieve single input
    $username = $request->input('username', 'DefaultGuest');

    // Retrieve input from query string only
    $search = $request->query('q');

    // Retrieve boolean check for checkboxes
    $agreed = $request->boolean('agreed');

    // Check if input is present and has value
    if ($request->filled('email')) { ... }
}
```

### 2. Old Inputs
To preserve data between redirects (e.g. when form validations fail), flash data to the session:
```php
// Flash all inputs to the session
$request->flash();

// Redirect back with old input
return back()->withInput();
```
In Blade, use the `old()` helper to repopulate the input value:
```html
<input type="text" name="username" value="{{ old('username') }}">
```

### 3. File Uploads
To upload files, the HTML `<form>` must contain `enctype="multipart/form-data"`.

```php
public function upload(Request $request)
{
    if ($request->hasFile('avatar')) {
        $file = $request->file('avatar');
        
        if ($file->isValid()) {
            $ext = $file->getClientOriginalExtension();
            $mime = $file->getMimeType(); // e.g. image/jpeg
            $size = $file->getSize(); // in bytes

            // Unique filename generation
            $filename = 'user_' . time() . '.' . $ext;

            // Store in storage/app/public/avatars/
            $path = $file->storeAs('avatars', $filename, 'public');

            return "File successfully stored at: " . $path;
        }
    }
}
```
Create a symbolic link to make these files accessible from the web:
```bash
php artisan storage:link
```
Then, access the asset in your view:
```blade
<img src="{{ asset('storage/' . $path) }}">
```

---

## Sending Emails

Laravel uses standard **Mailable** classes to construct and send emails via SMTP.

### 1. SMTP Mail Configuration (`.env`)
```env
MAIL_MAILER=smtp
MAIL_HOST=sandbox.smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=your_username
MAIL_PASSWORD=your_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS="library@lpu.co.in"
MAIL_FROM_NAME="LPU Library"
```

### 2. Generating Mailable Class
```bash
php artisan make:mail WelcomeEmail
```
This generates `app/Mail/WelcomeEmail.php`.

### 3. Class Configuration
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

    public $studentName; // Automatically passed to Blade views

    public function __construct($studentName)
    {
        $this->studentName = $studentName;
    }

    public function envelope(): Envelope
    {
        return new Envelope(
            subject: 'Welcome to Lovely Professional University Portal',
        );
    }

    public function content(): Content
    {
        return new Content(
            view: 'emails.welcome', // Resources/views/emails/welcome.blade.php
        );
    }
}
```

### 4. Sending the Mail
```php
use Illuminate\Support\Facades\Mail;
use App\Mail\WelcomeEmail;

Mail::to('student@lpu.in')->send(new WelcomeEmail('Amit Kumar'));
```

---

## Laravel Localization

Localization allows you to translate UI text into multiple languages.

### 1. Translation Files Structure
*   English: `lang/en/messages.php`
    ```php
    return ['greeting' => 'Welcome, :name!'];
    ```
*   Hindi: `lang/hi/messages.php`
    ```php
    return ['greeting' => 'आपका स्वागत है, :name!'];
    ```

### 2. Translation in Views
Use the double underscore helper inside Blade templates:
```blade
<h1>{{ __('messages.greeting', ['name' => 'Amit']) }}</h1>
```

### 3. Switching Locale Dynamically
```php
use Illuminate\Support\Facades\App;

Route::get('/lang/{locale}', function ($locale) {
    if (in_array($locale, ['en', 'hi'])) {
        App::setLocale($locale);
        session(['app_locale' => $locale]);
    }
    return back();
});
```

---

## Laravel Sessions

Sessions store temporary data across client requests.

### 1. Configuration (`config/session.php`)
Configure your session driver in your `.env` file using the `SESSION_DRIVER` key. Supported drivers include:
*   `file`: (Default) Saves sessions in files inside `storage/framework/sessions/`.
*   `cookie`: Saves encrypted session payloads directly inside browser cookies.
*   `database`: Stores session records inside a database table.
*   `redis`: Stores session data inside a Redis cache server for high performance.

### 2. Session CRUD Operations
```php
// 1. Store value in session
session(['cart_count' => 3]);
$request->session()->put('role', 'editor');

// 2. Retrieve value
$count = session('cart_count');
$role = $request->session()->get('role', 'default-role');

// 3. Check if key exists and is not null
if (session()->has('role')) { ... }

// 4. Retrieve and delete in one step (Atomic Pull)
$role = session()->pull('role');

// 5. Delete specific key
session()->forget('cart_count');

// 6. Delete all session data
session()->flush();
```
