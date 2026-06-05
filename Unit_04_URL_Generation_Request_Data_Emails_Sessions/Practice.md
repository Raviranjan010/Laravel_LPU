# Unit 4 Practice Lab: Requests, Uploads, Mail & Sessions

## 🔬 Practical Objectives
*   Build a profile upload form using `enctype="multipart/form-data"`.
*   Validate, store files in the public disk, and generate asset symlinks.
*   Setup SMTP mailers and dispatch customized Mailable files.
*   Implement localization folders and dynamic session-based language switchers.

---

## Exercise 1: File Upload & Symlink Integration

### Objective: Create an avatar upload form and link the storage to make the image viewable.

### Step-by-Step Instructions:

1.  **Configure routes** in `routes/web.php`:
    ```php
    Route::get('/profile', function() {
        return view('profile_view');
    });

    Route::post('/profile/avatar', function(\Illuminate\Http\Request $request) {
        $request->validate([
            'avatar' => 'required|image|mimes:jpeg,png,webp|max:2048'
        ]);

        if ($request->hasFile('avatar')) {
            $file = $request->file('avatar');
            $filename = 'avatar_' . time() . '.' . $file->getClientOriginalExtension();
            
            // Store inside storage/app/public/avatars/
            $path = $file->storeAs('avatars', $filename, 'public');

            // Save filename in user session
            session(['avatar_path' => $path]);

            return back()->with('status', 'Avatar uploaded successfully!');
        }
    });
    ```
2.  **Create the view file**: `resources/views/profile_view.blade.php`:
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>Profile Avatar Lab</title>
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    </head>
    <body class="p-5">
        <div class="card p-4 mx-auto" style="max-width: 450px;">
            <h3>User Profile Dashboard</h3>

            @if(session('status'))
                <div class="alert alert-success">{{ session('status') }}</div>
            @endif

            @if(session()->has('avatar_path'))
                <div class="text-center mb-4">
                    {{-- Load image via public storage link --}}
                    <img src="{{ asset('storage/' . session('avatar_path')) }}" 
                         class="rounded-circle img-thumbnail" 
                         style="width: 150px; height: 150px; object-fit: cover;">
                </div>
            @endif

            <form action="/profile/avatar" method="POST" enctype="multipart/form-data">
                @csrf
                <div class="mb-3">
                    <label class="form-label">Upload Profile Picture (Max 2MB)</label>
                    <input type="file" name="avatar" class="form-control" required>
                </div>
                <button type="submit" class="btn btn-success w-100">Upload & Save Session</button>
            </form>
        </div>
    </body>
    </html>
    ```
3.  **Create the storage link** in your terminal:
    ```bash
    php artisan storage:link
    ```
4.  **Test**: Visit `http://localhost:8000/profile`, upload a picture, and verify it renders on the dashboard.

---

## Exercise 2: Configuring SMTP Mailtrap & Sending Mail

### Objective: Set up a local test environment and send a custom exam notification email.

### Step-by-Step Instructions:

1.  **Configure Mailtrap** inside `.env`:
    Create a free account at [mailtrap.io](https://mailtrap.io/), go to Email Testing, and copy the SMTP credentials:
    ```env
    MAIL_MAILER=smtp
    MAIL_HOST=sandbox.smtp.mailtrap.io
    MAIL_PORT=2525
    MAIL_USERNAME=your_mailtrap_username
    MAIL_PASSWORD=your_mailtrap_password
    MAIL_FROM_ADDRESS="exam-portal@lpu.co.in"
    MAIL_FROM_NAME="LPU Exam Office"
    ```
2.  **Generate Mailable file**:
    ```bash
    php artisan make:mail ExamNotification
    ```
3.  Edit `app/Mail/ExamNotification.php`:
    ```php
    <?php

    namespace App\Mail;

    use Illuminate\Mail\Mailable;
    use Illuminate\Mail\Mailables\Content;
    use Illuminate\Mail\Mailables\Envelope;

    class ExamNotification extends Mailable
    {
        public $details;

        public function __construct($details) { $this->details = $details; }

        public function envelope(): Envelope
        {
            return new Envelope(subject: 'INT221 Practical Exam Scheduled Date');
        }

        public function content(): Content
        {
            return new Content(view: 'emails.notification');
        }
    }
    ```
4.  Create the email template file: `resources/views/emails/notification.blade.php`:
    ```html
    <h3>LPU Academic Portal</h3>
    <p>Dear Student,</p>
    <p>Your practical exam for <strong>{{ $details['subject'] }}</strong> is scheduled for <strong>{{ $details['date'] }}</strong>.</p>
    <p>All the best!</p>
    ```
5.  Add route to trigger email:
    ```php
    use App\Mail\ExamNotification;
    use Illuminate\Support\Facades\Mail;

    Route::get('/send-test-email', function() {
        $details = [
            'subject' => 'INT221 (MVC Programming)',
            'date' => 'June 10, 2026'
        ];
        
        Mail::to('student-test@lpu.in')->send(new ExamNotification($details));

        return "Notification email has been sent successfully to your test inbox!";
    });
    ```
6.  **Test**: Visit `http://localhost:8000/send-test-email` and check your Mailtrap inbox.

---

## Exercise 3: Dynamic Language Translation Switcher

### Objective: Build an dynamic English-Hindi translator site using dynamic sessions locale setting.

### Step-by-Step Instructions:

1.  Create English file `lang/en/labels.php`:
    ```php
    return [
        'welcome' => 'Welcome to the Lovely Professional University Academic System!',
        'course' => 'Practical course code: INT221.'
    ];
    ```
2.  Create Hindi file `lang/hi/labels.php`:
    ```php
    return [
        'welcome' => 'लवली प्रोफेशनल यूनिवर्सिटी अकादमिक प्रणाली में आपका स्वागत है!',
        'course' => 'प्रैक्टिकल कोर्स कोड: INT221।'
    ];
    ```
3.  Create the dynamic switcher route:
    ```php
    use Illuminate\Support\Facades\App;

    Route::get('/lang/{locale}', function($locale) {
        if (in_array($locale, ['en', 'hi'])) {
            session(['locale' => $locale]);
        }
        return redirect()->back();
    });
    ```
4.  Generate Middleware:
    ```bash
    php artisan make:middleware SetAppLocale
    ```
5.  Edit `app/Http/Middleware/SetAppLocale.php` handle logic:
    ```php
    public function handle($request, \Closure $next)
    {
        if (session()->has('locale')) {
            App::setLocale(session('locale'));
        }
        return $next($request);
    }
    ```
6.  Register Middleware in `app/Http/Kernel.php` under `'web'` array:
    ```php
    protected $middlewareGroups = [
        'web' => [
            // ... other middlewares
            \App\Http\Middleware\SetAppLocale::class,
        ],
    ];
    ```
7.  Create a view `resources/views/lang_dashboard.blade.php`:
    ```blade
    <!DOCTYPE html>
    <html>
    <head>
        <title>Dynamic Lang Switcher</title>
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    </head>
    <body class="p-5">
        <div class="card p-4 mx-auto" style="max-width: 500px;">
            <div class="text-end">
                <a href="/lang/en" class="btn btn-sm btn-outline-primary">English</a>
                <a href="/lang/hi" class="btn btn-sm btn-outline-danger">हिंदी</a>
            </div>
            <hr>
            <h3>{{ __('labels.welcome') }}</h3>
            <p class="text-muted">{{ __('labels.course') }}</p>
        </div>
    </body>
    </html>
    ```
8.  Add landing route:
    ```php
    Route::get('/localization', function() {
        return view('lang_dashboard');
    });
    ```
9.  **Test**: Visit `http://localhost:8000/localization` and switch back and forth between languages.
