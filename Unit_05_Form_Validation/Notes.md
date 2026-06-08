# Unit 5: Laravel Form Validation & CSRF Protection

## 📚 Course Outcomes Coverage
*   **CO5**: Design forms and execute form validation to ensure data integrity and security.

---

## 1. CSRF Protection (Cross-Site Request Forgery)

### 1.1 Definition
CSRF (Cross-Site Request Forgery) is a security mechanism in Laravel that protects forms from unauthorized or malicious requests by verifying a unique session token submitted with each state-changing request (POST, PUT, PATCH, DELETE).

### 1.2 Why It Is Needed
Without CSRF protection, an authenticated user browsing a malicious site could be tricked into executing actions on a separate site where they are logged in.
*   **The Attack Vector:** Imagine you are logged in to `www.mybank.com`. You visit `www.fakebank.com` in another tab. The fake site contains a hidden form that submits `Transfer ₹50,000` to `www.mybank.com`. Since your browser automatically sends session cookies, the bank website authorizes the transfer. CSRF protection prevents this.

### 1.3 Importance
CSRF protection is vital for web security. It blocks malicious automated cross-site scripts from performing state modifications (like changing passwords, updating emails, or executing transactions) on behalf of unsuspecting logged-in users.

### 1.4 Working Mechanism
1.  **Session Startup:** When a user visits the Laravel application, a random cryptographic token is generated and saved inside the user's active session store.
2.  **Form Injection:** When rendering HTML forms, the developer includes `@csrf`, which generates a hidden input containing this token.
3.  **Submission:** The browser submits the form including the hidden token field.
4.  **Verification:** The `VerifyCsrfToken` middleware intercepts the request, compares the form's token with the session's token. If they match, execution proceeds. Otherwise, Laravel aborts with a `419 Page Expired` error.

```
       User Browser                                      Laravel Server
            │                                                  │
            │ ─── 1. Requests Form Page ─────────────────────► │
            │ ◄── 2. Returns Form + Generated Session Token ── │ (Token: ab12xyz...)
            │                                                  │
            │ ─── 3. Submits Form with token input ──────────► │
            │                                                  │ [Compare Tokens]
            │                                                  │ Token matches?
            │                                                  │  ├─ Yes: Process Request
            │ ◄── 4. Returns Success Response ──────────────── │  └─ No: Throws 419 Error
```

### 1.5 Syntax
Include the `@csrf` directive inside the form:
```html
<form method="POST" action="/submit">
    @csrf
    <!-- Content -->
</form>
```
Which Laravel compiles into the following raw HTML:
```html
<input type="hidden" name="_token" value="ab12xy89mn456pq">
```

### 1.6 Examples
*   **Basic Example:** A simple subscription form.
    ```html
    <form method="POST" action="/subscribe">
        @csrf
        <input type="email" name="email" placeholder="Subscribe">
        <button type="submit">Subscribe</button>
    </form>
    ```
*   **Intermediate Example:** Registration form.
    ```html
    <form method="POST" action="/register">
        @csrf
        <input type="text" name="name" placeholder="Name">
        <input type="email" name="email" placeholder="Email">
        <input type="password" name="password" placeholder="Password">
        <button type="submit">Register</button>
    </form>
    ```
*   **Real-World Example:** Student save routing and view.
    *   *Route:*
        ```php
        // routes/web.php
        use Illuminate\Http\Request;
        Route::view('/student', 'student');
        Route::post('/save-student', function(Request $request) {
            return "Student Saved: " . $request->input('name');
        });
        ```
    *   *Blade File (`resources/views/student.blade.php`):*
        ```html
        <form method="POST" action="/save-student">
            @csrf
            <label>Name:</label>
            <input type="text" name="name">
            <button type="submit">Submit</button>
        </form>
        ```

### 1.7 Output Explanation
When a user visits `/student`, writes their name, and clicks Submit, the hidden `_token` is sent. Laravel compares the token, validates the request origin, and displays: `Student Saved: [Name]`. If `@csrf` is removed, submitting the form displays a `419 Page Expired` error screen.

### 1.8 Advantages
*   **Automatic Protection:** Enabled by default in the `web` middleware group.
*   **High Security:** Prevents cross-site credential hijack exploits.
*   **Ease of Use:** Simple `@csrf` Blade tag covers the entire backend logic setup.

### 1.9 Disadvantages / Limitations
*   **Session Dependency:** Requires active sessions. If sessions fail or expire, users get errors.
*   **REST APIs:** Unsuitable for stateless API routes (which should be excluded via `except` configs or API groups).

### 1.10 Common Mistakes
*   **Mistake 1: Forgetting `@csrf` directive:** Results in the infamous `419 Page Expired` error.
*   **Mistake 2: Using GET for modifications:** Using GET routes to insert, update, or delete records bypasses CSRF checks, making the app vulnerable to malicious clicks.

### 1.11 Interview Perspective
*   *What does CSRF stand for?* Cross-Site Request Forgery.
*   *How does Laravel verify the token?* Via the `VerifyCsrfToken` middleware comparing request `_token` parameter (or header `X-CSRF-TOKEN`) with the active session token.
*   *Does GET need CSRF?* No. GET requests should be read-only.

### 1.12 Exam Perspective
*   *Theory question:* Explain the concept of CSRF and how Laravel protects forms against it.
*   *Practical question:* Create a student registration form that uses CSRF and submits data via POST.

---

### 1.13 Comparison Tables

#### CSRF vs Validation
| Feature | CSRF Protection | Validation |
| :--- | :--- | :--- |
| **Purpose** | Request authenticity and source check. | Request content format and logic check. |
| **Trigger** | Intercepts *before* reaching controller. | Checked inside controller or form request. |
| **Fail Error** | `419 Page Expired` | `302 Redirect Back` with error messages. |
| **Scope** | Form origin verification. | Checking data integrity (min, email, unique). |

#### CSRF vs Authentication
| Feature | CSRF Protection | Authentication |
| :--- | :--- | :--- |
| **Focus** | Checks where the request came from. | Checks who is making the request. |
| **Storage** | Session-based random tokens. | Session-based user ID / password hashes. |
| **Mechanisms**| Hidden inputs / headers validation. | Middleware like `auth`. |

#### GET vs POST regarding CSRF
| Feature | GET | POST |
| :--- | :--- | :--- |
| **Purpose** | Read / Fetch data. | Modify / Insert / Delete data. |
| **CSRF Requirement** | Not required. | Highly required. |
| **Data Visibility** | Appended to URL (visible). | Sent in request payload (hidden). |

---

## 2. Method Field Spoofing

### 2.1 Definition
Method Field Spoofing is a technique used in Laravel to simulate HTTP verbs (PUT, PATCH, DELETE) that are not natively supported by standard HTML forms.

### 2.2 Why It Is Needed
HTML forms only support `GET` and `POST` methods. If a developer attempts to use `<form method="DELETE">` or `<form method="PUT">`, the browser will fallback to a `GET` request. Laravel routes require these RESTful methods to map actions like updating or deleting records securely.

### 2.3 Importance
It allows developers to implement strict RESTful route systems (CRUD operations) inside standard web browsers, ensuring the correct HTTP verbs are used for database operations.

### 2.4 Working Mechanism
1.  **Form setup:** The form is declared with `method="POST"`.
2.  **Field Injection:** The developer includes the `@method('PUT')` or `@method('DELETE')` directive.
3.  **Blade Compilation:** Blade converts the directive into a hidden form input named `_method` with the target method as the value.
4.  **Route Handling:** When submitted, Laravel intercepts the `POST` request, inspects the hidden `_method` field, overrides the request type, and routes it to the PUT/DELETE controller action.

```
       Browser (HTML)                                    Laravel Router
             │                                                 │
             │ ─── submits POST request with _method="PUT" ──► │
             │                                                 │ [Intercepts request]
             │                                                 │ Reads _method value
             │                                                 │ Overrides POST to PUT
             │                                                 │ Routes request to:
             │ ◄── Executes PUT route action response ──────── │ Route::put('/update')
```

### 2.5 Syntax
```html
<form method="POST" action="/resource">
    @csrf
    @method('PUT')
</form>
```
Blade compiles this into:
```html
<input type="hidden" name="_method" value="PUT">
```

### 2.6 Examples
*   **Basic Example:** Spoofing PATCH.
    ```html
    <form method="POST" action="/update-email">
        @csrf
        @method('PATCH')
        <input type="email" name="email">
        <button type="submit">Update</button>
    </form>
    ```
*   **Intermediate Example:** Delete button.
    ```html
    <form method="POST" action="/student/delete">
        @csrf
        @method('DELETE')
        <button type="submit">Delete Student</button>
    </form>
    ```
*   **Real-World Example:** Student CRUD update routing and view.
    *   *Route:*
        ```php
        Route::put('/student/update', function(Request $request) {
            return "Student Updated to: " . $request->input('name');
        });
        ```
    *   *Blade Form:*
        ```html
        <form method="POST" action="/student/update">
            @csrf
            @method('PUT')
            <input type="text" name="name" value="Ravi Kumar">
            <button type="submit">Update Name</button>
        </form>
        ```

### 2.7 Output Explanation
When the form is submitted, the browser sends a POST request. Laravel's routing engine reads `_method = PUT`, treats the request as a PUT verb, executes the matching `Route::put('/student/update')`, and outputs `Student Updated to: Ravi Kumar`.

### 2.8 Advantages
*   **RESTful Routing:** Enables standard REST APIs structure for frontend browser forms.
*   **Secure Scope:** Ensures operations like deletions cannot be triggered by simple links (GET requests).

### 2.9 Disadvantages / Limitations
*   **Browser Dependency:** Requires manual hidden inputs because browsers remain restricted to GET/POST.

### 2.10 Common Mistakes
*   **Mistake 1: Writing `<form method="PUT">`:** Browsers will convert this to GET, resulting in RouteNotFound or execution mismatches.
*   **Mistake 2: Forgetting `@csrf` when spoofing:** Method spoofing requires POST, which triggers CSRF token checks.

### 2.11 Interview Perspective
*   *Why can't browsers send DELETE requests?* The HTML specification restricts the `method` attribute of `<form>` tags to GET and POST.
*   *What parameter tells Laravel to spoof the method?* A hidden parameter named `_method`.

### 2.12 Exam Perspective
*   *Theory question:* What is method spoofing? Why do we use `@method('PUT')` instead of `method="PUT"`?
*   *Practical question:* Write a form to delete a student record using Laravel method spoofing.

---

### 2.13 Comparison Tables

#### CRUD Mapping to HTTP Verbs
| CRUD Operation | REST HTTP Method | Laravel Route Definition | Browser Form setup |
| :--- | :--- | :--- | :--- |
| **Create** | `POST` | `Route::post()` | `method="POST"` |
| **Read** | `GET` | `Route::get()` | `method="GET"` |
| **Update** | `PUT` or `PATCH` | `Route::put()` / `Route::patch()` | `method="POST"` + `@method('PUT')` |
| **Delete** | `DELETE` | `Route::delete()` | `method="POST"` + `@method('DELETE')` |

#### PUT vs PATCH
| Feature | PUT | PATCH |
| :--- | :--- | :--- |
| **Scope** | Replaces / Updates the entire resource. | Performs partial updates on specific fields. |
| **Directive** | `@method('PUT')` | `@method('PATCH')` |
| **Example** | Submitting a whole student registration form. | Updating only the profile phone number. |

#### Method Field vs Form Method
| Feature | Form Method | Method Field |
| :--- | :--- | :--- |
| **Specification**| HTML standard attribute. | Laravel framework helper. |
| **Supported Verbs**| `GET`, `POST`. | `PUT`, `PATCH`, `DELETE`. |
| **Location** | `<form method="...">` | Inside form body `@method('...')` |

---

## 3. Form Validation & Error Messages

### 3.1 Definition
Form Validation is the process of defining strict formats, limits, and rules for incoming user data, and generating error messages when the inputs violate these rules.

### 3.2 Why It Is Needed
To prevent corrupt, missing, or malicious data from being processed or written to your database (e.g. empty names, invalid email addresses, or short passwords).

### 3.3 Importance
Ensures data consistency and application security. It protects the application from SQL injections, database truncation errors, and unauthorized duplicate entries.

### 3.4 Working Mechanism
1.  **Form Submission:** The user fills the form and submits it.
2.  **Rule Check:** In the controller, `$request->validate([...])` evaluates rules.
3.  **Exception (If Failed):** If verification fails, a `ValidationException` is thrown.
4.  **Redirect & Session Flash:** Laravel redirects back to the previous form, flashing all errors into the session.
5.  **View Rendering:** The shared `$errors` variable captures the flashed session data, and the `@error` directive renders error messages dynamically next to inputs.

### 3.5 Syntax
*   **Controller Validation:**
    ```php
    $request->validate([
        'name' => 'required|min:3',
        'email' => 'required|email'
    ]);
    ```
*   **Custom Messages Override Syntax:**
    ```php
    $request->validate([
        'email' => 'required|email'
    ], [
        'email.required' => 'We need your email to contact you!',
        'email.email' => 'The email format is invalid.'
    ]);
    ```

### 3.6 Examples
*   **Basic Example:** Basic Required Input.
    ```php
    // Controller
    $request->validate(['username' => 'required']);
    ```
*   **Intermediate Example:** Multi-rule controller with custom messages.
    ```php
    public function save(Request $request) {
        $request->validate([
            'email' => 'required|email|unique:users,email',
            'password' => 'required|min:8|confirmed'
        ], [
            'email.unique' => 'This email is already registered!',
            'password.min' => 'Security check: Password must be at least 8 characters.'
        ]);
        return "Validated!";
    }
    ```
*   **Real-World Example:** Complete dynamic student admission validator and form.
    *   *Route:*
        ```php
        Route::post('/admission', function(Request $request) {
            $request->validate([
                'student_name' => 'required|alpha|max:50',
                'email' => 'required|email',
                'age' => 'required|numeric'
            ], [
                'student_name.required' => 'Student Name cannot be left blank.',
                'email.email' => 'Please input a valid email.',
                'age.numeric' => 'Age must be an integer value.'
            ]);
            return "Registration Approved!";
        });
        ```
    *   *Blade File:*
        ```html
        <!-- Display All Errors at Top -->
        @if ($errors->any())
            <div style="color: red;">
                @foreach ($errors->all() as $error)
                    <p>{{ $error }}</p>
                @endforeach
            </div>
        @endif

        <form method="POST" action="/admission">
            @csrf
            Name: <input type="text" name="student_name">
            @error('student_name') <small style="color: red;">{{ $message }}</small> @enderror <br>

            Email: <input type="text" name="email">
            @error('email') <small style="color: red;">{{ $message }}</small> @enderror <br>

            Age: <input type="text" name="age">
            @error('age') <small style="color: red;">{{ $message }}</small> @enderror <br>

            <button type="submit">Submit</button>
        </form>
        ```

### 3.7 Output Explanation
If validation passes, "Registration Approved!" is returned. If the user leaves the name blank or types an invalid email, Laravel redirects back and renders the specific errors using the `@error` directives.

### 3.8 Advantages
*   **Auto Redirects:** Handled completely by the framework.
*   **View Injection:** `$errors` bag is auto-available in all views.
*   **Extensible:** Easily allows custom rules and localized messages.

### 3.9 Disadvantages / Limitations
*   **Controller Clutter:** Declaring rules inside controller actions makes files large (resolved using Form Request Classes).

### 3.10 Common Mistakes
*   **Mistake 1: Missing string parameters in `@error`:** Writing `@error(email)` instead of `@error('email')`.
*   **Mistake 2: Printing `$message` outside `@error`:** The `$message` variable is only defined inside the `@error` block scope. Calling it elsewhere causes PHP errors.

### 3.11 Interview Perspective
*   *What class represents the `$errors` variable?* `Illuminate\Support\ViewErrorBag`.
*   *What HTTP status code is returned for validation failures in AJAX requests?* `422 Unprocessable Content`.

### 3.12 Exam Perspective
*   *Theory question:* Describe the role of `$errors` in Laravel and write a script to display all errors.
*   *Practical question:* Validate login inputs (email, password) and display custom error messages.

---

### 3.13 Comparison Tables

#### `$errors` vs `@error`
| Feature | `$errors` (Global Bag) | `@error` (Blade Directive) |
| :--- | :--- | :--- |
| **Purpose** | Holds all error messages for all inputs. | Checks and prints errors for one field. |
| **Iteration** | Requires `@foreach` loops to print lists. | Directly yields block with `$message`. |
| **Use Case** | Header notification alert messages. | Inline error alerts next to inputs. |

#### Validation Rule vs Error Message
| Validation Rule | Fails When... | Default Error Message |
| :--- | :--- | :--- |
| `required` | Field is empty / null. | "The [field] field is required." |
| `numeric` | Value contains non-numbers. | "The [field] field must be a number." |
| `email` | Standard @ structure is missing. | "The [field] must be a valid email." |

---

## 4. Custom Validation Rules

### 4.1 Definition
Custom Validation Rules are developer-defined constraints used to implement specific business logic validation checks that are not covered by Laravel's built-in rules.

### 4.2 Why It Is Needed
Often, enterprise requirements have specific patterns.
*   *Examples:* Validating that employee emails belong to a specific corporate domain, checking if a phone number starts with a specific regional digit, or preventing usernames from containing blocked blacklist words (like "admin" or "root").

### 4.3 Importance
Encapsulates business rules, promotes code reuse, and keeps validation arrays clean and easy to maintain.

### 4.4 Working Mechanism
*   **Closure Rule:** Declared dynamically in the validator using a closure function that runs matching conditional checks, generating error messages via the `$fail()` callable callback.
*   **Rule Class:** Generated via Artisan command. The class contains a `validate()` method. When the validator runs, it resolves this class, executes the logic, and triggers `$fail()` if invalid.

### 4.5 Syntax
*   **Closure Syntax:**
    ```php
    $request->validate([
        'field' => [function ($attribute, $value, $fail) {
            if (/* condition fails */) {
                $fail("Error message.");
            }
        }]
    ]);
    ```
*   **Rule Class Generation:**
    ```bash
    php artisan make:rule regionalPhoneRule
    ```

### 4.6 Examples
*   **Basic Example:** Rule requiring age 18+.
    ```php
    $request->validate([
        'age' => [function ($attribute, $value, $fail) {
            if ($value < 18) {
                $fail('You must be at least 18 years old.');
            }
        }]
    ]);
    ```
*   **Intermediate Example:** Block admin/root usernames.
    ```php
    $request->validate([
        'username' => [function ($attribute, $value, $fail) {
            $blocked = ['admin', 'root', 'superuser'];
            if (in_array(strtolower($value), $blocked)) {
                $fail('Reserved words cannot be used as usernames.');
            }
        }]
    ]);
    ```
*   **Real-World Example:** Corporate domain and phone structure checks.
    *   *Controller Validator:*
        ```php
        public function storeEmployee(Request $request)
        {
            $request->validate([
                'email' => [
                    'required', 'email',
                    function ($attribute, $value, $fail) {
                        if (!str_contains($value, '@lpu.co.in')) {
                            $fail('The email must belong to the @lpu.co.in domain.');
                        }
                    }
                ],
                'phone' => [
                    'required',
                    function ($attribute, $value, $fail) {
                        if (substr($value, 0, 1) !== '9') {
                            $fail('LPU regional phone numbers must start with digit 9.');
                        }
                    }
                ]
            ]);
            return "Employee Validated!";
        }
        ```

### 4.7 Output Explanation
If input contains `ravi@lpu.co.in` and `9876543210`, validation passes and outputs `Employee Validated!`. If inputs are `ravi@gmail.com` and `8876543210`, the page redirects back, rendering corporate and phone formatting errors inline.

### 4.8 Advantages
*   **Extensible:** Completely customizable business logic.
*   **Clean Architecture:** Separates complex checks from basic framework rules.

### 4.9 Disadvantages / Limitations
*   **Overhead:** Rule classes create extra directories and files (`app/Rules`).

### 4.10 Common Mistakes
*   **Mistake 1: Assignment instead of comparison:** Writing `if ($value = 'admin')` inside closures.
*   **Mistake 2: Missing `$fail` parameter:** Forgetting to declare or invoke `$fail('error')`, which prevents the validator from knowing the validation failed.

### 4.11 Interview Perspective
*   *What are the three parameters of the custom rule closure function?* `$attribute` (input name), `$value` (user value), and `$fail` (error generator callback).
*   *How do you trigger validation failure in rule classes?* By calling the `$fail` closure.

### 4.12 Exam Perspective
*   *Theory question:* Discuss custom validation rules in Laravel. Explain rule closures and custom rule classes.
*   *Practical question:* Write a custom validation logic to verify that the student salary input is greater than 10,000.

---

### 4.13 Comparison Tables

#### Closure Rule vs Rule Class
| Feature | Closure Custom Rule | Rule Class |
| :--- | :--- | :--- |
| **Creation** | Inline declaration inside Controller. | Generated via `php artisan make:rule`. |
| **Reusability** | Low (confined to that controller action). | High (importable across multiple files). |
| **Syntax Complexity**| Low (easy to write quickly for exams). | High (requires separate class structure). |
| **Best For** | Single-page, one-off form requirements. | Enterprise applications, shared rules. |

#### Custom Rule vs Built-In Rule
| Feature | Built-In Rule | Custom Rule |
| :--- | :--- | :--- |
| **Origin** | Provided by Laravel Framework. | Written by Developer. |
| **Example** | `required`, `email`, `unique`. | Check phone regional code format. |
| **Call Method** | String format: `'required|email'`. | Instantiated class: `[new CustomRule]`. |

---

## 5. Repopulating Forms (Old Inputs)

### 5.1 Definition
Repopulating Forms is the process of retrieving and displaying the data previously entered by a user after validation fails, preventing the user from having to re-type the entire form.

### 5.2 Why It Is Needed
If validation fails, the default browser behavior reloads a blank page. If a user fills out a 20-field application form and gets one email format wrong, clearing the entire form leads to frustration and poor user experience (UX).

### 5.3 Importance
Essential for user convenience. It streamlines data correction and increases form completion rates.

### 5.4 Working Mechanism
1.  **Form Submission:** Input data is sent.
2.  **Failure Redirect:** Validation fails; Laravel flashes all input data to the session store.
3.  **Page Reload:** The browser redirects back to the form page.
4.  **old() Execution:** Inside Blade views, the `old('field')` helper queries the flashed session data.
5.  **Data Render:** If found, the previous value is inserted into the input's `value`, `selected`, or `checked` attributes.

```
       Browser                                            Laravel
          │                                                  │
          │ ─── 1. Submits "Ravi", "invalid-email" ────────► │
          │                                                  │ [Validation Fails]
          │                                                  │ Flashes input to Session
          │ ◄── 2. Redirects back ────────────────────────── │
          │                                                  │
          │ ─── 3. Page loads with old() helpers ──────────► │
          │                                                  │ Retrieves flashed values
          │ ◄── 4. Renders form with previous inputs ─────── │
```

### 5.5 Syntax
*   **Textbox/Input:**
    ```html
    <input type="text" name="name" value="{{ old('name') }}">
    ```
*   **Textarea:**
    ```html
    <textarea name="bio">{{ old('bio') }}</textarea>
    ```
*   **Select Dropdowns:**
    ```html
    <option value="BCA" {{ old('course') == 'BCA' ? 'selected' : '' }}>BCA</option>
    ```
*   **Radio Buttons / Checkboxes:**
    ```html
    <input type="radio" name="gender" value="Male" {{ old('gender') == 'Male' ? 'checked' : '' }}>
    ```

### 5.6 Examples
*   **Basic Example:** Basic Textbox.
    ```html
    <input type="text" name="city" value="{{ old('city') }}">
    ```
*   **Intermediate Example:** Radio and Dropdown selection persistence.
    ```html
    <!-- Dropdown -->
    <select name="course">
        <option value="BCA" {{ old('course') == 'BCA' ? 'selected' : '' }}>BCA</option>
        <option value="MCA" {{ old('course') == 'MCA' ? 'selected' : '' }}>MCA</option>
    </select>

    <!-- Radio -->
    Gender:
    <input type="radio" name="gender" value="M" {{ old('gender') == 'M' ? 'checked' : '' }}> Male
    <input type="radio" name="gender" value="F" {{ old('gender') == 'F' ? 'checked' : '' }}> Female
    ```
*   **Real-World Example:** Full Student Registration Form with repopulation.
    *   *Controller File (`app/Http/Controllers/StudentController.php`):*
        ```php
        namespace App\Http\Controllers;
        use Illuminate\Http\Request;

        class StudentController extends Controller {
            public function save(Request $request) {
                $request->validate([
                    'name' => 'required',
                    'email' => 'required|email',
                    'address' => 'required',
                    'course' => 'required',
                    'gender' => 'required',
                    'agree' => 'accepted'
                ]);
                return "Registration Successful!";
            }
        }
        ```
    *   *Blade File (`resources/views/register.blade.php`):*
        ```html
        <form method="POST" action="/register-student">
            @csrf
            
            Name: <input type="text" name="name" value="{{ old('name') }}"> <br>
            Email: <input type="text" name="email" value="{{ old('email') }}"> <br>
            
            Address:
            <textarea name="address">{{ old('address') }}</textarea> <br>

            Course:
            <select name="course">
                <option value="BCA" {{ old('course') == 'BCA' ? 'selected' : '' }}>BCA</option>
                <option value="MCA" {{ old('course') == 'MCA' ? 'selected' : '' }}>MCA</option>
            </select> <br>

            Gender:
            <input type="radio" name="gender" value="Male" {{ old('gender') == 'Male' ? 'checked' : '' }}> Male
            <input type="radio" name="gender" value="Female" {{ old('gender') == 'Female' ? 'checked' : '' }}> Female <br>

            <input type="checkbox" name="agree" value="1" {{ old('agree') == '1' ? 'checked' : '' }}> Accept Terms <br>

            <button type="submit">Submit Form</button>
        </form>
        ```

### 5.7 Output Explanation
If the form fails validation because the Terms box was unchecked, the form is re-rendered with the Name, Email, Address, Course, and Gender inputs remaining populated with the user's previous inputs.

### 5.8 Advantages
*   **Superior UX:** Saves users time and prevents frustration.
*   **Data Integrity:** Reduces entry modification mistakes.

### 5.9 Disadvantages / Limitations
*   **Security Risk:** Passwords should never be repopulated (`old('password')` should not be used).

### 5.10 Common Mistakes
*   **Mistake 1: Placing `old()` inside textarea `value` attribute:**
    *   ❌ *Wrong:* `<textarea name="address" value="{{ old('address') }}"></textarea>`
    *   ✅ *Correct:* `<textarea name="address">{{ old('address') }}</textarea>`
    *   *Reason:* HTML Textareas do not support `value` attributes; data must sit inside the tags.
*   **Mistake 2: Missing quotes:** Writing `old(name)` instead of `old('name')`.

### 5.11 Interview Perspective
*   *How does Laravel remember input values on redirect?* It flashes inputs into the session temporarily for the next request.
*   *Should you repopulate password fields?* No, for security reasons.

### 5.12 Exam Perspective
*   *Theory question:* Explain the purpose and implementation of the `old()` helper in Laravel.
*   *Practical question:* Build a registration form that repopulates text, textarea, select-dropdown, and radio elements after validation failures.

---

### 5.13 Comparison Tables

#### `old()` vs Static Value Attribute
| Feature | Static Value | Dynamic `old()` Helper |
| :--- | :--- | :--- |
| **Mutability** | Always displays the same string value. | Changes dynamically based on previous inputs. |
| **Purpose** | Pre-filling default templates. | Restoring user-entered data on validation failure. |
| **Syntax** | `value="Delhi"` | `value="{{ old('city') }}"` |

#### `old()` vs Session
| Feature | `old()` | Session (`session()`) |
| :--- | :--- | :--- |
| **Lifetime** | 1 Request (flashed data). | Indefinite until manual delete or timeout. |
| **Type** | Inputs only. | Arbitrary application variables (user roles, cart). |
| **Persistence**| Auto-deleted. | Remains active across multiple logins. |

#### Repopulating Form Inputs Comparison
| Field Type | Attribute to Bind | Binding Syntax |
| :--- | :--- | :--- |
| **Textbox / Email**| `value` | `value="{{ old('field') }}"` |
| **Textarea** | Inside Tags | `<textarea>{{ old('field') }}</textarea>` |
| **Select / Option**| `selected` | `{{ old('field') == 'val' ? 'selected' : '' }}` |
| **Radio Button** | `checked` | `{{ old('field') == 'val' ? 'checked' : '' }}` |
| **Checkbox** | `checked` | `{{ old('field') == '1' ? 'checked' : '' }}` |
