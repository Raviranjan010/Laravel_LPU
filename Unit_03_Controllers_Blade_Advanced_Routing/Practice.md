# Unit 3 Practice Lab: Controllers, Blade & Advanced Routing

## 🔬 Practical Objectives
*   Generate and configure a RESTful Resource Controller.
*   Implement layout inheritance templates in Blade.
*   Use stack directives to inject child scripts into layouts.
*   Configure route prefixes, namespaces, and group structures.

---

## Exercise 1: Building a RESTful Resource Controller

### Objective: Set up a CRUD workflow for managing a `Book` resource.

### Step-by-Step Instructions:

1.  **Generate the Resource Controller**:
    ```bash
    php artisan make:controller BookController --resource
    ```
2.  **Verify file location**: Open `app/Http/Controllers/BookController.php`. It contains empty index, create, store, show, edit, update, destroy methods.
3.  **Implement basic output logic** in the index and show methods:
    ```php
    public function index()
    {
        return "Displaying all books in the inventory.";
    }

    public function show($id)
    {
        return "Displaying details of Book ID: " . $id;
    }
    ```
4.  **Register the resource route** in `routes/web.php`:
    ```php
    use App\Http\Controllers\BookController;

    Route::resource('books', BookController::class);
    ```
5.  **Verify routes list** in the terminal:
    ```bash
    php artisan route:list
    ```
6.  **Test URLs in browser**:
    *   Visit `http://localhost:8000/books` -> Returns index content.
    *   Visit `http://localhost:8000/books/42` -> Returns "Displaying details of Book ID: 42".

---

## Exercise 2: Building Blade Inheritance Layouts

### Objective: Design a base layout containing script stacks and extend it in a dynamic child view.

### Step-by-Step Instructions:

1.  Create the master layout file: `resources/views/layouts/base.blade.php`:
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>LPU Library - @yield('title')</title>
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    </head>
    <body class="p-5">
        <header class="mb-4">
            <h2>LPU Academic Library</h2>
            <nav class="navbar navbar-expand-lg bg-light p-2 rounded">
                <a href="{{ url('/') }}" class="navbar-brand">Home</a>
                <a href="{{ route('books.index') }}" class="nav-link">Books Catalog</a>
            </nav>
        </header>

        <main>
            @yield('content')
        </main>

        <footer class="mt-5 text-muted">&copy; 2026 Lovely Professional University</footer>

        {{-- Dynamic scripts placeholder stack --}}
        @stack('scripts')
    </body>
    </html>
    ```
2.  Create the child view extending this layout: `resources/views/library_home.blade.php`:
    ```blade
    @extends('layouts.base')

    @section('title', 'Welcome Screen')

    @section('content')
        <div class="p-4 bg-light rounded-3">
            <h3>Welcome to LPU Library Services</h3>
            <p>Master layout has been extended successfully.</p>
            <button id="alertBtn" class="btn btn-primary">Click for Greeting</button>
        </div>
    @endsection

    @push('scripts')
        <script>
            document.getElementById('alertBtn').addEventListener('click', function() {
                alert('Hello from LPU library script stack!');
            });
        </script>
    @endpush
    ```
3.  Add route in `routes/web.php` to render the view:
    ```php
    Route::get('/library', function () {
        return view('library_home');
    });
    ```
4.  **Test**: Visit `http://localhost:8000/library` and click the button to trigger the stacked script.

---

## Exercise 3: Configuring Advanced Prefix Routing Groups

### Objective: Group routes under `/api/v1/library/` with numeric constraints.

### Step-by-Step Instructions:

1.  In `routes/web.php`, configure a prefix group:
    ```php
    Route::prefix('api/v1/library')->group(function () {
        
        // Matches URL: /api/v1/library/books
        Route::get('/books', function () {
            return response()->json(['status' => 'success', 'data' => ['Book A', 'Book B']]);
        });

        // Matches URL: /api/v1/library/book/{id} with numeric constraint
        Route::get('/book/{id}', function ($id) {
            return response()->json(['status' => 'success', 'book_id' => $id]);
        })->where('id', '[0-9]+');

    });
    ```
2.  **Test**:
    *   Visit `http://localhost:8000/api/v1/library/books` -> Returns JSON string.
    *   Visit `http://localhost:8000/api/v1/library/book/105` -> Returns success JSON.
    *   Visit `http://localhost:8000/api/v1/library/book/abc` -> Returns 404 error page.
