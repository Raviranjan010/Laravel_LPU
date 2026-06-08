# Solved Laravel Practical Exam Paper B

This document contains full, complete, and exam-ready code solutions for all 9 questions of Practical Exam Paper B (from the image `WhatsApp Image 2026-06-07 at 15.16.16 (1).jpeg`).

---

## Q1: Authenticated Route with Controller Response

### Question
Create a MVC application to define a route and assign a middleware to this route for authentication and return a controller code (function) in response.

### Solution

#### 1. Define Route with Auth Middleware (`routes/web.php`)
```php
use App\Http\Controllers\SecureController;
use Illuminate\Support\Facades\Route;

// Redirect guests to standard login route
Route::get('/login', function() {
    return "Please login at /login-action first.";
})->name('login');

// Route protected by 'auth' middleware calling controller index method
Route::get('/secure-dashboard', [SecureController::class, 'index'])
    ->middleware('auth');
```

#### 2. Create the Controller
File: `app/Http/Controllers/SecureController.php`
```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class SecureController extends Controller
{
    public function index()
    {
        // Get authenticated user object
        $user = Auth::user();

        // Return a response confirming authorization status
        return response("Hello, " . $user->name . ". You have successfully accessed this secure controller function via routing middleware!", 200);
    }
}
```

---

## Q2: Blade Template Inheritance & Product Table Sorting (Ascending)

### Question
Create a MVC application to demonstrate the concept of blade template inheritance by creating products table with three fields and display the price in ascending order. (use controller for same).

### Solution

#### 1. Create Layout Template (`resources/views/layouts/main.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Products Portal</title>
</head>
<body>
    <div style="background:#eee; padding:10px;"><h2>LPU Shopping Site Header</h2></div>
    <div style="margin:20px 0;">
        @yield('content')
    </div>
    <div style="background:#ccc; padding:5px;"><p>Footer Rights Reserved</p></div>
</body>
</html>
```

#### 2. Define Controller (`app/Http/Controllers/ProductSortController.php`)
```php
namespace App\Http\Controllers;

class ProductSortController extends Controller
{
    public function show()
    {
        // 3-field products array: Name, Description, Price
        $products = [
            ['name' => 'Mouse', 'desc' => 'USB optical mouse', 'price' => 500],
            ['name' => 'Keyboard', 'desc' => 'Mechanical keyboard', 'price' => 1500],
            ['name' => 'Monitor', 'desc' => '24-inch LED display', 'price' => 9500],
            ['name' => 'HDMI Cable', 'desc' => '4K HDMI Cable', 'price' => 300]
        ];

        // Sort products array by price in ascending order using PHP array helpers
        usort($products, function($a, $b) {
            return $a['price'] <=> $b['price'];
        });

        return view('products_sorted', compact('products'));
    }
}
```

#### 3. Define Route and Child View
*   *Route:* `Route::get('/products-sorted', [ProductSortController::class, 'show']);`
*   *Child View (`resources/views/products_sorted.blade.php`):*
    ```html
    @extends('layouts.main')

    @section('content')
        <h3>Sorted Products (Price: Ascending)</h3>
        <table border="1" cellpadding="5">
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Description</th>
                    <th>Price (₹)</th>
                </tr>
            </thead>
            <tbody>
                @foreach($products as $product)
                    <tr>
                        <td>{{ $product['name'] }}</td>
                        <td>{{ $product['desc'] }}</td>
                        <td>{{ $product['price'] }}</td>
                    </tr>
                @endforeach
            </tbody>
        </table>
    @endsection
    ```

---

## Q3: Form Upload & Database Storage

### Question
Create a MVC application to generate form with atleast five fields and choose file and upload file option. Display the content of form on browser and save in database as well.

### Solution

#### 1. Database Migration & Eloquent Model
*   **Migration Code:**
    ```php
    Schema::create('students', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('email');
        $table->string('phone');
        $table->string('course');
        $table->string('file_path');
        $table->timestamps();
    });
    ```
*   **Model Code (`app/Models/Student.php`):**
    ```php
    namespace App\Models;
    use Illuminate\Database\Eloquent\Model;

    class Student extends Model {
        protected $fillable = ['name', 'email', 'phone', 'course', 'file_path'];
    }
    ```

#### 2. Define Routes (`routes/web.php`)
```php
use App\Http\Controllers\StudentRegistrationController;

Route::get('/register-student', [StudentRegistrationController::class, 'create']);
Route::post('/register-student', [StudentRegistrationController::class, 'store']);
```

#### 3. Create Controller File
File: `app/Http/Controllers/StudentRegistrationController.php`
```php
namespace App\Http\Controllers;

use App\Models\Student;
use Illuminate\Http\Request;

class StudentRegistrationController extends Controller
{
    public function create() {
        return view('student_form');
    }

    public function store(Request $request) {
        // Validate 5 inputs (name, email, phone, course, and file upload)
        $request->validate([
            'name' => 'required',
            'email' => 'required|email',
            'phone' => 'required',
            'course' => 'required',
            'upload' => 'required|file|max:2048'
        ]);

        if ($request->hasFile('upload')) {
            $file = $request->file('upload');
            $filename = 'student_' . time() . '.' . $file->getClientOriginalExtension();
            $path = $file->storeAs('uploads', $filename, 'public');

            // Save to database
            $student = Student::create([
                'name' => $request->input('name'),
                'email' => $request->input('email'),
                'phone' => $request->input('phone'),
                'course' => $request->input('course'),
                'file_path' => $path
            ]);

            // Return and display content back on the browser page
            return view('display_registration', compact('student'));
        }
    }
}
```

#### 4. Create View Templates
*   **Form Template (`resources/views/student_form.blade.php`):**
    ```html
    <form method="POST" action="/register-student" enctype="multipart/form-data">
        @csrf
        Name: <input type="text" name="name" required><br><br>
        Email: <input type="email" name="email" required><br><br>
        Phone: <input type="text" name="phone" required><br><br>
        Course: <input type="text" name="course" required><br><br>
        Upload File: <input type="file" name="upload" required><br><br>
        <button type="submit">Submit Registration</button>
    </form>
    ```
*   **Rendering Page (`resources/views/display_registration.blade.php`):**
    ```html
    <h2>Registration Successful (Stored in DB)</h2>
    <ul>
        <li>Name: {{ $student->name }}</li>
        <li>Email: {{ $student->email }}</li>
        <li>Phone: {{ $student->phone }}</li>
        <li>Course: {{ $student->course }}</li>
        <li>Stored File Path: {{ $student->file_path }}</li>
    </ul>
    ```

---

## Q4: Product Table Listing

### Question
Create a MVC application in laravel which will display the list of 5 products on the webpage in a tabular form. Following fields should be considered: ProductID, ProductName, Description, Price. Use controllers to achieve the same.

### Solution

#### 1. Define Controller (`app/Http/Controllers/ProductListController.php`)
```php
namespace App\Http\Controllers;

class ProductListController extends Controller
{
    public function index()
    {
        // Define list of exactly 5 products
        $products = [
            ['id' => 101, 'name' => 'USB Hub', 'desc' => '4-Port High Speed USB 3.0 Hub', 'price' => 899.00],
            ['id' => 102, 'name' => 'Desk Lamp', 'desc' => 'Dimmable LED Study Lamp with USB Charger', 'price' => 1299.00],
            ['id' => 103, 'name' => 'Laptop Stand', 'desc' => 'Ergonomic Aluminum Foldable Stand', 'price' => 1499.00],
            ['id' => 104, 'name' => 'Webcam', 'desc' => '1080p Full HD Webcam with Microphone', 'price' => 2499.00],
            ['id' => 105, 'name' => 'Wireless Mouse', 'desc' => 'Silent Bluetooth Ergonomic Mouse', 'price' => 799.00]
        ];

        return view('products_list', compact('products'));
    }
}
```

#### 2. Route & View
*   *Route:* `Route::get('/products-table', [ProductListController::class, 'index']);`
*   *View (`resources/views/products_list.blade.php`):*
    ```html
    <h2>Product Inventory List</h2>
    <table border="1" cellpadding="8" style="border-collapse:collapse; width: 100%;">
        <thead style="background-color: #f2f2f2;">
            <tr>
                <th>Product ID</th>
                <th>Product Name</th>
                <th>Description</th>
                <th>Price</th>
            </tr>
        </thead>
        <tbody>
            @foreach($products as $product)
                <tr>
                    <td>{{ $product['id'] }}</td>
                    <td>{{ $product['name'] }}</td>
                    <td>{{ $product['desc'] }}</td>
                    <td>₹{{ number_format($product['price'], 2) }}</td>
                </tr>
            @endforeach
        </tbody>
    </table>
    ```

---

## Q5: Employee Salary Sorting (Descending)

### Question
Create a MVC application to demonstrate the concept of blade template inheritance by creating employee table with three fields and display the salary in descending order. (use controller for same).

### Solution

#### 1. Controller Code (`app/Http/Controllers/EmployeeController.php`)
```php
namespace App\Http\Controllers;

class EmployeeController extends Controller
{
    public function showDescending()
    {
        // 3-fields: ID, Name, Salary
        $employees = [
            ['id' => 1, 'name' => 'Amit Kumar', 'salary' => 45000],
            ['id' => 2, 'name' => 'Ravi Ranjan', 'salary' => 75000],
            ['id' => 3, 'name' => 'Pooja Sharma', 'salary' => 60000],
            ['id' => 4, 'name' => 'Suresh Patel', 'salary' => 32000]
        ];

        // Sort employees by salary in descending order using PHP usort
        usort($employees, function($a, $b) {
            return $b['salary'] <=> $a['salary'];
        });

        return view('employees_descending', compact('employees'));
    }
}
```

#### 2. Define Routes & Blade Layout Template
*   *Route:* `Route::get('/employees-salary', [EmployeeController::class, 'showDescending']);`
*   *Main Layout File (`resources/views/layouts/app_emp.blade.php`):*
    ```html
    <!DOCTYPE html>
    <html>
    <head><title>Employee Records Portal</title></head>
    <body style="font-family: sans-serif; padding: 20px;">
        <header><h2>Employee Management System</h2></header>
        <hr>
        @yield('main_section')
    </body>
    </html>
    ```
*   *Inherited Page View (`resources/views/employees_descending.blade.php`):*
    ```html
    @extends('layouts.app_emp')

    @section('main_section')
        <h3>Employee Salaries List (Sorted: Descending)</h3>
        <table border="1" cellpadding="6" style="border-collapse:collapse;">
            <thead>
                <tr style="background:#ddd;">
                    <th>ID</th>
                    <th>Name</th>
                    <th>Salary (₹)</th>
                </tr>
            </thead>
            <tbody>
                @foreach($employees as $emp)
                    <tr>
                        <td>{{ $emp['id'] }}</td>
                        <td>{{ $emp['name'] }}</td>
                        <td>{{ number_format($emp['salary']) }}</td>
                    </tr>
                @endforeach
            </tbody>
        </table>
    @endsection
    ```

---

## Q6: Route Parameters vs. Query Parameters

### Question
Create a MVC application to demonstrate the concept of route parameter and query parameter a) For this create a products page and display the product item. (Use for loop and controllers for the same).

### Solution

#### 1. Define Routes (`routes/web.php`)
```php
use App\Http\Controllers\RouteQueryDemoController;

// Route parameter route: /product/105
Route::get('/product/{id}', [RouteQueryDemoController::class, 'showRouteParam']);

// Query parameter route: /products-search?category=electronics
Route::get('/products-search', [RouteQueryDemoController::class, 'showQueryParam']);
```

#### 2. Create Controller
File: `app/Http/Controllers/RouteQueryDemoController.php`
```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class RouteQueryDemoController extends Controller
{
    private $inventory = [
        101 => ['name' => 'Mouse', 'category' => 'accessories'],
        102 => ['name' => 'Keyboard', 'category' => 'accessories'],
        103 => ['name' => 'Monitor', 'category' => 'electronics'],
        104 => ['name' => 'CPU', 'category' => 'electronics']
    ];

    // Route parameter handling
    public function showRouteParam($id)
    {
        $product = $this->inventory[$id] ?? null;
        return view('param_view', [
            'type' => 'Route Parameter',
            'param' => $id,
            'result' => $product ? $product['name'] : 'Not Found'
        ]);
    }

    // Query parameter handling
    public function showQueryParam(Request $request)
    {
        $category = $request->query('category', 'all');
        $filtered = [];

        // Loop through array elements to match category
        foreach ($this->inventory as $id => $item) {
            if ($category == 'all' || $item['category'] == $category) {
                $filtered[] = $item['name'];
            }
        }

        return view('param_view', [
            'type' => 'Query Parameter (?category=)',
            'param' => $category,
            'resultsList' => $filtered
        ]);
    }
}
```

#### 3. View template (`resources/views/param_view.blade.php`)
```html
<!DOCTYPE html>
<html>
<head><title>Parameter Demo</title></head>
<body>
    <h2>Laravel Parameter Demonstration</h2>
    <p>Demonstrating: <strong>{{ $type }}</strong></p>
    <p>Parameter Value Submitted: <strong>{{ $param }}</strong></p>
    <hr>

    @if(isset($result))
        <p>Matched Product: {{ $result }}</p>
    @elseif(isset($resultsList))
        <p>Products Matching Category:</p>
        <ul>
            <!-- Using Blade Loop -->
            @for($i = 0; $i < count($resultsList); $i++)
                <li>{{ $resultsList[$i] }}</li>
            @endfor
        </ul>
    @endif
</body>
</html>
```

---

## Q7: Controller and Student Table (5 Fields)

### Question
Create a MVC application to demonstrate the concept of controllers and display the student table with atleast 5 fields.

### Solution

#### 1. Define Controller (`app/Http/Controllers/StudentTableController.php`)
```php
namespace App\Http\Controllers;

class StudentTableController extends Controller
{
    public function showStudents()
    {
        // Student array containing 5 fields: RollNo, Name, Email, Course, Marks
        $students = [
            ['roll' => 'LPU001', 'name' => 'Ravi', 'email' => 'ravi@lpu.in', 'course' => 'BCA', 'marks' => 88],
            ['roll' => 'LPU002', 'name' => 'Amit', 'email' => 'amit@lpu.in', 'course' => 'MCA', 'marks' => 92],
            ['roll' => 'LPU003', 'name' => 'Neha', 'email' => 'neha@lpu.in', 'course' => 'BTech', 'marks' => 85]
        ];

        return view('student_records', compact('students'));
    }
}
```

#### 2. Route & View
*   *Route:* `Route::get('/students-records', [StudentTableController::class, 'showStudents']);`
*   *View (`resources/views/student_records.blade.php`):*
    ```html
    <h2>Student Records (5 Fields)</h2>
    <table border="1" cellpadding="6" style="border-collapse:collapse;">
        <thead style="background:#eee;">
            <tr>
                <th>Roll No</th>
                <th>Name</th>
                <th>Email</th>
                <th>Course</th>
                <th>Marks (%)</th>
            </tr>
        </thead>
        <tbody>
            @foreach($students as $student)
                <tr>
                    <td>{{ $student['roll'] }}</td>
                    <td>{{ $student['name'] }}</td>
                    <td>{{ $student['email'] }}</td>
                    <td>{{ $student['course'] }}</td>
                    <td>{{ $student['marks'] }}</td>
                </tr>
            @endforeach
        </tbody>
    </table>
    ```

---

## Q8: Blade Inheritance & Employee Table (Descending)

*Note: This matches Q5 in the exam paper. The same code structure is applied here.*

---

## Q9: Registration Form Validation & Save

### Question
Create a MVC application to request 'name', 'gender', 'address', 'password' fields from a form and display their values on browser and also save in database.

### Solution

#### 1. Database Migration & Eloquent Model
*   **Migration Code:**
    ```php
    Schema::create('users_validated', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('gender');
        $table->text('address');
        $table->string('password'); // Hashed password
        $table->timestamps();
    });
    ```
*   **Model Code (`app/Models/UserValidated.php`):**
    ```php
    namespace App\Models;
    use Illuminate\Database\Eloquent\Model;

    class UserValidated extends Model {
        protected $table = 'users_validated';
        protected $fillable = ['name', 'gender', 'address', 'password'];
    }
    ```

#### 2. Define Routes (`routes/web.php`)
```php
use Illuminate\Http\Request;
use App\Models\UserValidated;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Route;

Route::get('/user-form', function() {
    return view('user_form');
});

Route::post('/user-form', function(Request $request) {
    $request->validate([
        'name' => 'required',
        'gender' => 'required',
        'address' => 'required',
        'password' => 'required|min:6'
    ]);

    // Save to Database
    $user = UserValidated::create([
        'name' => $request->input('name'),
        'gender' => $request->input('gender'),
        'address' => $request->input('address'),
        'password' => Hash::make($request->input('password')) // Secure hash
    ]);

    // Display values directly on browser
    return view('user_display', compact('user'));
});
```

#### 3. View Templates
*   **Form Template (`resources/views/user_form.blade.php`):**
    ```html
    <form method="POST" action="/user-form">
        @csrf
        Name: <input type="text" name="name"><br><br>
        Gender:
        <input type="radio" name="gender" value="Male"> Male
        <input type="radio" name="gender" value="Female"> Female<br><br>
        Address: <textarea name="address"></textarea><br><br>
        Password: <input type="password" name="password"><br><br>
        <button type="submit">Submit</button>
    </form>
    ```
*   **Display View Template (`resources/views/user_display.blade.php`):**
    ```html
    <h2>User Created (Stored in Database)</h2>
    <p>Name: {{ $user->name }}</p>
    <p>Gender: {{ $user->gender }}</p>
    <p>Address: {{ $user->address }}</p>
    <p>Hashed Password: {{ $user->password }}</p>
    ```
