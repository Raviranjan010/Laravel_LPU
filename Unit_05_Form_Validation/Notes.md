# Unit 5: Laravel Form Validation & CSRF Protection

## 📚 Course Outcomes Coverage
*   **CO5**: Design forms and execute form validation to ensure data integrity.

---

## CSRF Protection

**CSRF (Cross-Site Request Forgery)** is a malicious attack where a foreign site triggers unauthorized requests on behalf of an authenticated user.

### How Laravel Prevents CSRF
Laravel's `VerifyCsrfToken` middleware checks all incoming POST, PUT, PATCH, and DELETE requests for a token that matches the token stored in the user's active session.

To add this token to your HTML forms, use the `@csrf` Blade directive:
```html
<form method="POST" action="/submit">
    @csrf
    <!-- Generates a hidden input element containing the token -->
</form>
```

#### Raw HTML Output:
```html
<input type="hidden" name="_token" value="xyz123abc456...">
```

---

## Method Field Spoofing

HTML forms only support `GET` and `POST` methods. Standard web browsers cannot submit `PUT`, `PATCH`, or `DELETE` requests directly.

### Spoofing HTTP Verbs
To match RESTful routes defined with `PUT` or `DELETE`, submit the form as `POST` and inject a hidden `_method` variable using the `@method` Blade directive:

```html
<form method="POST" action="/post/5">
    @csrf
    @method('DELETE') {{-- Spoofs DELETE request --}}
    
    <button type="submit">Delete Post</button>
</form>
```

---

## Form Validation

Laravel offers two primary ways to validate user inputs:
1.  **In-Controller Validation**: Good for simple forms.
2.  **Form Request Validation**: Best practice for decoupling validation rules from controller logic.

### 1. In-Controller Validation
```php
public function store(Request $request)
{
    $validated = $request->validate([
        'username' => 'required|alpha_num|min:5|unique:users,name',
        'email' => 'required|email',
        'password' => 'required|min:8|confirmed', // Matches password_confirmation
    ]);

    // Code execution only continues if validation passes.
    // If it fails, an exception is thrown, and Laravel redirects back automatically.
}
```

### 2. Standard Validation Rules Table

| Rule | Parameter | Purpose |
|------|-----------|---------|
| `required` | None | Field cannot be null or empty. |
| `email` | None | Validates correct email format. |
| `unique` | `table,column` | Value must not exist in the specified table. |
| `confirmed` | None | Checks if field matches secondary field ending in `_confirmation` (e.g. `password` matches `password_confirmation`). |
| `min / max` | `integer` | Sets minimum/maximum string length, numeric value, or file size. |
| `accepted` | None | Validates field is 'yes', 'on', 1, or true (useful for terms of service). |

---

## Form Request Classes

A **Form Request** is a custom request class containing validation and authorization logic.

### 1. Generate Form Request
```bash
php artisan make:request StoreBookRequest
```
This generates `app/Http/Requests/StoreBookRequest.php`.

### 2. Configure Form Request Class
```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreBookRequest extends FormRequest
{
    // Authorization check
    public function authorize(): bool
    {
        // Set to true to allow request execution
        return auth()->check(); 
    }

    // Validation rules
    public function rules(): array
    {
        return [
            'title' => 'required|string|max:100|unique:books,title',
            'isbn' => 'required|alpha_dash|size:13',
            'price' => 'required|numeric|min:0',
        ];
    }

    // Custom validation error messages
    public function messages(): array
    {
        return [
            'title.required' => 'A unique book title is mandatory.',
            'isbn.size' => 'The ISBN number must be exactly 13 characters long.',
        ];
    }
}
```

---

## Displaying Validation Errors & Repopulating Forms

When validation fails, errors are flashed to the session and loaded into the `$errors` variable, which is automatically shared with all views.

### 1. Displaying All Errors
```blade
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

### 2. Displaying Inline Errors (Specific Fields)
```html
<div class="mb-3">
    <label>Email Address</label>
    <input type="text" name="email" value="{{ old('email') }}">
    
    @error('email')
        <div class="text-danger">{{ $message }}</div>
    @enderror
</div>
```

---

## Custom Validation Rules

To create a custom validation rule class:

### 1. Generate Rule
```bash
php artisan make:rule MinWords
```
This generates `app/Rules/MinWords.php`.

### 2. Write Validation Logic
```php
<?php

namespace App\Rules;

use Closure;
use Illuminate\Contracts\Validation\ValidationRule;

class MinWords implements ValidationRule
{
    protected $minWords;

    public function __construct($minWords = 3)
    {
        $this->minWords = $minWords;
    }

    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        $wordCount = str_word_count($value);

        if ($wordCount < $this->minWords) {
            $fail("The :attribute must contain at least {$this->minWords} words.");
        }
    }
}
```

### 3. Use in Validation Array
```php
use App\Rules\MinWords;

$request->validate([
    'description' => ['required', new MinWords(5)],
]);
```
