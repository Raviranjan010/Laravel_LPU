# Unit V: Laravel Form Validation, CSRF, & Method Spoofing

## 📚 Table of Contents
- [CSRF Protection](#csrf-protection)
- [Method Field Spoofing](#method-field-spoofing)
- [Laravel Form Validation](#laravel-form-validation)
- [Displaying Validation Errors in Blade](#displaying-validation-errors-in-blade)
- [Custom Validation Rules](#custom-validation-rules)
- [Repopulating Forms (Old Input)](#repopulating-forms-old-input)
- [Common Mistakes to Avoid (Gotchas)](#common-mistakes-to-avoid-gotchas)
- [Keywords to Remember](#keywords-to-remember)
- [Exam Practice Questions](#exam-practice-questions)

---

## CSRF Protection

### What is CSRF?
**CSRF (Cross-Site Request Forgery)** is a malicious exploit where unauthorized commands are transmitted from a user that the web application trusts.

### How CSRF Works
Imagine you are logged into your bank account. A malicious website has a hidden form that sends a post request to `yourbank.com/transfer-money`. Since you are already logged in, the browser automatically attaches your session cookie, and the bank processes the request.

### Laravel's Solution: CSRF Tokens
Laravel automatically secures all active POST, PUT, PATCH, and DELETE HTML forms by generating a CSRF token for each active user session. This token is verified by the `VerifyCsrfToken` middleware.

```
┌──────────┐            1. GET /form             ┌───────────┐
│          ├────────────────────────────────────>│           │
│  Client  │     2. Response (Form + CSRF Token) │  Laravel  │
│ Browser  │<────────────────────────────────────┤  Server   │
│          │                                     │           │
│          │            3. POST /submit          │           │
│          │   (Sends form inputs + Token)       │           │
│          ├────────────────────────────────────>│           │
│          │                                     │           │
│          │                                     │  Checks:  │
│          │                                     │  Token OK?│
│          │                                     │  If Yes:  │
│          │                                     │  Process! │
└──────────┘                                     └───────────┘
```

### How to use it in Blade
Simply add the `@csrf` directive inside your HTML form:

```html
<form method="POST" action="/register">
    @csrf <!-- Automatically generates hidden input containing the CSRF token -->
    <input type="text" name="name">
    <button type="submit">Register</button>
</form>
```

#### Raw HTML Output:
```html
<input type="hidden" name="_token" value="abc123xyz456...tokenString...">
```

---

## Method Field Spoofing

HTML forms only support `GET` and `POST` methods. Standard web browsers cannot send `PUT`, `PATCH`, or `DELETE` requests directly from a form tag.

### Laravel's Solution: Method Spoofing
Laravel allows you to define RESTful PUT/PATCH/DELETE routes and access them in forms by injecting a hidden `_method` field.

Use the `@method` directive inside your form:

```html
<form method="POST" action="/user/profile/5">
    @csrf
    @method('PUT') <!-- Tells Laravel to treat this POST request as a PUT request -->
    
    <input type="text" name="email">
    <button type="submit">Update</button>
</form>
```

#### Raw HTML Output:
```html
<input type="hidden" name="_method" value="PUT">
```

---

## Laravel Form Validation

Laravel provides a simple and rich validation system. There are two primary ways to perform form validation:
1.  **In-Controller Validation (Using `$request->validate()`)**
2.  **Form Request Validation (For complex, clean validation logic)**

### 1. In-Controller Validation (Quick & Basic)
This is best for simple applications and fast development.

```php
use Illuminate\Http\Request;

public function store(Request $request)
{
    // Validate request fields
    $validatedData = $request->validate([
        'name' => 'required|string|max:255',
        'email' => 'required|email|unique:users,email',
        'password' => 'required|min:8|confirmed', // expects password_confirmation field!
        'age' => 'nullable|integer|min:18',
    ]);

    // If validation fails, Laravel automatically redirects the user BACK
    // with error messages and old inputs flashed to the session.
    
    // If validation passes, we proceed:
    User::create($validatedData);

    return redirect()->route('dashboard')->with('success', 'User registered successfully!');
}
```

### 2. Built-in Validation Rules Cheat Sheet

| Validation Rule | Description | Example |
|-----------------|-------------|---------|
| `required` | Field must not be empty or missing. | `'name' => 'required'` |
| `email` | Field must be formatted as a valid email address. | `'email' => 'email'` |
| `unique:table,column` | Value must not exist in database table. | `'email' => 'unique:users,email'` |
| `confirmed` | Field must match another field ending in `_confirmation`. | `'password' => 'confirmed'` (needs `password_confirmation`) |
| `integer` | Value must be a valid integer. | `'age' => 'integer'` |
| `min:value` / `max:value` | Numeric size, string character length, or file size. | `'password' => 'min:8'` |
| `nullable` | Field is optional (can be null). | `'middle_name' => 'nullable'` |
| `image` | Uploaded file must be an image (jpeg, png, bmp, gif, svg, webp). | `'avatar' => 'image'` |
| `mimes:types` | Uploaded file must match one of the listed extensions. | `'doc' => 'mimes:pdf,docx'` |

### 3. Customizing Error Messages Inline
You can customize validation messages directly in the controller:

```php
$request->validate([
    'email' => 'required|email',
], [
    'email.required' => 'We absolutely need your email address to register!',
    'email.email' => 'The email format you entered is incorrect.',
]);
```

### 4. Form Request Validation (Best Practice)
For cleaner, decoupled controller code, move validation rules into a dedicated Form Request class.

#### Generate the Form Request:
```bash
php artisan make:request StoreUserRequest
```
This file is generated in `app/Http/Requests/StoreUserRequest.php`.

#### Set up the Request class:
```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreUserRequest extends FormRequest
{
    // Determine if the user is authorized to make this request
    public function authorize(): bool
    {
        return true; // Set to true to allow access
    }

    // Get the validation rules that apply to the request
    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users',
            'password' => 'required|min:8|confirmed',
        ];
    }

    // Get custom validation error messages (Optional)
    public function messages(): array
    {
        return [
            'name.required' => 'Please fill in your full name.',
            'email.unique' => 'This email address is already in use.',
        ];
    }
}
```

#### Inject the Request in the Controller:
Replace standard `Request` with your custom request:

```php
use App\Http\Requests\StoreUserRequest;

public function store(StoreUserRequest $request)
{
    // Validation runs automatically before this method executes.
    // If it fails, redirection is triggered automatically.

    // Retrieve validated inputs:
    $validated = $request->validated();

    User::create($validated);

    return redirect()->route('home');
}
```

---

## Displaying Validation Errors in Blade

When validation fails, errors are flashed to the session as `$errors`, which is automatically shared with all views.

### 1. Displaying All Error Messages at the Top:
```blade
@if ($errors->any())
    <div class="alert alert-danger" style="border: 1px solid red; padding: 10px; border-radius: 5px;">
        <strong>Validation Errors occurred:</strong>
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

### 2. Displaying Specific Field Error inline (Recommended):
Use the `@error` Blade directive:

```html
<div>
    <label for="email">Email Address:</label>
    <input type="text" name="email" id="email" class="@error('email') is-invalid @enderror" value="{{ old('email') }}">
    
    @error('email')
        <span style="color: red; font-size: 12px;">{{ $message }}</span>
    @enderror
</div>
```

---

## Custom Validation Rules

When built-in rules aren't enough, you can create custom validation rule classes.

### 1. Generating a Custom Rule:
Let's create a rule that checks if a string is uppercase:
```bash
php artisan make:rule Uppercase
```
This creates `app/Rules/Uppercase.php`.

### 2. Writing the Rule Logic:
Open `app/Rules/Uppercase.php` and edit:

```php
<?php

namespace App\Rules;

use Closure;
use Illuminate\Contracts\Validation\ValidationRule;

class Uppercase implements ValidationRule
{
    // Run the validation rule.
    // $attribute: Name of the field being validated (e.g., "name")
    // $value: The input value
    // $fail: Callable to trigger if validation fails
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        if (strtoupper($value) !== $value) {
            // Trigger fail and set custom error message
            $fail("The :attribute field must consist of uppercase letters only.");
        }
    }
}
```

### 3. Using the Custom Rule in your validation array:
```php
use App\Rules\Uppercase;

$request->validate([
    'license_code' => ['required', new Uppercase], // Instantiate custom rule class
]);
```

---

## Repopulating Forms (Old Input)

To prevent users from having to re-enter all fields (like a long address) if validation fails, use the `old()` helper.

```html
<form method="POST" action="/submit">
    @csrf
    
    <!-- String Inputs -->
    <input type="text" name="username" value="{{ old('username') }}">
    
    <!-- Select Dropdown -->
    <select name="country">
        <option value="IN" {{ old('country') == 'IN' ? 'selected' : '' }}>India</option>
        <option value="US" {{ old('country') == 'US' ? 'selected' : '' }}>United States</option>
    </select>
    
    <!-- Textarea -->
    <textarea name="bio">{{ old('bio') }}</textarea>
</form>
```

---

## Common Mistakes to Avoid (Gotchas)

### ❌ Forgetting `password_confirmation` field for `confirmed` rule
If your validation rule is `'password' => 'confirmed'`, Laravel expects another input field named exactly `password_confirmation`. If this field is missing in your form or named differently, validation will always fail.

### ❌ Forgetting `@csrf` on POST/PUT forms
Without `@csrf`, submitting a form will result in a **"419 Page Expired"** HTTP error. Always include it in forms!

### ❌ Setting `@method('PUT')` directly in `<form method="PUT">`
HTML form tag `method` attribute only understands `GET` and `POST`. Writing `<form method="PUT">` behaves as a GET request in most browsers. Always set `<form method="POST">` and add `@method('PUT')` inside.

---

## Keywords to Remember

1.  **CSRF (Cross-Site Request Forgery)**: An exploit that tricks user browsers into executing unwanted actions on verified web sessions. Securing this requires CSRF tokens.
2.  **Method Spoofing**: Injecting a hidden input (`_method`) to bypass HTML form limits, enabling routes to handle PUT, PATCH, and DELETE requests.
3.  **Form Request**: An independent request class encapsulating validation rules and authorization checks for a clean controller design.
4.  **Confirmed Rule**: A validation constraint requiring a field value to match a secondary field named with the suffix `_confirmation`.
5.  **Errors Shared Variable**: An automatically shared variable (`$errors`) available in all Blade templates containing active validation messages.

---

## Exam Practice Questions

### Short Answer Questions (2-5 Marks)
1.  **What is a "419 Page Expired" error in Laravel? Why does it occur?**
    *Answer:* It represents a CSRF token verification failure. It occurs when a user submits a POST/PUT/DELETE form that lacks a valid `@csrf` token, or when a session has expired.
2.  **Explain the difference between `@csrf` and `@method('PUT')` in Laravel forms.**
    *Answer:* `@csrf` generates a hidden field for session validation to protect against Cross-Site Request Forgery. `@method('PUT')` spoofing simulates a PUT HTTP method because browsers only support GET and POST form submissions.
3.  **Write down a controller script to validate that a field called `username` is required, alphanumeric, and between 5 to 15 characters long.**
    ```php
    $request->validate([
        'username' => 'required|alpha_num|between:5,15',
    ]);
    ```

### Long Answer Questions (10 Marks)
1.  **Explain the step-by-step process of creating a custom Form Request in Laravel. Illustrate with a detailed class implementation example showing custom validation rules, authorization, and custom validation messages.**
    *(Hint: Refer to Form Request Validation section. Detail authorization methods, returning rule arrays, and declaring custom messages.)*
2.  **How are validation errors captured and rendered in Laravel? Explain the structure of the `$errors` container and write code to display errors both globally at the top of a page and inline next to specific fields.**
    *(Hint: Detail the `$errors` global view variable, demonstrate `$errors->any()`, `$errors->all()`, and `@error('field')` blade structures.)*
