# Unit 4 Question Bank: URL Generation, Requests, Emails & Sessions

## 2-Mark Questions

### Q1. What does the `storage:link` Artisan command do?
**Answer:** It creates a symbolic link from `public/storage` pointing to the private `storage/app/public` folder. This allows files uploaded to the storage folder to be publicly accessible via the web browser.

### Q2. How do you check if a file has been uploaded in a request?
**Answer:** Use the `hasFile()` method on the `Request` object:
```php
if ($request->hasFile('profile_picture')) { ... }
```

### Q3. Write code to fetch a session variable named `'theme'` with a fallback value of `'light'`.
**Answer:**
```php
$theme = session('theme', 'light');
// or
$theme = $request->session()->get('theme', 'light');
```

### Q4. What is a Mailable class in Laravel?
**Answer:** A Mailable class represents a specific email message in Laravel. It handles all configurations for the email (envelope, sender, attachments, views) in a single, reusable class file.

### Q5. Write the syntax for displaying a localization string in Blade template.
**Answer:** Use the double underscore helper syntax:
```blade
{{ __('messages.welcome') }}
```

---

## 5-Mark Questions

### Q6. Detail how file uploads are securely handled in Laravel. Write a sample controller action.
**Answer:**
Laravel handles file uploads securely by abstracting file system interactions and offering automatic filename generation, directory validation, and security sanitization.

1.  **Form setup**: Ensure the HTML form tag has `method="POST"` and `enctype="multipart/form-data"`.
2.  **File retrieve**: Inside the controller, check if the file is valid: `request()->file('name')->isValid()`.
3.  **Sanitization**: Use `getClientOriginalExtension()` and validate MIME types to block malicious execution uploads (e.g. `.php` files masquerading as images).
4.  **Storage**: Use the `storeAs()` method to save the file using a secure, custom name.

#### Code Example:
```php
public function storeAvatar(Request $request) {
    $request->validate([
        'avatar' => 'required|image|mimes:jpg,png|max:1024' // Validation validation checks
    ]);

    if ($request->hasFile('avatar')) {
        $file = $request->file('avatar');
        $fileName = 'user_avatar_' . auth()->id() . '.' . $file->getClientOriginalExtension();
        
        // Store inside storage/app/public/avatars/
        $path = $file->storeAs('avatars', $fileName, 'public');
        
        return back()->with('status', 'File saved: ' . $path);
    }
}
```

### Q7. Explain Session Drivers in Laravel. Compare the:
1.  **File driver**
2.  **Database driver**
3.  **Redis driver**

**Answer:**
Session drivers determine where Laravel stores session data across requests.

#### 1. File Driver:
*   *Mechanics*: Saves each session as a flat text file inside `storage/framework/sessions/`.
*   *Use case*: Default driver. Best for local development and small, single-server projects. Slows down under high concurrency.

#### 2. Database Driver:
*   *Mechanics*: Saves session records in a `sessions` table in your relational database (MySQL).
*   *Use case*: Useful for tracking active sessions and sharing session data across multiple web servers. Requires running a session table migration first.

#### 3. Redis Driver:
*   *Mechanics*: Stores sessions in-memory using a Redis database.
*   *Use case*: Best for enterprise applications. Offers ultra-fast read/write operations and scales well under heavy traffic.

### Q8. Describe how to set up dynamic language switching (Localization) in a Laravel application.
**Answer:**
To set up dynamic localization:

1.  **Create translation files**:
    *   Create files at `lang/en/labels.php` returning `['welcome' => 'Welcome']`.
    *   Create files at `lang/hi/labels.php` returning `['welcome' => 'स्वागत है']`.
2.  **Register a dynamic switcher route**:
    ```php
    Route::get('/language/{locale}', function ($locale) {
        if (in_array($locale, ['en', 'hi'])) {
            session(['locale' => $locale]);
        }
        return back();
    });
    ```
3.  **Register a SetLocale Middleware**:
    Generate middleware `php artisan make:middleware SetLocale` and update the `handle` method:
    ```php
    public function handle($request, Closure $next) {
        if (session()->has('locale')) {
            App::setLocale(session('locale'));
        }
        return $next($request);
    }
    ```
    *Add this middleware to the `web` middleware group.*
4.  **Render in Blade views**:
    ```blade
    <a href="/language/en">English</a> | <a href="/language/hi">Hindi</a>
    <h1>{{ __('labels.welcome') }}</h1>
    ```

---

## 10-Mark Questions

### Q9. Explain the steps to configure and send HTML emails in Laravel. Write the code for a custom Mailable class, an email Blade template, and a Controller action that dispatches the email.
**Answer:**
Laravel's mailing system is built on top of the Symfony Mailer component, offering clean SMTP configurations and object-oriented Mailable classes.

#### Step 1: Configure SMTP environment settings (`.env`):
```env
MAIL_MAILER=smtp
MAIL_HOST=sandbox.smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=user_id
MAIL_PASSWORD=password_id
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS="registrar@lpu.co.in"
MAIL_FROM_NAME="LPU Registrar Office"
```

#### Step 2: Generate and write the Mailable class:
Generate via: `php artisan make:mail ExamWelcomeMail`
File: `app/Mail/ExamWelcomeMail.php`
```php
<?php

namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Queue\SerializesModels;

class ExamWelcomeMail extends Mailable
{
    use Queueable, SerializesModels;

    // Public properties are automatically exposed to email views
    public $studentName;
    public $examTime;

    public function __construct($studentName, $examTime)
    {
        $this->studentName = $studentName;
        $this->examTime = $examTime;
    }

    public function envelope(): Envelope
    {
        return new Envelope(
            subject: 'Lovely Professional University Exam Notification',
        );
    }

    public function content(): Content
    {
        return new Content(
            view: 'emails.exam_welcome', // Blade template path
        );
    }
}
```

#### Step 3: Create the email Blade View template:
File: `resources/views/emails/exam_welcome.blade.php`
```html
<!DOCTYPE html>
<html>
<head>
    <title>Exam Notification</title>
</head>
<body style="font-family: sans-serif; padding: 20px; color: #333;">
    <h2 style="color: #f60;">Dear {{ $studentName }},</h2>
    <p>This email is to confirm your registration for the upcoming practical examination:</p>
    <ul>
        <li><strong>Course Code</strong>: INT221 (MVC Programming)</li>
        <li><strong>Exam Date & Time</strong>: {{ $examTime }}</li>
    </ul>
    <p>Please make sure you have configured your environment before the exam start time.</p>
    <br>
    <p>Good Luck,<br>LPU Academic Portal Team</p>
</body>
</html>
```

#### Step 4: Dispatch the mail inside a Controller:
File: `app/Http/Controllers/ExamController.php`
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Mail;
use App\Mail\ExamWelcomeMail;

class ExamController extends Controller
{
    public function notifyStudent(Request $request)
    {
        $email = $request->input('email');
        $name = $request->input('name');
        $time = now()->addDays(2)->toDayDateTimeString();

        // Dispatch email using the Mail facade
        Mail::to($email)->send(new ExamWelcomeMail($name, $time));

        return response()->json([
            'success' => true,
            'message' => 'Exam welcome notification email has been dispatched!'
        ], 200);
    }
}
```
