# Solved Laravel Practical Exam Paper D

This document contains full, complete, and exam-ready code solutions for all 10 questions of Practical Exam Paper D (from the images `WhatsApp Image 2026-06-05 at 00.39.59.jpeg` and `WhatsApp Image 2026-06-05 at 00.40.01.jpeg`).

---

## Q1: Job Portal Registration & Guest Redirect

### Question
Create a job portal registration form with fields for `name`, `email`, `password`, and `resume` (PDF only). The form must validate inputs, store data in the `users` table, and redirect guests to `/login` if already authenticated.
*   **a)** Define the route for `/register` (GET/POST) with `guest` middleware.
*   **b)** Create a Blade template `register.blade.php` with CSRF token and file upload.
*   **c)** Write a `RegistrationController` to handle validation (email uniqueness, PDF validation) and store data.
*   **d)** Generate a migration for the `users` table including a `resume_path` string column.

### Solution

#### 1. Define Migration (d)
```php
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique();
    $table->string('password');
    $table->string('resume_path')->nullable(); // Column for PDF paths
    $table->timestamps();
});
```

#### 2. Define Routes (`routes/web.php`) (a)
```php
use App\Http\Controllers\RegistrationController;

// Apply guest middleware so logged-in users are redirected to home/login
Route::middleware('guest')->group(function() {
    Route::get('/register', [RegistrationController::class, 'create'])->name('register');
    Route::post('/register', [RegistrationController::class, 'store']);
});
```

#### 3. Create Blade Template (`resources/views/register.blade.php`) (b)
```html
<form method="POST" action="/register" enctype="multipart/form-data">
    @csrf
    Name: <input type="text" name="name" value="{{ old('name') }}"><br><br>
    Email: <input type="email" name="email" value="{{ old('email') }}"><br><br>
    Password: <input type="password" name="password"><br><br>
    Resume (PDF only, Max 2MB): <input type="file" name="resume"><br><br>
    <button type="submit">Register</button>
</form>
```

#### 4. Create RegistrationController (c)
File: `app/Http/Controllers/RegistrationController.php`
```php
namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;

class RegistrationController extends Controller
{
    public function create() {
        return view('register');
    }

    public function store(Request $request) {
        $request->validate([
            'name' => 'required|string|max:50',
            'email' => 'required|email|unique:users,email',
            'password' => 'required|min:8',
            'resume' => 'required|file|mimes:pdf|max:2048' // Max 2MB
        ]);

        $resumePath = null;
        if ($request->hasFile('resume')) {
            $resumePath = $request->file('resume')->store('resumes', 'public');
        }

        User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
            'resume_path' => $resumePath
        ]);

        return redirect('/login')->with('success', 'Registration Successful.');
    }
}
```

---

## Q2: Gallery Image Upload (Max 5MB Validation & Display)

### Question
Create the app File Upload & Gallery. Users upload images to `/gallery`. Images are stored in `storage/app/public/gallery`, and their paths are saved in the `galleries` table.
*   **a)** Create a route `/gallery/upload` (POST) with `auth` middleware.
*   **b)** Write a `GalleryController@store` method to validate images (max 5MB) and store paths.
*   **c)** Design a Blade view `gallery.blade.php` to display images using asset URLs.
*   **d)** Generate a migration for the `galleries` table (`user_id`, `image_path`).

### Solution

#### 1. Migration (d)
```php
Schema::create('galleries', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->string('image_path');
    $table->timestamps();
});
```

#### 2. Define Routes (`routes/web.php`) (a)
```php
use App\Http\Controllers\GalleryController;

Route::middleware('auth')->group(function() {
    Route::get('/gallery', [GalleryController::class, 'index']);
    Route::post('/gallery/upload', [GalleryController::class, 'store']);
});
```

#### 3. Create Controller (b)
File: `app/Http/Controllers/GalleryController.php`
```php
namespace App\Http\Controllers;

use App\Models\Gallery;
use Illuminate\Http\Request;

class GalleryController extends Controller
{
    public function index() {
        $images = Gallery::where('user_id', auth()->id())->get();
        return view('gallery', compact('images'));
    }

    public function store(Request $request) {
        $request->validate([
            'photo' => 'required|image|mimes:jpeg,png,webp|max:5120' // 5MB limit
        ]);

        if ($request->hasFile('photo')) {
            $path = $request->file('photo')->store('gallery', 'public');

            Gallery::create([
                'user_id' => auth()->id(),
                'image_path' => $path
            ]);

            return back()->with('success', 'Image added successfully!');
        }
    }
}
```

#### 4. Blade Gallery Template (`resources/views/gallery.blade.php`) (c)
```html
<form method="POST" action="/gallery/upload" enctype="multipart/form-data">
    @csrf
    Upload Photo: <input type="file" name="photo" required>
    <button type="submit">Upload</button>
</form>

<hr>
<h3>Your Image Gallery:</h3>
<div style="display:flex; flex-wrap:wrap; gap:15px;">
    @foreach($images as $img)
        <div>
            <!-- Render via asset() dynamic public link -->
            <img src="{{ asset('storage/' . $img->image_path) }}" width="150" height="150" style="object-fit:cover;">
        </div>
    @endforeach
</div>
```

---

## Q3: Real-Time Notification System (Shared View Data)

### Question
Create a Real-time Notifications system where Logged-in users receive notifications stored in the `notifications` table. Notifications are displayed in a dropdown using Blade.
*   **a)** Create a middleware to share unread notifications with all Blade views.
*   **b)** Design a Blade component `notification-dropdown.blade.php`.
*   **c)** Write a migration for `notifications` (`user_id`, `message`, `read_at`).
*   **d)** Add a route `/notifications/mark-as-read` to update `read_at`.

### Solution

#### 1. Define Migration (c)
```php
Schema::create('notifications', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->string('message');
    $table->timestamp('read_at')->nullable();
    $table->timestamps();
});
```

#### 2. Create Notifications Middleware (a)
Generate using: `php artisan make:middleware ShareNotifications`

File: `app/Http/Middleware/ShareNotifications.php`
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\View;
use App\Models\Notification;

class ShareNotifications
{
    public function handle(Request $request, Closure $next)
    {
        if (auth()->check()) {
            $unreadNotifications = Notification::where('user_id', auth()->id())
                ->whereNull('read_at')
                ->get();
            // Bind globally to all view renders
            View::share('unreadNotifications', $unreadNotifications);
        }
        return $next($request);
    }
}
```

#### 3. Define Routes (`routes/web.php`) (d)
```php
Route::middleware(['auth', 'share.notifications'])->group(function() {
    Route::post('/notifications/mark-as-read', function() {
        Notification::where('user_id', auth()->id())
            ->whereNull('read_at')
            ->update(['read_at' => now()]);
        return response()->json(['success' => true]);
    });
});
```

#### 4. Design Dropdown Blade Component (b)
File: `resources/views/components/notification-dropdown.blade.php`
```html
<div class="dropdown">
    <button>Notifications ({{ count($unreadNotifications ?? []) }})</button>
    <div class="dropdown-content">
        @if(isset($unreadNotifications) && count($unreadNotifications) > 0)
            @foreach($unreadNotifications as $notif)
                <p>{{ $notif->message }}</p>
            @endforeach
            <button onclick="markAllRead()">Mark all as read</button>
        @else
            <p>No new notifications.</p>
        @endif
    </div>
</div>

<script>
function markAllRead() {
    fetch('/notifications/mark-as-read', {
        method: 'POST',
        headers: { 'X-CSRF-TOKEN': '{{ csrf_token() }}' }
    }).then(() => window.location.reload());
}
</script>
```

---

## Q4: Admin Blog Post Management (Route Group & Middleware)

### Question
Create an application Blog Post Management (Admin Panel). An admin must manage blog posts via `/admin/posts`. All routes require authentication and a middleware confirming admin role (`is_admin`).
*   **a)** Create a route group with prefix `admin`, middleware `auth` and `is_admin`.
*   **b)** Use a RESTful `PostController` (index, create, store, edit, update, delete).
*   **c)** Design Blade templates `posts/index.blade.php` (listing posts) and `posts/create.blade.php` (form with TinyMCE editor).
*   **d)** Write a migration for the `posts` table (`title`, `content`, `user_id`).

### Solution

#### 1. Define Migration (d)
```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->text('content');
    $table->foreignId('user_id')->constrained();
    $table->timestamps();
});
```

#### 2. Create `is_admin` Middleware (a)
File: `app/Http/Middleware/EnsureUserIsAdmin.php`
```php
namespace App\Http\Middleware;

use Closure;

class EnsureUserIsAdmin
{
    public function handle($request, Closure $next)
    {
        if (auth()->check() && auth()->user()->is_admin) {
            return $next($request);
        }
        abort(403, 'Unauthorized access.');
    }
}
```

#### 3. Define Route Groups (`routes/web.php`) (a)
```php
use App\Http\Controllers\PostController;

Route::middleware(['auth', 'is_admin'])
    ->prefix('admin')
    ->group(function() {
        Route::resource('posts', PostController::class);
    });
```

#### 4. Design Views (TinyMCE editor) (c)
*   **Create Template (`resources/views/posts/create.blade.php`):**
    ```html
    <head>
        <!-- Load TinyMCE from CDN -->
        <script src="https://cdn.tiny.cloud/1/no-api-key/tinymce/6/tinymce.min.js"></script>
        <script>tinymce.init({ selector:'#editor' });</script>
    </head>
    <form method="POST" action="/admin/posts">
        @csrf
        Title: <input type="text" name="title"><br><br>
        Content: <textarea id="editor" name="content"></textarea><br><br>
        <button type="submit">Publish</button>
    </form>
    ```

---

## Q5: Task Priority Updates & Logs Middleware

### Question
Create a Task manager app allowing users to update task priority via `/tasks/{id}/priority`. Priority must be 1-5, and changes logged in a `task_logs` table.
*   **a)** Define a PUT route `/tasks/{task}/priority` with parameter binding.
*   **b)** Create a `TaskController@updatePriority` method with validation.
*   **c)** Add a `priority` integer column via migration and a `task_logs` table (`task_id`, `old_priority`, `new_priority`).
*   **d)** Use Blade components for priority dropdowns.

### Solution

#### 1. Migrations (c)
*   **Tasks Priority Update:**
    ```php
    Schema::table('tasks', function (Blueprint $table) {
        $table->integer('priority')->default(3); // Scale 1-5
    });
    ```
*   **Task Logs Table:**
    ```php
    Schema::create('task_logs', function (Blueprint $table) {
        $table->id();
        $table->foreignId('task_id');
        $table->integer('old_priority');
        $table->integer('new_priority');
        $table->timestamps();
    });
    ```

#### 2. Define Routes (`routes/web.php`) (a)
```php
use App\Http\Controllers\TaskController;

Route::put('/tasks/{task}/priority', [TaskController::class, 'updatePriority']);
```

#### 3. Controller Actions (b)
File: `app/Http/Controllers/TaskController.php`
```php
namespace App\Http\Controllers;

use App\Models\Task;
use App\Models\TaskLog;
use Illuminate\Http\Request;

class TaskController extends Controller
{
    public function updatePriority(Request $request, Task $task)
    {
        // Validate priority scale between 1 and 5
        $request->validate([
            'priority' => 'required|integer|between:1,5'
        ]);

        $oldPriority = $task->priority;
        $newPriority = $request->input('priority');

        $task->update(['priority' => $newPriority]);

        // Write log entry trigger
        TaskLog::create([
            'task_id' => $task->id,
            'old_priority' => $oldPriority,
            'new_priority' => $newPriority
        ]);

        return back()->with('status', 'Task priority updated and logged.');
    }
}
```

---

## Q6: MongoDB Integration for Page View Analytics

### Question
Create a Laravel app (MongoDB Integration for Analytics) which tracks page views in MongoDB. A dashboard at `/analytics` displays the data.
*   **a)** Configure Laravel to use MongoDB via `jenssegers/laravel-mongodb`.
*   **b)** Create a `PageView` model and MongoDB collection (`page_views`).
*   **c)** Write a middleware to log each request to MongoDB.
*   **d)** Build a Blade view `analytics.blade.php` showing visit counts.

### Solution

#### 1. Configuration (a)
*   Register MongoDB connection inside `config/database.php`:
    ```php
    'mongodb' => [
        'driver' => 'mongodb',
        'host' => env('DB_MONGO_HOST', '127.0.0.1'),
        'port' => env('DB_MONGO_PORT', 27017),
        'database' => env('DB_MONGO_DATABASE', 'analytics_db'),
    ],
    ```

#### 2. Create PageView Model (b)
File: `app/Models/PageView.php`
```php
namespace App\Models;

// Extends MongoDB Eloquent model instead of standard SQL Eloquent model
use Jenssegers\Mongodb\Eloquent\Model;

class PageView extends Model
{
    protected $connection = 'mongodb'; // Force MongoDB driver usage
    protected $collection = 'page_views';
    protected $fillable = ['url', 'ip_address', 'visited_at'];
}
```

#### 3. Log visits Middleware (c)
File: `app/Http/Middleware/LogPageView.php`
```php
namespace App\Http\Middleware;

use Closure;
use App\Models\PageView;

class LogPageView
{
    public function handle($request, Closure $next)
    {
        PageView::create([
            'url' => $request->fullUrl(),
            'ip_address' => $request->ip(),
            'visited_at' => now()->toDateTimeString()
        ]);
        return $next($request);
    }
}
```

---

## Q7: AJAX Social Follow / Unfollow System

### Question
Create a Social Media Follow System where Users can follow/unfollow others via a button. The action is handled via AJAX/JSON, and the count updates dynamically.
*   **a)** Create a `FollowController` with `follow` and `unfollow` methods.
*   **b)** Define routes `/follow/{user}` and `/unfollow/{user}` (POST).
*   **c)** Use Blade components for the follow button and counter.
*   **d)** Generate pivot table `followers` (`follower_id`, `followed_id`).

### Solution

#### 1. Define Pivot Table Migration (d)
```php
Schema::create('followers', function (Blueprint $table) {
    $table->id();
    $table->foreignId('follower_id')->constrained('users');
    $table->foreignId('followed_id')->constrained('users');
    $table->timestamps();
});
```

#### 2. Create FollowController (a & b)
*   *Routes:*
    ```php
    Route::post('/follow/{user}', [FollowController::class, 'follow']);
    Route::post('/unfollow/{user}', [FollowController::class, 'unfollow']);
    ```
*   *Controller Code:*
    ```php
    namespace App\Http\Controllers;

    use App\Models\User;
    use Illuminate\Http\Request;

    class FollowController extends Controller {
        public function follow(User $user) {
            auth()->user()->following()->attach($user->id);
            return response()->json(['success' => true, 'count' => $user->followers()->count()]);
        }

        public function unfollow(User $user) {
            auth()->user()->following()->detach($user->id);
            return response()->json(['success' => true, 'count' => $user->followers()->count()]);
        }
    }
    ```

---

## Q8: Route Constraints, Components, and Log Middleware

### Question
*   **a)** Define a route `/products/{category}` with a regex constraint (`^[a-z]+$`).
*   **b)** Create a `ProductController@showByCategory` method to fetch products from the `products` table.
*   **c)** Build a Blade component `product-card.blade.php` to display product details.
*   **d)** Use a middleware to log all product page visits to a `logs` table.

### Solution

#### 1. Define Route with Regex Constraint (a)
```php
use App\Http\Controllers\ProductController;

Route::get('/products/{category}', [ProductController::class, 'showByCategory'])
    ->where('category', '[a-z]+') // Regex: Only lowercase letters
    ->middleware('log.product.visit');
```

#### 2. Create Logging Middleware (d)
File: `app/Http/Middleware/LogProductVisit.php`
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\DB;

class LogProductVisit
{
    public function handle($request, Closure $next)
    {
        DB::table('logs')->insert([
            'url' => $request->path(),
            'visited_at' => now()
        ]);
        return $next($request);
    }
}
```

#### 3. Controller Method (b)
```php
public function showByCategory($category) {
    $products = Product::where('category', $category)->get();
    return view('products.category', compact('products', 'category'));
}
```

---

## Q9: Language Selection Cookie Translator Middleware

### Question
A news website supports English (`en`) and French (`fr`). Users select a language stored in a cookie, and all Blade templates display translated content.
*   **a)** Create a middleware `SetLocale` to read the cookie and set `app.locale`.
*   **b)** Define routes `/lang/{locale}` to switch languages (store cookie).
*   **c)** Modify the Blade layout `app.blade.php` to use `__()` helper for text.
*   **d)** Add a `lang` column to the `users` table to store preferences.

### Solution

#### 1. Create Middleware (a)
File: `app/Http/Middleware/SetLocaleFromCookie.php`
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\App;

class SetLocaleFromCookie
{
    public function handle($request, Closure $next)
    {
        // Read language preference from cookie
        $locale = $request->cookie('language_preference', 'en');
        App::setLocale($locale);
        return $next($request);
    }
}
```

#### 2. Define Language Switching Routes (b & d)
```php
Route::get('/lang/{locale}', function($locale) {
    if (in_array($locale, ['en', 'fr'])) {
        if (auth()->check()) {
            // Update lang column in users table
            auth()->user()->update(['lang' => $locale]);
        }
        // Save choice for 1 year
        return redirect()->back()->cookie('language_preference', $locale, 525600);
    }
    return back();
});
```

---

## Q10: Todo App REST API using Sanctum

### Question
Create A Todo app exposing API endpoints (`/api/todos`) for CRUD operations. All responses are JSON, and routes use token authentication.
*   **a)** Define API routes in `routes/api.php` with `auth:sanctum` middleware.
*   **b)** Create a `TodoController` resource with validation for `title` (required) and `status` (pending/completed).
*   **c)** Use Eloquent to interact with the `todos` table.
*   **d)** Write a Blade view `todos/index.blade.php` to display todos fetched via AJAX/JSON.

### Solution

#### 1. Define API Routes (`routes/api.php`) (a)
```php
use App\Http\Controllers\Api\TodoApiController;

Route::middleware('auth:sanctum')->group(function() {
    Route::apiResource('todos', TodoApiController::class);
});
```

#### 2. API Resource Controller (b & c)
File: `app/Http/Controllers/Api/TodoApiController.php`
```php
namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Todo;
use Illuminate\Http\Request;

class TodoApiController extends Controller
{
    public function index() {
        return response()->json(Todo::all(), 200);
    }

    public function store(Request $request) {
        $request->validate([
            'title' => 'required|string',
            'status' => 'required|in:pending,completed'
        ]);

        $todo = Todo::create($request->all());
        return response()->json($todo, 201);
    }
}
```
