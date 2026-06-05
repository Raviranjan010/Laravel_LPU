# Unit 2 Practice Lab: Routing, Views & Responses

## 🔬 Practical Objectives
*   Build routes with required and optional parameters.
*   Enforce parameter constraints using regular expressions.
*   Render views, pass dynamic arrays, and share data.
*   Output custom HTTP responses containing headers and cookies.

---

## Exercise 1: Defining Advanced Routes with Constraints

### Objective: Create routes under `routes/web.php` that validate user parameters and redirect to named targets.

### Step-by-Step Instructions:

1.  Open your project's web routes file: `routes/web.php`.
2.  Add a route that accepts a user ID, constrained to numeric values only:
    ```php
    Route::get('/student/profile/{id}', function ($id) {
        return "Student Profile ID: " . $id;
    })->where('id', '[0-9]+')->name('student.profile');
    ```
3.  Add a route that accepts a course code, constrained to alphanumeric characters only:
    ```php
    Route::get('/course/{code}', function ($code) {
        return "Course Code: " . strtoupper($code);
    })->where('code', '[A-Za-z0-9]+');
    ```
4.  Add a route that demonstrates redirections to named routes:
    ```php
    Route::get('/go-to-student/{id}', function ($id) {
        return redirect()->route('student.profile', ['id' => $id]);
    });
    ```
5.  **Verify testing in browser**:
    *   `http://localhost:8000/student/profile/101` -> Displays "Student Profile ID: 101"
    *   `http://localhost:8000/student/profile/abc` -> Throws a **404 Not Found** error
    *   `http://localhost:8000/go-to-student/55` -> Automatically redirects to `/student/profile/55`

---

## Exercise 2: Building Views & Passing Arrays

### Objective: Pass cricket player variables using `compact()` and render list.

### Step-by-Step Instructions:

1.  Add the route in `routes/web.php`:
    ```php
    Route::get('/players', function () {
        $players = ['Virat Kohli', 'Rohit Sharma', 'Jasprit Bumrah', 'K.L. Rahul'];
        $title = 'LPU Sports Board';
        return view('players', compact('players', 'title'));
    });
    ```
2.  Create a view file: `resources/views/players.blade.php`:
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>{{ $title }}</title>
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    </head>
    <body class="p-5">
        <div class="card p-4 mx-auto" style="max-width: 500px;">
            <h3>{{ $title }}</h3>
            <p>Welcome to <strong>{{ $school_name }}</strong> athletic list:</p>
            <ul class="list-group">
                @foreach($players as $player)
                    <li class="list-group-item">{{ $player }}</li>
                @endforeach
            </ul>
        </div>
    </body>
    </html>
    ```
3.  Open `app/Providers/AppServiceProvider.php` and share the global variable `school_name`:
    ```php
    use Illuminate\Support\Facades\View;

    public function boot()
    {
        View::share('school_name', 'Lovely Professional University');
    }
    ```
4.  **Test**: Visit `http://localhost:8000/players`.

---

## Exercise 3: Generating Custom Responses & Cookies

### Objective: Set user preferences (theme) via a cookie response.

### Step-by-Step Instructions:

1.  Add a route to set a preference cookie:
    ```php
    Route::get('/set-theme/{color}', function ($color) {
        return response("Theme color '{$color}' has been saved in cookies.")
                    ->cookie('theme', $color, 60) // valid for 60 minutes
                    ->header('X-App-Theme', $color);
    });
    ```
2.  Add a route to retrieve and show the active cookie:
    ```php
    use Illuminate\Http\Request;

    Route::get('/get-theme', function (Request $request) {
        $theme = $request->cookie('theme', 'default-light');
        return "Current active theme: " . $theme;
    });
    ```
3.  **Test**:
    *   Visit `http://localhost:8000/set-theme/dark`
    *   Visit `http://localhost:8000/get-theme` -> Displays "Current active theme: dark"
