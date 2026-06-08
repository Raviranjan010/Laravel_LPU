# Unit 5 Question Bank & Prep Material

This document contains university exam preparation questions, practical lab tasks, MCQs, and quick revision resources for Unit 5.

---

## 1. University Exam Preparation Section

### 1.1 Short Answer Questions (2-5 Marks)

#### Q1. What is the CSRF token, and why does Laravel require it for state-changing forms?
*   **Answer:** A CSRF (Cross-Site Request Forgery) token is a unique, random string generated per session. Laravel requires it to ensure the incoming POST/PUT/DELETE request was genuinely initiated by the authenticated user from within the application, preventing cross-site session hijacking.

#### Q2. Explain the purpose of method spoofing in Laravel forms.
*   **Answer:** HTML forms only support GET and POST methods. To support RESTful routing standards (which use PUT, PATCH, and DELETE verbs), Laravel implements spoofing. Adding `@method('DELETE')` inserts a hidden input named `_method` containing the verb, which Laravel routing intercepts and interprets accordingly.

#### Q3. What happens behind the scenes in a Laravel controller when validation fails?
*   **Answer:** When `$request->validate()` fails, a `ValidationException` is thrown. Laravel automatically catches it, flashes the error messages and user inputs to the session, and redirects the user back to the previous page.

#### Q4. Write the syntax to display a validation error message inline for a specific input field named `email`.
*   **Answer:**
    ```html
    <input type="text" name="email" value="{{ old('email') }}">
    @error('email')
        <span class="text-danger">{{ $message }}</span>
    @enderror
    ```

---

### 1.2 Long Answer Questions (8-10 Marks)

#### Q5. Compare In-Controller Validation with custom Form Request Classes in detail. Provide code snippets representing both approaches.
*   **Answer:**
    In-controller validation is quick to write but bloats controllers. Form Request classes decouple validation and authorization checks into standalone files.
    *   **In-Controller Approach:**
        ```php
        public function store(Request $request) {
            $request->validate([
                'title' => 'required|max:100',
                'body' => 'required|min:5'
            ]);
            Post::create($request->all());
            return "Saved!";
        }
        ```
    *   **Form Request Approach:**
        First generate request: `php artisan make:request StorePostRequest`.
        File `app/Http/Requests/StorePostRequest.php`:
        ```php
        namespace App\Http\Requests;
        use Illuminate\Foundation\Http\FormRequest;

        class StorePostRequest extends FormRequest {
            public function authorize(): bool {
                return auth()->check(); // Only logged-in users
            }
            public function rules(): array {
                return [
                    'title' => 'required|max:100',
                    'body' => 'required|min:5'
                ];
            }
        }
        ```
        Then type-hint in Controller:
        ```php
        public function store(StorePostRequest $request) {
            Post::create($request->validated());
            return "Saved!";
        }
        ```

#### Q6. Write a complete student registration Blade form and matching Controller action that fulfills the following specifications:
1. Form uses CSRF protection and POST submission.
2. Form fields: Name, Email, Address, Course, Gender, Agree to Terms.
3. Form redirects back on error, showing inline validation messages and repopulating all inputs except password.
*   **Answer:**
    *   **Blade View (`resources/views/student_register.blade.php`):**
        ```html
        <form method="POST" action="/student-save">
            @csrf
            Name: <input type="text" name="name" value="{{ old('name') }}">
            @error('name') <small style="color:red;">{{ $message }}</small> @enderror <br>

            Email: <input type="text" name="email" value="{{ old('email') }}">
            @error('email') <small style="color:red;">{{ $message }}</small> @enderror <br>

            Address: <textarea name="address">{{ old('address') }}</textarea>
            @error('address') <small style="color:red;">{{ $message }}</small> @enderror <br>

            Course:
            <select name="course">
                <option value="BCA" {{ old('course') == 'BCA' ? 'selected' : '' }}>BCA</option>
                <option value="MCA" {{ old('course') == 'MCA' ? 'selected' : '' }}>MCA</option>
            </select>
            @error('course') <small style="color:red;">{{ $message }}</small> @enderror <br>

            Gender:
            <input type="radio" name="gender" value="Male" {{ old('gender') == 'Male' ? 'checked' : '' }}> Male
            <input type="radio" name="gender" value="Female" {{ old('gender') == 'Female' ? 'checked' : '' }}> Female
            @error('gender') <small style="color:red;">{{ $message }}</small> @enderror <br>

            <input type="checkbox" name="agree" value="1" {{ old('agree') == '1' ? 'checked' : '' }}> Accept Terms
            @error('agree') <small style="color:red;">{{ $message }}</small> @enderror <br>

            <button type="submit">Register</button>
        </form>
        ```
    *   **Controller Action:**
        ```php
        public function save(Request $request) {
            $request->validate([
                'name' => 'required|string|max:50',
                'email' => 'required|email|unique:students,email',
                'address' => 'required',
                'course' => 'required',
                'gender' => 'required',
                'agree' => 'accepted'
            ]);
            Student::create($request->all());
            return "Student registered successfully!";
        }
        ```

---

### 1.3 Important Practical Questions

#### Task 1: Custom Blocklist Username Checker
Create a form with a username input. Write custom validation logic inside the controller closure to block the usernames `admin`, `root`, and `manager`.
*   **Controller Code:**
    ```php
    public function verifyUsername(Request $request) {
        $request->validate([
            'username' => [
                'required',
                function($attribute, $value, $fail) {
                    $blocklist = ['admin', 'root', 'manager'];
                    if (in_array(strtolower($value), $blocklist)) {
                        $fail('The username :attribute is reserved and not allowed.');
                    }
                }
            ]
        ]);
        return "Username is available!";
    }
    ```

#### Task 2: Spoofed Delete Form
Create a practical interface with a "Delete Student" button. Ensure the route maps to a `DELETE` request, spoofed from a `POST` form, and executes a success output.
*   **Route:**
    ```php
    Route::delete('/student/{id}/delete', function($id) {
        return "Student record " . $id . " deleted successfully!";
    });
    ```
*   **Blade Form:**
    ```html
    <form method="POST" action="/student/45/delete">
        @csrf
        @method('DELETE')
        <button type="submit" onclick="return confirm('Are you sure?')">Delete Record</button>
    </form>
    ```

---

## 2. MCQ Preparation Section

### 2.1 Beginner Level MCQs

#### Q1. Which directive generates the hidden input token to protect forms from cross-site scripts?
*   A) `@token`
*   B) `@csrf`
*   C) `@validate`
*   D) `@method`
*   *Correct Answer:* B
*   *Explanation:* The `@csrf` directive compiles into a hidden HTML input field containing the cryptographic session CSRF token.

#### Q2. What error code is returned when a form is submitted without a CSRF token in Laravel?
*   A) `404 Not Found`
*   B) `403 Forbidden`
*   C) `419 Page Expired`
*   D) `500 Internal Server Error`
*   *Correct Answer:* C
*   *Explanation:* Laravel triggers a `419 Page Expired` response when the session token is missing, expired, or invalid.

#### Q3. Standard HTML forms natively support which of the following HTTP request methods?
*   A) GET, POST, PUT, DELETE
*   B) GET, POST
*   C) POST, PUT, DELETE
*   D) GET only
*   *Correct Answer:* B
*   *Explanation:* HTML specifications restrict the `method` attribute of `<form>` tags to GET and POST requests.

---

### 2.2 Intermediate Level MCQs

#### Q4. What helper method retrieves the user's previously submitted input value after a validation redirect?
*   A) `session('input')`
*   B) `input('old')`
*   C) `old('input_name')`
*   D) `previous('input')`
*   *Correct Answer:* C
*   *Explanation:* The `old()` helper reads flashed input data stored in the session by the validation exception handler.

#### Q5. Inside a custom rule closure, what function is called to notify the validator that validation failed?
*   A) `$fail()`
*   B) `$error()`
*   C) `$abort()`
*   D) `$reject()`
*   *Correct Answer:* A
*   *Explanation:* Calling the third argument closure `$fail('Error Message')` registers a failure in the validator instance.

#### Q6. What variable holds all validation errors in a Blade file?
*   A) `$errorBag`
*   B) `$errors`
*   C) `$messages`
*   D) `$error`
*   *Correct Answer:* B
*   *Explanation:* Laravel's error middleware automatically shares the `$errors` variable (instance of `ViewErrorBag`) with all views.

---

### 2.3 Advanced Level MCQs

#### Q7. If the `authorize()` method in a custom Form Request class returns false, what HTTP status code does Laravel throw?
*   A) `401 Unauthorized`
*   B) `403 Forbidden`
*   C) `422 Unprocessable Content`
*   D) `419 Page Expired`
*   *Correct Answer:* B
*   *Explanation:* A falsy return in the `authorize()` method throws an `AuthorizationException`, triggering an HTTP `403 Forbidden` response.

#### Q8. What is the correct order of parameters in a custom validation rule closure function?
*   A) `$value, $attribute, $fail`
*   B) `$attribute, $value, $fail`
*   C) `$fail, $attribute, $value`
*   D) `$attribute, $fail, $value`
*   *Correct Answer:* B
*   *Explanation:* Laravel's closure validator signature expects parameters in the order: string `$attribute`, mixed `$value`, and Closure `$fail`.

---

## 3. Quick Revision Section

### 3.1 One-Line Definitions
*   **CSRF Protection:** Cryptographic session-token mechanism securing state-changing forms against unauthorized external request execution.
*   **Method Spoofing:** Simulating PUT, PATCH, and DELETE request verbs in GET/POST-only HTML form elements via a hidden `_method` variable.
*   **old() Helper:** Blade function rendering temporarily flashed session inputs to restore form states after validations redirect.
*   **ViewErrorBag ($errors):** An object automatically shared with all templates, carrying error messages from the previous request redirect.
*   **Closure Rules:** In-controller validation functions containing custom rules utilizing parameter inputs and a `$fail` callback.

### 3.2 Most Important Syntax formulas
*   **CSRF Form Token:** `@csrf` ➜ `<input type="hidden" name="_token" value="xyz...">`
*   **Spoofed PUT Verb:** `@method('PUT')` ➜ `<input type="hidden" name="_method" value="PUT">`
*   **Textbox Repopulation:** `value="{{ old('field_name') }}"`
*   **Textarea Repopulation:** `<textarea name="address">{{ old('address') }}</textarea>`
*   **Dropdown Selection:** `<option value="BCA" {{ old('course') == 'BCA' ? 'selected' : '' }}>`
*   **Radio / Checkbox Checking:** `<input type="radio" value="M" {{ old('gender') == 'M' ? 'checked' : '' }}>`

### 3.3 Frequently Forgotten Concepts
*   **Textareas lack value attributes:** Writing `<textarea value="{{ old('bio') }}">` is a syntax bug; old values must be output between `<textarea>` and `</textarea>` tags.
*   **$message scope restriction:** The `$message` variable holds the error string, but is only available inside the `@error('field') ... @enderror` directive block. Calling it outside results in undefined variable warnings.
*   **CSRF exception handling for APIs:** Stateless APIs do not use sessions. Ensure API routing arrays bypass CSRF middleware to avoid constant `419` returns.

### 3.4 Top 10 Exam Questions List
1. Explain the internal working of CSRF protection in Laravel. ⭐
2. Differentiate between In-Controller validation and Form Requests. ⭐
3. Write code to display all validation error messages using a Blade loop. ⭐
4. Implement a custom validation rule using a closure. ⭐
5. Differentiate between `unique` and `exists` validation rules with examples. ⭐
6. How does Laravel support PUT and DELETE routing verbs inside forms? ⭐
7. How are old input fields repopulated in dropdowns and checkboxes?
8. Explain the parameters and role of `$fail` in Laravel validation rules.
9. What does the `authorize()` method in custom Form Requests do?
10. Detail what happens in Laravel when user input fails controller rules.
