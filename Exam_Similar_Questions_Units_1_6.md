# Similar Practical & Theory Questions (Units 1 - 6)

This document contains expected similar questions and complete solutions across all 6 units of the INT221 MVC Programming course syllabus.

---

## Unit 1: Getting Started & MVC Architecture

### Expected Question 1: MVC Flow Demonstration
*   **Question:** Define a custom route that maps to a controller. The controller should fetch static user records from an array and pass them to a Blade view that renders the data in an HTML table.
*   **Solution:**
    *   *Route (`routes/web.php`):*
        ```php
        use App\Http\Controllers\UserController;
        Route::get('/users-mvc', [UserController::class, 'index']);
        ```
    *   *Controller (`app/Http/Controllers/UserController.php`):*
        ```php
        namespace App\Http\Controllers;

        class UserController extends Controller
        {
            public function index()
            {
                $users = [
                    ['id' => 1, 'name' => 'Amit Kumar', 'role' => 'Administrator'],
                    ['id' => 2, 'name' => 'Neha Sharma', 'role' => 'Student'],
                    ['id' => 3, 'name' => 'Ravi Ranjan', 'role' => 'Teacher']
                ];
                return view('users_mvc', compact('users'));
            }
        }
        ```
    *   *Blade View (`resources/views/users_mvc.blade.php`):*
        ```html
        <h2>Registered System Users</h2>
        <table border="1" cellpadding="5" style="border-collapse: collapse;">
            <thead>
                <tr style="background:#eee;"><th>ID</th><th>Name</th><th>Role</th></tr>
            </thead>
            <tbody>
                @foreach($users as $user)
                    <tr>
                        <td>{{ $user['id'] }}</td>
                        <td>{{ $user['name'] }}</td>
                        <td>{{ $user['role'] }}</td>
                    </tr>
                @endforeach
            </tbody>
        </table>
        ```

---

## Unit 2: Request Routing & Responses

### Expected Question 2: JSON APIs & Named Redirection
*   **Question:** Write routes and controller actions to:
    1. Return user details in JSON format for route `/api/user/{id}`.
    2. Redirect the visitor from route `/legacy-url` to a named route `user.profile` with status code 301.
*   **Solution:**
    *   *Routes (`routes/web.php`):*
        ```php
        use App\Http\Controllers\RouteResponseController;

        // Named Route
        Route::get('/new-profile/{name}', [RouteResponseController::class, 'profile'])
            ->name('user.profile');

        // Legacy Redirect
        Route::get('/legacy-url', function() {
            return redirect()->route('user.profile', ['name' => 'Ravi'], 301);
        });

        // JSON Response API
        Route::get('/api/user/{id}', [RouteResponseController::class, 'getUserJson']);
        ```
    *   *Controller (`app/Http/Controllers/RouteResponseController.php`):*
        ```php
        namespace App\Http\Controllers;

        class RouteResponseController extends Controller
        {
            public function profile($name) {
                return "Viewing profile of: " . $name;
            }

            public function getUserJson($id) {
                $user = ['id' => $id, 'name' => 'John Doe', 'institution' => 'LPU'];
                return response()->json($user, 200); // Sends JSON headers
            }
        }
        ```

---

## Unit 3: Controllers & Advanced Blade Views

### Expected Question 3: Blade Layout Inheritance & Custom Sections
*   **Question:** Build a Blade template layout file containing dynamic header, navigation links, main content section, and a sidebar. Inherit this layout on a dashboard child page that renders dynamic lists.
*   **Solution:**
    *   *Layout View (`resources/views/layouts/dashboard_layout.blade.php`):*
        ```html
        <!DOCTYPE html>
        <html>
        <head><title>@yield('title')</title></head>
        <body style="font-family: sans-serif; display: flex; flex-direction: column; min-height: 100vh;">
            <header style="background: #333; color: white; padding: 15px;">
                <h1>LPU Academic Portal</h1>
            </header>
            <div style="display: flex; flex: 1;">
                <nav style="width: 20%; background: #f4f4f4; padding: 15px;">
                    <a href="/dashboard">Dashboard</a><br><br>
                    <a href="/profile">Profile</a>
                </nav>
                <main style="flex: 1; padding: 20px;">
                    @yield('content')
                </main>
                <aside style="width: 25%; background: #fafafa; padding: 15px; border-left: 1px solid #ccc;">
                    @section('sidebar')
                        <h4>Default Sidebar Links</h4>
                        <p>Academic Rules & Regulations</p>
                    @show
                </aside>
            </div>
            <footer style="background: #ddd; text-align: center; padding: 10px;">
                <p>&copy; 2026 Lovely Professional University</p>
            </footer>
        </body>
        </html>
        ```
    *   *Child View (`resources/views/dashboard_main.blade.php`):*
        ```html
        @extends('layouts.dashboard_layout')

        @section('title', 'Student Home Dashboard')

        @section('content')
            <h2>Welcome Back, Student!</h2>
            <p>Your academic records are completely up to date.</p>
        @endsection

        @section('sidebar')
            @parent
            <h4>Special Notice</h4>
            <p style="color: red;">Practical exams start next week!</p>
        @endsection
        ```

---

## Unit 4: Dynamic URLs, Emails & Sessions

### Expected Question 4: Queued Mail Dispatch with Attachments
*   **Question:** Write the controller code to send an email with an attachment (a PDF file stored in local storage) using a Mailable class. Ensure the email dispatching is handled asynchronously using queues.
*   **Solution:**
    *   *Generate Mailable:* `php artisan make:mail CertificateMail`
    *   *Mailable Code (`app/Mail/CertificateMail.php`):*
        ```php
        namespace App\Mail;

        use Illuminate\Bus\Queueable;
        use Illuminate\Contracts\Queue\ShouldQueue;
        use Illuminate\Mail\Mailable;
        use Illuminate\Mail\Mailables\Attachment;
        use Illuminate\Mail\Mailables\Content;
        use Illuminate\Mail\Mailables\Envelope;

        class CertificateMail extends Mailable implements ShouldQueue
        {
            use Queueable;

            protected $pdfPath;

            public function __construct($pdfPath)
            {
                $this->pdfPath = $pdfPath;
            }

            public function envelope(): Envelope
            {
                return new Envelope(subject: 'Your Practical Exam Certificate');
            }

            public function content(): Content
            {
                return new Content(view: 'emails.certificate');
            }

            public function attachments(): array
            {
                return [
                    // Attach the PDF from local storage disk
                    Attachment::fromStorageDisk('public', $this->pdfPath)
                        ->as('Exam_Certificate.pdf')
                        ->withMime('application/pdf'),
                ];
            }
        }
        ```
    *   *Controller Send Action:*
        ```php
        use App\Mail\CertificateMail;
        use Illuminate\Support\Facades\Mail;

        public function sendCertificate() {
            $path = 'certificates/cert_102.pdf';
            // Pushes mail dispatch into DB jobs queue
            Mail::to('student@lpu.in')->queue(new CertificateMail($path));
            return "Certificate email dispatch has been queued.";
        }
        ```

---

## Unit 5: Form Validation & CSRF Protection

### Expected Question 5: Form Request Custom Rules
*   **Question:** Create a custom Form Request class to validate form inputs: `email` (required, valid domain format), `password` (required, min 8 chars), and `phone` (must start with digit 9). Implement custom validation error messages.
*   **Solution:**
    *   *Generate Request:* `php artisan make:request StoreProfileRequest`
    *   *Form Request (`app/Http/Requests/StoreProfileRequest.php`):*
        ```php
        namespace App\Http\Requests;

        use Illuminate\Foundation\Http\FormRequest;

        class StoreProfileRequest extends FormRequest
        {
            public function authorize(): bool
            {
                return true; // Set to true to allow validation execution
            }

            public function rules(): array
            {
                return [
                    'email' => 'required|email',
                    'password' => 'required|min:8',
                    'phone' => [
                        'required',
                        function($attribute, $value, $fail) {
                            if (substr($value, 0, 1) !== '9') {
                                $fail('The phone number must start with regional code 9.');
                            }
                        }
                    ]
                ];
            }

            public function messages(): array
            {
                return [
                    'email.required' => 'We need your email address to authenticate.',
                    'password.min' => 'Security policy: Passwords must be at least 8 characters long.'
                ];
            }
        }
        ```
    *   *Controller Code:*
        ```php
        public function saveProfile(StoreProfileRequest $request) {
            // Data is already validated. Retrieve safely:
            $data = $request->validated();
            return "Profile Data Validated and Approved!";
        }
        ```

---

## Unit 6: Migrations, Relationships & APIs

### Expected Question 6: Eloquent One-to-Many Relationship & JSON CRUD API
*   **Question:** Define an Eloquent relationship where a `Student` can have many `Marksheets`. Write migrations for both tables, define model relationship methods, and implement a JSON API route that retrieves a student along with their marksheets.
*   **Solution:**
    *   *1. Migration for Students table:*
        ```php
        Schema::create('students', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
        ```
    *   *2. Migration for Marksheets table (Foreign Key constraint):*
        ```php
        Schema::create('marksheets', function (Blueprint $table) {
            $table->id();
            $table->foreignId('student_id')->constrained()->onDelete('cascade');
            $table->string('subject');
            $table->integer('marks');
            $table->timestamps();
        });
        ```
    *   *3. Student Model Relationship (`app/Models/Student.php`):*
        ```php
        namespace App\Models;

        use Illuminate\Database\Eloquent\Model;

        class Student extends Model
        {
            protected $fillable = ['name'];

            // Define Relationship: One Student has Many Marksheets
            public function marksheets()
            {
                return $this->hasMany(Marksheet::class);
            }
        }
        ```
    *   *4. Marksheet Model Relationship (`app/Models/Marksheet.php`):*
        ```php
        namespace App\Models;

        use Illuminate\Database\Eloquent\Model;

        class Marksheet extends Model
        {
            protected $fillable = ['student_id', 'subject', 'marks'];

            // Define Inverse: Marksheet belongs to Student
            public function student()
            {
                return $this->belongsTo(Student::class);
            }
        }
        ```
    *   *5. JSON API Controller Endpoint:*
        ```php
        use App\Models\Student;

        Route::get('/api/student/{id}/marksheets', function($id) {
            // Fetch student and eager-load marksheets relationship to prevent N+1 query problem
            $student = Student::with('marksheets')->findOrFail($id);
            return response()->json($student, 200);
        });
        ```
