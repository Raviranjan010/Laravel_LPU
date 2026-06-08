# Unit 5 Viva Questions and Answers

This section contains categorized Viva Questions and Answers for Unit 5: Form Validation and CSRF Protection.

---

## 1. Basic Viva Questions (Beginner Level)

### ⭐ Q1: What does the error "419 Page Expired" mean in Laravel, and how is it resolved?
*   **Short Answer:** It means the CSRF token is missing, expired, or invalid. Resolve it by adding `@csrf` inside the form.
*   **Detailed Answer:** In Laravel, state-changing requests (POST, PUT, PATCH, DELETE) are intercepted by the `VerifyCsrfToken` middleware. This middleware expects a secret token parameter named `_token`. If the form is submitted without `@csrf`, or if the user stays on the form page so long that their session expires, the validator throws a `TokenMismatchException`, which renders a `419 Page Expired` error screen.
*   **Follow-up Questions an Examiner May Ask:**
    1.  *Where does Laravel store the reference token to compare?* Inside the user's active session.
    2.  *Which middleware checks this token?* `VerifyCsrfToken` middleware (which resides in the `web` middleware group).
    3.  *How do you check for this in Javascript/AJAX forms?* By reading the meta tag `<meta name="csrf-token" content="{{ csrf_token() }}">` and sending it in request headers.

### ⭐ Q2: Why is the `@method` directive necessary in HTML forms for PUT, PATCH, or DELETE routes?
*   **Short Answer:** Because HTML forms only support GET and POST methods. `@method` spoofs the other HTTP verbs.
*   **Detailed Answer:** Standard web browsers are restricted by HTML specifications and can only execute GET and POST submissions inside form elements. Since modern RESTful web APIs utilize PUT (full resource updates), PATCH (partial updates), and DELETE (removals), Laravel overcomes this limitation through method spoofing. Writing `@method('DELETE')` inserts a hidden input `<input type="hidden" name="_method" value="DELETE">`. Laravel reads this parameter on submission and routes the request to the DELETE controller action.
*   **Follow-up Questions an Examiner May Ask:**
    1.  *What form method must be defined in the HTML tag when spoofing?* `method="POST"`.
    2.  *What happens if you define method="PUT" directly in the form tag?* Browsers do not recognize it and default the request method to GET.

### Q3: What is the `$errors` variable in Laravel, and where does it come from?
*   **Short Answer:** It is an instance of `ViewErrorBag` containing all validation error messages, automatically shared with all views.
*   **Detailed Answer:** In Laravel, whenever validation fails in a controller using `$request->validate()`, it throws a validation exception and redirects back. Before redirecting, Laravel flashes the error messages array to the session. The `ShareErrorsFromSession` middleware (registered inside the `web` group) automatically binds the `$errors` variable to all Blade templates, meaning developers don't have to manually pass it.
*   **Follow-up Questions an Examiner May Ask:**
    1.  *What happens if there are no errors? Is the variable still available?* Yes, it remains defined as an empty `ViewErrorBag` object, preventing "undefined variable" PHP warnings.
    2.  *How do you retrieve the very first error for a field?* Use `$errors->first('field_name')`.

---

## 2. Intermediate Viva Questions (Conceptual Understanding)

### ⭐ Q4: Explain the difference between `$errors->any()`, `$errors->all()`, and `@error` directive.
*   **Short Answer:** `any()` checks if any errors exist; `all()` returns a flat array of all messages; `@error` checks and displays an inline message for a specific input field.
*   **Detailed Answer:**
    *   `$errors->any()` returns a boolean (`true`/`false`) determining if any validation checks failed.
    *   `$errors->all()` retrieves a flat string array containing every validation error message (useful for showing bullet-point summaries at the top of forms).
    *   `@error('field')` is a Blade directive that checks if a specific input field failed validation, and defines a temporary `$message` variable containing the error text for rendering inline.
*   **Follow-up Questions an Examiner May Ask:**
    1.  *How do you loop through all errors?* Use `@foreach ($errors->all() as $error) <li>{{ $error }}</li> @endforeach`.
    2.  *Where is $message defined?* Only within the opening and closing tags of the `@error('field')` block.

### ⭐ Q5: What is the difference between `unique` and `exists` validation rules?
*   **Short Answer:** `unique` ensures the input value is not in the database table, while `exists` ensures the input value already exists in the table.
*   **Detailed Answer:**
    *   `unique:users,email` checks the `users` table's `email` column. If the input value matches an existing record, validation fails. (Used in **Registration** to prevent duplicate accounts).
    *   `exists:categories,id` checks the `categories` table's `id` column. If the input value is *not* found in the table, validation fails. (Used in **Foreign Key checks** to ensure category selections are valid).
*   **Follow-up Questions an Examiner May Ask:**
    1.  *What happens if you run unique validation on update forms?* It will throw an error because the current user already owns the email. You must configure the rule to ignore the current user ID.

### Q6: What does the `old()` helper do in Laravel, and what is its working mechanism?
*   **Short Answer:** It retrieves the user's previously submitted input from session flash storage after validation fails.
*   **Detailed Answer:** When input validation fails, Laravel redirects back and temporarily flashes the input array to the session. The `old('input_name')` helper pulls that flashed data out of the session and renders it in the form input fields (`value`, `selected`, `checked` attributes) so the user doesn't have to re-type.
*   **Follow-up Questions an Examiner May Ask:**
    1.  *Can you set a default value in old()?* Yes: `old('name', 'Default Student Name')`.
    2.  *Why shouldn't you use old() on password fields?* For security; passwords should never be displayed in plain text or preserved on screen after validation failure.

---

## 3. Advanced Viva Questions (External & Practical Exam Level)

### ⭐ Q7: What are the parameters passed to custom rule closures, and what is the role of `$fail`?
*   **Short Answer:** The parameters are `$attribute`, `$value`, and `$fail`. `$fail` is a callback function invoked to mark validation failure and specify error messages.
*   **Detailed Answer:**
    ```php
    function ($attribute, $value, $fail) { ... }
    ```
    *   `$attribute` represents the string name of the field being validated (e.g. `'phone'`).
    *   `$value` contains the raw input string/data submitted by the user.
    *   `$fail` is a Closure callback. If the custom condition fails, invoking `$fail('Error Text')` registers the validation failure and sets the message in the `$errors` bag. If `$fail` is never called, validation passes.
*   **Follow-up Questions an Examiner May Ask:**
    1.  *Can you pass dynamic data into the closure?* Yes, you can use PHP's `use ($variable)` syntax to import variables from the controller scope.

### ⭐ Q8: Compare Form Request Classes vs. In-Controller Validation. When would you use which?
*   **Short Answer:** In-controller validation defines rules directly inside controller actions (simple forms). Form Requests decouple validation into custom request classes (complex enterprise forms).
*   **Detailed Comparison Table:**
    | Comparison | In-Controller Validation | Form Request Classes |
    | :--- | :--- | :--- |
    | **Location** | Inside controller action. | Separate class file in `app/Http/Requests`. |
    | **Clean Code** | Clutters controller code. | Controller remains clean and readable. |
    | **Reusability** | Hard to reuse elsewhere. | Easy to share across controllers/API requests. |
    | **Best For** | Simple forms, quick exam coding. | Medium to large enterprise projects. |
*   **Follow-up Questions an Examiner May Ask:**
    1.  *What command generates a Form Request class?* `php artisan make:request StoreRequestName`.
    2.  *What happens if the authorize() method in a Form Request class returns false?* Laravel aborts the request and returns an HTTP `403 Forbidden` response.

---

## 4. Frequently Asked Questions (FAQs)

### FAQ 1: What is CSRF, and how does Laravel verify it?
*   **What is it?** A security token system preventing external sites from executing forged POST requests.
*   **Why use it?** To protect user accounts and state data from automated cross-site attacks.
*   **How does it work?** Matches token parameters submitted by client browser forms with reference values stored in user sessions.
*   **Limitations:** Stateless REST API routes should exclude CSRF checking because clients (like mobile apps) don't have session files.

### FAQ 2: What is form repopulation?
*   **What is it?** Retaining old inputs in input text boxes, textareas, dropdowns, and radios after redirects.
*   **How does it work?** Using the `old()` helper inside Blade input tags to bind flashed session inputs to HTML input states.
*   **Common mistake:** Placing `old()` in textareas using `value="..."` attributes instead of rendering it inside `<textarea>{{ old('name') }}</textarea>`.

### FAQ 3: What is method spoofing?
*   **What is it?** Overriding standard GET/POST methods to support PUT, PATCH, and DELETE verbs.
*   **How does it work?** Blade's `@method('verb')` directive generates a hidden `_method` input which Laravel routing parses to override the HTTP request verb.
*   **Why is it needed?** Standard browser HTML forms do not natively support state-changing verbs other than POST.
