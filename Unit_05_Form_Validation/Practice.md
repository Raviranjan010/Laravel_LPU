# Unit 5 Practical Lab: Form Validation, CSRF & Custom Rules

This practical guide provides a step-by-step walkthrough to build a fully validated form in Laravel, implementing CSRF protection, method spoofing, error bag rendering, form input repopulation, custom Form Requests, and custom validation rules.

---

## Exercise 1: Build a Form with Inline Errors and Repopulation

We will create a form to create a "Project" with name, email, description, and budget.

### Step 1: Define the Routes
Add the routes to handle displaying and submitting the form in `routes/web.php`:

```php
use App\Http\Controllers\ProjectController;

Route::get('/projects/create', [ProjectController::class, 'create'])->name('projects.create');
Route::post('/projects', [ProjectController::class, 'store'])->name('projects.store');
```

### Step 2: Create the Controller
Generate the `ProjectController` using Artisan:
```bash
php artisan make:controller ProjectController
```

Implement the controller logic inside `app/Http/Controllers/ProjectController.php`:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class ProjectController extends Controller
{
    /**
     * Show the project creation form.
     */
    public function create()
    {
        return view('projects.create');
    }

    /**
     * Handle the form submission and validate inputs.
     */
    public function store(Request $request)
    {
        // Controller-level validation
        $validated = $request->validate([
            'name' => 'required|string|min:5|max:100',
            'email' => 'required|email',
            'description' => 'required|min:15',
            'budget' => 'required|numeric|min:1000|max:100000',
        ]);

        // If validation passes, process the project creation (simulated here)
        return back()->with('success', 'Project validation passed! ' . $validated['name'] . ' is ready to build.');
    }
}
```

### Step 3: Create the Blade Template
Create `resources/views/projects/create.blade.php`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Create Project</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body class="bg-light py-5">
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-6">
                <!-- Success Message -->
                @if(session('success'))
                    <div class="alert alert-success shadow-sm mb-4">
                        {{ session('success') }}
                    </div>
                @endif

                <div class="card shadow-sm border-0">
                    <div class="card-header bg-dark text-white text-center py-3">
                        <h4 class="mb-0">Create New Project</h4>
                    </div>
                    <div class="card-body p-4">
                        <form action="{{ route('projects.store') }}" method="POST">
                            <!-- CSRF Security Token -->
                            @csrf

                            <!-- Project Name -->
                            <div class="mb-3">
                                <label for="name" class="form-label">Project Name</label>
                                <input type="text" name="name" id="name" 
                                       class="form-control @error('name') is-invalid @enderror" 
                                       value="{{ old('name') }}">
                                @error('name')
                                    <div class="invalid-feedback">{{ $message }}</div>
                                @enderror
                            </div>

                            <!-- Contact Email -->
                            <div class="mb-3">
                                <label for="email" class="form-label">Client Contact Email</label>
                                <input type="text" name="email" id="email" 
                                       class="form-control @error('email') is-invalid @enderror" 
                                       value="{{ old('email') }}">
                                @error('email')
                                    <div class="invalid-feedback">{{ $message }}</div>
                                @enderror
                            </div>

                            <!-- Description -->
                            <div class="mb-3">
                                <label for="description" class="form-label">Project Description</label>
                                <textarea name="description" id="description" rows="4" 
                                          class="form-control @error('description') is-invalid @enderror">{{ old('description') }}</textarea>
                                @error('description')
                                    <div class="invalid-feedback">{{ $message }}</div>
                                @enderror
                            </div>

                            <!-- Budget -->
                            <div class="mb-3">
                                <label for="budget" class="form-label">Budget ($ USD)</label>
                                <input type="number" name="budget" id="budget" 
                                       class="form-control @error('budget') is-invalid @enderror" 
                                       value="{{ old('budget') }}">
                                @error('budget')
                                    <div class="invalid-feedback">{{ $message }}</div>
                                @enderror
                            </div>

                            <!-- Submit -->
                            <button type="submit" class="btn btn-dark w-100">Submit Project Application</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</body>
</html>
```

---

## Exercise 2: Implementing a Form Request and Method Spoofing

We will create a form to edit an existing task using a dedicated Form Request class, showcasing `PUT` method spoofing.

### Step 1: Generate Form Request Class
Run this Artisan command to generate the Form Request:
```bash
php artisan make:request UpdateTaskRequest
```

### Step 2: Implement the Rules inside `UpdateTaskRequest.php`
Open `app/Http/Requests/UpdateTaskRequest.php` and configure authorization, rules, and custom failure messages:

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class UpdateTaskRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     */
    public function authorize(): bool
    {
        // Allow anyone to access for lab purposes
        return true; 
    }

    /**
     * Get the validation rules that apply to the request.
     */
    public function rules(): array
    {
        return [
            'task_title' => 'required|string|min:5|max:150',
            'due_date' => 'required|date|after:today',
            'priority' => 'required|in:low,medium,high',
        ];
    }

    /**
     * Get the custom validation error messages.
     */
    public function messages(): array
    {
        return [
            'task_title.required' => 'You must name this task.',
            'due_date.after' => 'The due date must be a future date.',
            'priority.in' => 'Please select a valid priority (low, medium, or high).',
        ];
    }
}
```

### Step 3: Implement the Edit Form Routes and Controller Method
In `routes/web.php`:
```php
use App\Http\Controllers\TaskController;

Route::get('/tasks/{id}/edit', [TaskController::class, 'edit'])->name('tasks.edit');
Route::put('/tasks/{id}', [TaskController::class, 'update'])->name('tasks.update');
```

Create the controller `TaskController.php`:
```bash
php artisan make:controller TaskController
```

Implement inside `app/Http/Controllers/TaskController.php`:
```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\UpdateTaskRequest;

class TaskController extends Controller
{
    public function edit($id)
    {
        // Mocking an existing task model
        $task = (object)[
            'id' => $id,
            'title' => 'Implement Auth System',
            'due_date' => date('Y-m-d', strtotime('+3 days')),
            'priority' => 'medium'
        ];

        return view('tasks.edit', compact('task'));
    }

    // Notice we type-hint the custom UpdateTaskRequest instead of Request
    public function update(UpdateTaskRequest $request, $id)
    {
        // Execution will only reach here if validation passes
        $validated = $request->validated();

        return back()->with('success', 'Task ' . $id . ' successfully updated with validated data!');
    }
}
```

### Step 4: Create the Blade Template with Method Spoofing
Create `resources/views/tasks/edit.blade.php`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Edit Task</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body class="bg-light py-5">
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-6">
                <!-- Success Message -->
                @if(session('success'))
                    <div class="alert alert-success shadow-sm mb-4">
                        {{ session('success') }}
                    </div>
                @endif

                <div class="card shadow-sm border-0">
                    <div class="card-header bg-success text-white py-3">
                        <h4 class="mb-0">Edit Task #{{ $task->id }}</h4>
                    </div>
                    <div class="card-body p-4">
                        <form action="{{ route('tasks.update', $task->id) }}" method="POST">
                            <!-- CSRF Token -->
                            @csrf
                            
                            <!-- HTTP Method Spoofing for PUT -->
                            @method('PUT')

                            <!-- Task Title -->
                            <div class="mb-3">
                                <label for="task_title" class="form-label">Task Title</label>
                                <input type="text" name="task_title" id="task_title" 
                                       class="form-control @error('task_title') is-invalid @enderror" 
                                       value="{{ old('task_title', $task->title) }}">
                                @error('task_title')
                                    <div class="invalid-feedback">{{ $message }}</div>
                                @enderror
                            </div>

                            <!-- Due Date -->
                            <div class="mb-3">
                                <label for="due_date" class="form-label">Due Date</label>
                                <input type="date" name="due_date" id="due_date" 
                                       class="form-control @error('due_date') is-invalid @enderror" 
                                       value="{{ old('due_date', $task->due_date) }}">
                                @error('due_date')
                                    <div class="invalid-feedback">{{ $message }}</div>
                                @enderror
                            </div>

                            <!-- Priority -->
                            <div class="mb-3">
                                <label for="priority" class="form-label">Priority</label>
                                <select name="priority" id="priority" class="form-select @error('priority') is-invalid @enderror">
                                    <option value="low" {{ old('priority', $task->priority) == 'low' ? 'selected' : '' }}>Low</option>
                                    <option value="medium" {{ old('priority', $task->priority) == 'medium' ? 'selected' : '' }}>Medium</option>
                                    <option value="high" {{ old('priority', $task->priority) == 'high' ? 'selected' : '' }}>High</option>
                                </select>
                                @error('priority')
                                    <div class="invalid-feedback">{{ $message }}</div>
                                @enderror
                            </div>

                            <button type="submit" class="btn btn-success w-100">Update Task Details</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</body>
</html>
```

---

## Exercise 3: Implementing a Custom Validation Rule

We will build a custom validation rule to verify that a text input has exactly a specified number of words (e.g. checking if a resume summary has at least 5 words).

### Step 1: Generate Rule Class
Run the Artisan command:
```bash
php artisan make:rule MinWords
```

### Step 2: Implement the logic inside `app/Rules/MinWords.php`
Open the generated file and edit the `validate` method:

```php
<?php

namespace App\Rules;

use Closure;
use Illuminate\Contracts\Validation\ValidationRule;

class MinWords implements ValidationRule
{
    protected $minWords;

    /**
     * Create a new rule instance.
     */
    public function __construct(int $minWords = 5)
    {
        $this->minWords = $minWords;
    }

    /**
     * Run the validation rule.
     */
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        // Count words in the string
        $wordCount = str_word_count($value);

        if ($wordCount < $this->minWords) {
            $fail("The :attribute must contain at least {$this->minWords} words. You entered only {$wordCount} words.");
        }
    }
}
```

### Step 3: Implement in a Controller Action
Use this rule in your controller:

```php
use App\Rules\MinWords;
use Illuminate\Http\Request;

Route::post('/profile/summary', function (Request $request) {
    $request->validate([
        'bio' => ['required', 'string', new MinWords(10)], // Bio must be at least 10 words
    ]);

    return "Validation passed! Summary saved successfully.";
});
```

---

## 🧪 Verification Check List
1. Start the local server: `php artisan serve`.
2. Visit `http://127.0.0.1:8000/projects/create`. Submit the form empty, check if inline errors render, then input wrong fields to check.
3. Visit `http://127.0.0.1:8000/tasks/1/edit`. Inspect the source code of the generated webpage and verify that `@method('PUT')` outputted `<input type="hidden" name="_method" value="PUT">`.
4. Try submitting an expired page or removing the `@csrf` code snippet from the HTML forms to intentionally verify the `419 Page Expired` handling page.
