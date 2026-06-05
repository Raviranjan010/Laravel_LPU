# Unit 5 Question Bank: Form Validation & CSRF Protection

## 2-Mark Questions

### Q1. What is CSRF (Cross-Site Request Forgery), and how does Laravel prevent it?
**Answer:**
CSRF is an attack where a malicious website tricks a user's browser into executing unauthorized requests on a web application where the user is currently authenticated. Laravel prevents this by automatically generating a cryptographic token for each active user session. The `VerifyCsrfToken` middleware verifies that the token submitted with POST, PUT, PATCH, or DELETE requests matches the token stored in the session.

### Q2. How is method spoofing implemented in Laravel, and why is it necessary?
**Answer:**
Standard web browsers only support `GET` and `POST` methods in HTML forms. Since RESTful designs require other HTTP verbs like `PUT`, `PATCH`, and `DELETE`, Laravel implements **method spoofing**. To spoof these methods, you include a hidden `_method` input containing the target HTTP verb:
```html
<form action="/posts/1" method="POST">
    @csrf
    @method('DELETE')
</form>
```

### Q3. What occurs behind the scenes in Laravel when controller-level validation fails?
**Answer:**
If the validation rules fail:
1. An `ValidationException` is thrown.
2. Laravel automatically catches the exception and intercepts the execution flow.
3. It stores the validation errors and the user's input in the session flash data.
4. The user is redirected back to their previous page with error messages populated in the shared `$errors` bag and previous inputs available via `old()`.
5. For AJAX requests, it returns a JSON response containing the errors with a `422 Unprocessable Content` HTTP status code.

### Q4. Write the syntax to display a validation error message inline for a specific input field named `username`.
**Answer:**
You can use the `@error` Blade directive to check for errors and display the message inline:
```html
<input type="text" name="username" value="{{ old('username') }}">
@error('username')
    <span class="text-danger">{{ $message }}</span>
@enderror
```

### Q5. What is the command to generate a custom validation rule class, and where is it placed by default?
**Answer:**
The Artisan command is:
```bash
php artisan make:rule MinWords
```
By default, this command generates the rule class inside the `app/Rules/` directory as `app/Rules/MinWords.php`.

---

## 5-Mark Questions

### Q6. Differentiate between In-Controller Validation and Form Request Validation in Laravel.
**Answer:**

| Comparison Factor | In-Controller Validation | Form Request Validation |
|-------------------|--------------------------|-------------------------|
| **Location** | Defined directly inside the controller action method. | Extracted into a dedicated class in `app/Http/Requests`. |
| **Separation of Concerns** | Low. Mixes HTTP routing/handling with validation rules. | High. Business validation logic is decoupled from controllers. |
| **Reusability** | Low. Hard to reuse across multiple controllers or API routes. | High. Can be type-hinted across multiple controllers and requests. |
| **Authorization** | Must be manually checked inside the controller before validation. | Includes a dedicated `authorize()` method executed before validation rules. |
| **Clarity** | Good for small, simple apps. Declutters the folder hierarchy. | Essential for complex, enterprise-level forms. Clean and modular. |

### Q7. Explain how Laravel handles old input repopulation and how the `$errors` variable is shared across views.
**Answer:**
#### 1. Input Repopulation (`old()` helper):
During validation failure, Laravel flashes the inputs of the request to the session before redirecting back. To retrieve these values, Laravel provides the helper function `old()`.
* **Syntax**: `<input type="text" name="email" value="{{ old('email') }}">`
* **Default Value**: You can supply a default fallback: `old('role', $user->role)`.

#### 2. Shared `$errors` Variable:
* All views in Laravel receive a shared variable named `$errors` which is an instance of `Illuminate\Support\ViewErrorBag`.
* This occurs via the `ShareErrorsFromSession` middleware (contained in the `web` middleware group).
* Because of this, developers do **not** need to manually pass `$errors` from the controller. It is always defined and safe to call (even on successful requests where it will simply be empty).

### Q8. Describe the anatomy of a Form Request class generated using `php artisan make:request`.
**Answer:**
A custom Form Request extends `Illuminate\Foundation\Http\FormRequest` and contains two main methods by default:

1. **`authorize()`**:
   * Evaluates if the current user is permitted to perform the request action.
   * If it returns `false`, a `403 Forbidden` response is automatically generated.
   * Example:
     ```php
     public function authorize(): bool {
         return $this->user()->can('edit-posts');
     }
     ```

2. **`rules()`**:
   * Returns an associative array of keys (input names) and values (validation rules).
   * Example:
     ```php
     public function rules(): array {
         return [
             'title' => 'required|max:255',
             'body' => 'required|min:10',
         ];
     }
     ```

3. **`messages()` (Optional override)**:
   * You can declare this method to define custom error messages for the validation rules:
     ```php
     public function messages(): array {
         return [
             'title.required' => 'The title field is absolutely mandatory.',
         ];
     }
     ```

---

## 10-Mark Questions

### Q9. Explain the implementation of Custom Validation Rules in Laravel. Detail both the modern contract-based approach (using `ValidationRule`) and inline closure validation.
**Answer:**

Laravel provides two primary mechanisms to create custom validation rules when built-in validation rules do not suffice.

#### 1. Dedicated Custom Rule Class (Recommended for Reusability)
To create a reusable validation rule, run the Artisan command:
```bash
php artisan make:rule Uppercase
```
This generates a file implementing the `Illuminate\Contracts\Validation\ValidationRule` interface. The class contains a single `validate()` method:

```php
<?php

namespace App\Rules;

use Closure;
use Illuminate\Contracts\Validation\ValidationRule;

class Uppercase implements ValidationRule
{
    /**
     * Run the validation rule.
     *
     * @param  string  $attribute  The name of the form field being validated
     * @param  mixed  $value      The incoming value of the field
     * @param  \Closure(string): \Illuminate\Translation\PotentiallyTranslatedString  $fail  Closure to call on failure
     */
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        // Custom logic: value must be equal to its uppercase version
        if (strtoupper($value) !== $value) {
            $fail('The :attribute must be written in uppercase.');
        }
    }
}
```

##### Usage in Controller:
```php
use App\Rules\Uppercase;

$request->validate([
    'promo_code' => ['required', new Uppercase],
]);
```

#### 2. Inline Custom Closures (Recommended for One-Off Validation)
For validation constraints that are unique and do not require code reusability, you can declare an inline Closure directly in the validation array:

```php
$request->validate([
    'license_plate' => [
        'required',
        function (string $attribute, mixed $value, Closure $fail) {
            // Check regex pattern for license plate (e.g., AA-12-BB)
            if (!preg_match('/^[A-Z]{2}-\d{2}-[A-Z]{2}$/', $value)) {
                $fail("The {$attribute} must match the dynamic format: AA-12-BB.");
            }
        }
    ]
]);
```

---

### Q10. You are tasked with creating a secure user registration system in Laravel. Write the Blade template and Controller action method to fulfill the following requirements:
* Form must use CSRF Protection.
* Inputs to collect: Full Name, Email, Password, and Password Confirmation.
* Validation rules: Name must be string and max 50 chars; Email must be valid format, unique in `users` table, and required; Password must be minimum 8 characters and confirmed.
* Errors must be displayed inline, and inputs must be repopulated upon validation failure.

**Answer:**

#### 1. The Blade Template (`resources/views/auth/register.blade.php`)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Register Account</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body class="bg-light d-flex align-items-center justify-content-center" style="min-height: 100vh;">
    <div class="card shadow" style="width: 450px;">
        <div class="card-header bg-primary text-white">
            <h4 class="mb-0">User Registration</h4>
        </div>
        <div class="card-body">
            <form action="{{ route('register.store') }}" method="POST">
                <!-- CSRF Token Directive -->
                @csrf

                <!-- Name Input -->
                <div class="mb-3">
                    <label for="name" class="form-label">Full Name</label>
                    <input type="text" name="name" id="name" 
                           class="form-control @error('name') is-invalid @enderror" 
                           value="{{ old('name') }}">
                    @error('name')
                        <div class="invalid-feedback">{{ $message }}</div>
                    @enderror
                </div>

                <!-- Email Input -->
                <div class="mb-3">
                    <label for="email" class="form-label">Email Address</label>
                    <input type="email" name="email" id="email" 
                           class="form-control @error('email') is-invalid @enderror" 
                           value="{{ old('email') }}">
                    @error('email')
                        <div class="invalid-feedback">{{ $message }}</div>
                    @enderror
                </div>

                <!-- Password Input -->
                <div class="mb-3">
                    <label for="password" class="form-label">Password</label>
                    <input type="password" name="password" id="password" 
                           class="form-control @error('password') is-invalid @enderror">
                    @error('password')
                        <div class="invalid-feedback">{{ $message }}</div>
                    @enderror
                </div>

                <!-- Confirm Password Input -->
                <div class="mb-3">
                    <label for="password_confirmation" class="form-label">Confirm Password</label>
                    <input type="password" name="password_confirmation" id="password_confirmation" 
                           class="form-control">
                </div>

                <!-- Submit Button -->
                <button type="submit" class="btn btn-primary w-100">Create Account</button>
            </form>
        </div>
    </div>
</body>
</html>
```

#### 2. The Controller (`app/Http/Controllers/RegisterController.php`)
```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use App\Models\User;
use Illuminate\Support\Facades\Hash;

class RegisterController extends Controller
{
    /**
     * Show registration form.
     */
    public function create()
    {
        return view('auth.register');
    }

    /**
     * Store a new registered user after validation.
     */
    public function store(Request $request)
    {
        // 1. Perform In-Controller Validation
        $validatedData = $request->validate([
            'name' => 'required|string|max:50',
            'email' => 'required|email|max:255|unique:users,email',
            'password' => 'required|string|min:8|confirmed', // validates against 'password_confirmation'
        ]);

        // 2. Execution only reaches here if validation passes.
        // Create user record using Eloquent with Hashed password
        $user = User::create([
            'name' => $validatedData['name'],
            'email' => $validatedData['email'],
            'password' => Hash::make($validatedData['password']),
        ]);

        // 3. Log user in and redirect to dashboard
        auth()->login($user);

        return redirect()->route('dashboard')->with('success', 'Your account has been successfully created!');
    }
}
```
