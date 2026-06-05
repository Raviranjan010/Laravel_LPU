# Unit 5 Viva Questions & Answers

### Q1: What does the HTTP status code "419 Page Expired" mean in Laravel, and how do you resolve it?
**Answer:**
A `419 Page Expired` error indicates that a POST, PUT, PATCH, or DELETE request was submitted without a valid CSRF token, or that the session expired before the form was submitted.
To resolve it:
1. Ensure the `@csrf` Blade directive is included inside the `<form>` tags.
2. Clear browser cookies and session storage.
3. Ensure the session configuration (`SESSION_DRIVER` in `.env`) is properly set and sessions are working.

### Q2: Why is the `@method` directive necessary in HTML forms when calling PUT, PATCH, or DELETE routes?
**Answer:**
HTML forms only support `GET` and `POST` HTTP request methods. They do not understand or send `PUT`, `PATCH`, or `DELETE` methods natively. To override this limitation, Laravel parses a hidden input named `_method` submitted via a `POST` form (using `@method('PUT')`) to route the request to the matching controller action.

### Q3: What class or interface represents the `$errors` variable that is automatically shared with all views?
**Answer:**
The `$errors` variable is an instance of `Illuminate\Support\ViewErrorBag`. It wraps individual `Illuminate\Support\MessageBag` instances for each input form context.

### Q4: How do you check if a specific input field (e.g., `email`) has validation errors, and how do you retrieve the first error message?
**Answer:**
* **To check**: Use `$errors->has('email')` or the `@error('email')` Blade directive.
* **To get the first error message**: Use `$errors->first('email')`.

### Q5: How do you retrieve all validation error messages as a flat array in a view?
**Answer:**
Use the `all()` method on the `$errors` object:
```blade
@foreach ($errors->all() as $message)
    <p>{{ $message }}</p>
@endforeach
```

### Q6: Can you retrieve the invalid inputs that the user previously entered after validation fails? How?
**Answer:**
Yes. During validation failures, Laravel automatically flashes the input values to the session. You can retrieve these values using the `old('field_name')` helper.
Example:
```html
<input type="text" name="name" value="{{ old('name') }}">
```

### Q7: If you are using Form Request validation, do you need to manually write redirect logic in your controller upon failure?
**Answer:**
No. The `FormRequest` class handles this automatically. If validation fails, it throws a `ValidationException` which is caught by Laravel's exception handler. The handler automatically redirects the user back to the previous location with the errors and old inputs flashed to the session.

### Q8: What does the `authorize()` method in a Form Request class do? What happens if it returns `false`?
**Answer:**
The `authorize()` method determines if the currently authenticated user has the permission to perform the request. If it returns `false`, Laravel automatically aborts the request and returns an HTTP `403 Forbidden` response without running the validation rules or controller action.

### Q9: How can you write custom error messages for specific validation rules inside a Form Request?
**Answer:**
By overriding the `messages()` method in your custom `FormRequest` class.
Example:
```php
public function messages(): array
{
    return [
        'email.required' => 'We need your email address to register you!',
        'email.unique' => 'This email is already registered in our system.',
    ];
}
```

### Q10: How do you create a custom validation rule class using Artisan?
**Answer:**
Run the command:
```bash
php artisan make:rule RuleName
```
This generates the rule class inside the `app/Rules` directory.

### Q11: What interface must a custom validation rule class implement, and what is its primary method?
**Answer:**
It must implement the `Illuminate\Contracts\Validation\ValidationRule` interface. The primary method is:
```php
public function validate(string $attribute, mixed $value, Closure $fail): void
```
To fail the validation, you execute the `$fail` closure with the error message string: `$fail('The input is invalid.')`.

### Q12: How do you exclude specific URLs (like webhooks or external API routes) from CSRF protection in Laravel?
**Answer:**
In Laravel 11.x, you exclude routes by adding them to the middleware configurations in `bootstrap/app.php` using `validateCsrfTokens(except: [...])`:
```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->validateCsrfTokens(except: [
        'stripe/*',
        'webhook/receive',
    ]);
})
```
*(In Laravel 10.x and below, you would add them to the `$except` array inside `app/Http/Middleware/VerifyCsrfToken.php`).*

### Q13: What validation rule checks if a secondary confirmation field matches the primary field (e.g., password matching password_confirmation)?
**Answer:**
The `confirmed` validation rule. Applying `confirmed` to the `password` field tells Laravel to automatically check for a matching input named `password_confirmation`.

### Q14: How does Laravel validate file uploads, and what are some common file-related validation rules?
**Answer:**
Laravel handles file validation using built-in file rules in the validator.
Common rules include:
* `file`: Must be a successfully uploaded file.
* `image`: Must be an image (jpeg, png, bmp, gif, svg, or webp).
* `mimes:pdf,docx`: Must match one of the specified MIME/file extensions.
* `dimensions:min_width=100,min_height=200`: Image dimension limits.
* `max:2048`: Maximum file size in kilobytes (2048 KB = 2 MB).

### Q15: What is the difference between `unique` and `exists` validation rules?
**Answer:**
* `unique:table,column`: Ensures the submitted value does **not** exist in the database table (useful for new registrations).
* `exists:table,column`: Ensures the submitted value **does** exist in the database table (useful for checking if a foreign key, like `category_id`, is valid).
