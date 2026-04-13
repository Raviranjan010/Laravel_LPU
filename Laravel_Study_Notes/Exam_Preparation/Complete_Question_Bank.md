# 🎯 Complete Exam Question Bank - Laravel Practical Exam

## 📊 Exam Pattern Analysis (Based on LPU INT221 Papers)

**Course**: INT221 - Laravel Framework  
**Max Marks**: 30  
**Max Time**: 2 Hours  
**Evaluation Criteria**:
- Written (Exact Output Match): 15 Marks
- Visual Implementation: 10 Marks  
- Viva: 5 Marks

---

## 🔥 TYPE 1: Route Parameters & Display Questions

### Pattern from Image 1: Multiple Route Parameters

**Original Question**: Create a route `/order/{id}/{product}` that accepts two parameters and displays them.
- **Input**: `/order/101/Laptop`
- **Expected Output**:
  ```
  Order ID: 101
  Product: Laptop
  ```

### 📝 Similar Questions (High Probability):

**Q1.1**: Create route `/user/{username}/{age}` to display user information
- **Input**: `/user/john/25`
- **Expected Output**:
  ```
  Username: john
  Age: 25 years
  ```

**Q1.2**: Create route `/student/{roll}/{name}/{course}` with three parameters
- **Input**: `/student/101/Rahul/BTech`
- **Expected Output**:
  ```
  Roll Number: 101
  Name: Rahul
  Course: BTech
  ```

**Q1.3**: Create route `/book/{id}/{title}/{author}` for book details
- **Input**: `/book/501/Laravel/JohnDoe`
- **Expected Output**:
  ```
  Book ID: 501
  Title: Laravel
  Author: JohnDoe
  ```

**Q1.4**: Create route `/city/{name}/{country}/{population}` 
- **Input**: `/city/Delhi/India/20000000`
- **Expected Output**:
  ```
  City: Delhi
  Country: India
  Population: 20000000
  ```

**Q1.5**: Create route `/invoice/{number}/{date}/{amount}`
- **Input**: `/invoice/INV001/2024-01-15/5000`
- **Expected Output**:
  ```
  Invoice Number: INV001
  Date: 2024-01-15
  Amount: ₹5000
  ```

---

## 🔥 TYPE 2: Controller to Blade Data Passing

### Pattern from Image 1: Passing Data from Controller

**Original Question**: Pass employee data from controller to Blade and display.
- **Expected Output**:
  ```
  Employee Name: Rahul
  Department: IT
  ```

### 📝 Similar Questions:

**Q2.1**: Pass student information from controller to view
- **Expected Output**:
  ```
  Student Name: Priya Sharma
  Roll No: 101
  Class: BCA-5th Semester
  ```

**Q2.2**: Pass product details from controller to Blade view
- **Expected Output**:
  ```
  Product Name: Laptop
  Brand: Dell
  Price: ₹50,000
  ```

**Q2.3**: Pass book information from controller to view
- **Expected Output**:
  ```
  Book Title: Laravel Framework
  Author: Taylor Otwell
  ISBN: 978-1234567890
  ```

**Q2.4**: Pass company employee data to Blade
- **Expected Output**:
  ```
  Employee ID: E001
  Name: Amit Kumar
  Designation: Software Developer
  Salary: ₹45,000
  ```

---

## 🔥 TYPE 3: Product List with Controller & Group Routing

### Pattern from Image 2: Two-View Application

**Original Question**: Create product list with controller group routing
- **First View** (`/products`): Display list of products
- **Second View** (`/products/2`): Display single product details

### 📝 Similar Questions:

**Q3.1**: Create a book store with two views
- **View 1** (`/books`): List all books with prices
- **View 2** (`/books/3`): Show book details with description

**Q3.2**: Create a mobile shop application
- **View 1** (`/mobiles`): Display mobile list
- **View 2** (`/mobiles/1`): Show mobile specifications

**Q3.3**: Create a restaurant menu system
- **View 1** (`/menu`): Display food items with prices
- **View 2** (`/menu/4`): Show item details

**Q3.4**: Create a car showroom listing
- **View 1** (`/cars`): List available cars
- **View 2** (`/cars/2`): Show car details

**Q3.5**: Create a course catalog
- **View 1** (`/courses`): Display all courses
- **View 2** (`/courses/5`): Show course details

---

## 🔥 TYPE 4: Form Validation Questions

### Pattern from Image 3: Registration Form with Validation

**Original Question**: User registration form with validation rules
- Name: Required, min 3 characters
- Email: Required, valid email
- Password: Required, min 6 characters

### 📝 Similar Questions:

**Q4.1**: Student registration form
- Roll No: Required, numeric
- Name: Required, min 2 characters
- Email: Required, email format
- Phone: Required, 10 digits

**Q4.2**: Product addition form
- Product Name: Required, max 100 characters
- Price: Required, numeric, min 1
- Category: Required
- Description: Required, min 10 characters

**Q4.3**: Employee registration form
- Employee ID: Required, alphanumeric
- Name: Required
- Email: Required, unique
- Password: Required, min 8 characters, confirmed

**Q4.4**: Contact form validation
- Name: Required
- Email: Required, email
- Subject: Required, max 200 characters
- Message: Required, min 20 characters

---

## 🔥 TYPE 5: Mathematical Operations in Controller (YOUR PATTERN)

### Pattern: Controller Logic with Blade Display + Styling + Navigation

**Your Question**: Using Laravel develop a program to reverse a number using controller logic, display the result in Blade view with teal colored text and provide navigation to another page.

### 📝 Similar Questions (Very High Probability):

**Q5.1**: Calculate factorial of a number
- **Input**: `/factorial/5`
- **Controller Logic**: Calculate factorial using loop/recursion
- **Blade Display**: Show result with **blue colored text**
- **Navigation**: Link to "Calculate Another" page
- **Expected Output**:
  ```
  Number: 5
  Factorial: 120
  ```

**Q5.2**: Check if number is prime
- **Input**: `/prime/17`
- **Controller Logic**: Prime number checking algorithm
- **Blade Display**: Show result with **green colored text** (if prime) or **red** (if not)
- **Navigation**: Link to home page
- **Expected Output**:
  ```
  Number: 17
  Status: Prime Number
  ```

**Q5.3**: Calculate sum of digits
- **Input**: `/sum-digits/12345`
- **Controller Logic**: Extract and sum digits
- **Blade Display**: Show result with **orange colored text**
- **Navigation**: Link to "Try Another Number" page
- **Expected Output**:
  ```
  Number: 12345
  Sum of Digits: 15
  ```

**Q5.4**: Check if number is palindrome
- **Input**: `/palindrome/12321`
- **Controller Logic**: Reverse and compare
- **Blade Display**: Show result with **purple colored text**
- **Navigation**: Link to check another number
- **Expected Output**:
  ```
  Number: 12321
  Status: Palindrome Number
  ```

**Q5.5**: Calculate power of a number
- **Input**: `/power/2/10` (base 2, exponent 10)
- **Controller Logic**: Calculate power
- **Blade Display**: Show result with **dark green colored text**
- **Navigation**: Link to calculator page
- **Expected Output**:
  ```
  Base: 2
  Exponent: 10
  Result: 1024
  ```

**Q5.6**: Find Fibonacci series up to N terms
- **Input**: `/fibonacci/10`
- **Controller Logic**: Generate Fibonacci sequence
- **Blade Display**: Show series with **brown colored text**
- **Navigation**: Link to "Generate Another Series" page
- **Expected Output**:
  ```
  Number of Terms: 10
  Fibonacci Series: 0, 1, 1, 2, 3, 5, 8, 13, 21, 34
  ```

**Q5.7**: Check Armstrong number
- **Input**: `/armstrong/153`
- **Controller Logic**: Calculate sum of cubes of digits
- **Blade Display**: Show result with **teal colored text**
- **Navigation**: Link to home page
- **Expected Output**:
  ```
  Number: 153
  Status: Armstrong Number
  ```

**Q5.8**: Calculate GCD (Greatest Common Divisor)
- **Input**: `/gcd/48/18`
- **Controller Logic**: Euclidean algorithm
- **Blade Display**: Show result with **navy colored text**
- **Navigation**: Link to "Calculate GCD of Another Pair" page
- **Expected Output**:
  ```
  First Number: 48
  Second Number: 18
  GCD: 6
  ```

**Q5.9**: Convert decimal to binary
- **Input**: `/binary/42`
- **Controller Logic**: Decimal to binary conversion
- **Blade Display**: Show result with **maroon colored text**
- **Navigation**: Link to converter page
- **Expected Output**:
  ```
  Decimal Number: 42
  Binary: 101010
  ```

**Q5.10**: Calculate simple interest
- **Input**: `/interest/10000/5/2` (principal, rate, time)
- **Controller Logic**: SI = (P × R × T) / 100
- **Blade Display**: Show result with **teal colored text**
- **Navigation**: Link to "Calculate Again" page
- **Expected Output**:
  ```
  Principal: ₹10000
  Rate: 5%
  Time: 2 years
  Simple Interest: ₹1000
  ```

---

## 🔥 TYPE 6: Array Operations in Controller

### Pattern: Array Processing + Blade Display + Styling

**Q6.1**: Find maximum and minimum from array
- **Controller**: Array of 10 numbers
- **Logic**: Find max and min values
- **Blade Display**: Show results with **red (max)** and **blue (min)** colored text
- **Navigation**: Link to "Process Another Array" page

**Q6.2**: Sort array and display
- **Controller**: Array of student names
- **Logic**: Sort alphabetically
- **Blade Display**: Show sorted list with **green colored text**
- **Navigation**: Link to home page

**Q6.3**: Count vowels and consonants in array
- **Controller**: Array of strings
- **Logic**: Count vowels and consonants
- **Blade Display**: Show counts with different colors
- **Navigation**: Link to "Analyze Another Text" page

**Q6.4**: Remove duplicates from array
- **Controller**: Array with duplicate values
- **Logic**: Remove duplicates
- **Blade Display**: Show original and filtered arrays
- **Navigation**: Link to page with back button

---

## 🔥 TYPE 7: String Operations in Controller

### Pattern: String Manipulation + Blade Display

**Q7.1**: Count vowels in a string
- **Input**: `/vowels/Hello World`
- **Controller Logic**: Count vowels
- **Blade Display**: Show count with **teal colored text**
- **Navigation**: Link to "Check Another String" page

**Q7.2**: Check if string is palindrome
- **Input**: `/string-palindrome/madam`
- **Controller Logic**: Reverse and compare
- **Blade Display**: Show result with colored text
- **Navigation**: Link to home page

**Q7.3**: Count words in a string
- **Input**: `/word-count/Laravel is awesome`
- **Controller Logic**: Count words
- **Blade Display**: Show count with **orange colored text**
- **Navigation**: Link to another page

**Q7.4**: Convert string to uppercase/lowercase
- **Input**: `/convert/Hello World/upper`
- **Controller Logic**: String transformation
- **Blade Display**: Show original and converted text
- **Navigation**: Link to converter page

---

## 🔥 TYPE 8: Cookie/Session + Controller Logic

### Pattern: Data Storage + Display + Navigation

**Q8.1**: Store calculated result in session
- **Input**: Number to reverse
- **Controller**: Reverse number and store in session
- **Blade**: Display result from session with **teal color**
- **Navigation**: Link to clear session page

**Q8.2**: Store user preference in cookie
- **Controller**: Set cookie with color preference
- **Blade**: Display text in cookie-specified color
- **Navigation**: Link to change preference page

**Q8.3**: Session-based counter
- **Controller**: Increment session counter on each visit
- **Blade**: Display visit count with **blue colored text**
- **Navigation**: Link to reset counter page

---

## 🔥 TYPE 9: Complete Application Questions

### Pattern: Multi-Feature Integration

**Q9.1**: Number Calculator Application
- **Route 1**: `/calculator/reverse/{number}` - Reverse number
- **Route 2**: `/calculator/factorial/{number}` - Factorial
- **Route 3**: `/calculator/prime/{number}` - Prime check
- **Blade**: Display all results with different colors
- **Navigation**: Links between all calculator pages

**Q9.2**: Student Grade Calculator
- **Input**: Marks in 5 subjects
- **Controller**: Calculate total, percentage, grade
- **Blade**: Display result card with **teal colored grade**
- **Navigation**: Link to "Calculate Another" page

**Q9.3**: BMI Calculator
- **Input**: Weight and height
- **Controller**: Calculate BMI
- **Blade**: Display BMI with color coding (underweight/normal/overweight)
- **Navigation**: Link to BMI chart page

---

## 🎯 YOUR SPECIFIC QUESTION - Complete Solution

### Question: Reverse Number with Controller Logic

**Requirement**: Using Laravel develop a program to reverse a number using controller logic, display the result in Blade view with teal colored text and provide navigation to another page.

#### Solution:

**Step 1: Create Controller**
```bash
php artisan make:controller NumberController
```

**Step 2: Controller Code** (`app/Http/Controllers/NumberController.php`)
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class NumberController extends Controller
{
    // Show input form
    public function showForm()
    {
        return view('reverse-form');
    }

    // Reverse number logic
    public function reverseNumber(Request $request)
    {
        $number = $request->input('number');
        
        // Validate input
        if (!is_numeric($number)) {
            return back()->with('error', 'Please enter a valid number');
        }
        
        // Reverse the number
        $reversed = strrev($number);
        
        // Pass to view
        return view('reverse-result', compact('number', 'reversed'));
    }

    // Alternative: Route parameter method
    public function reverseFromUrl($number)
    {
        $reversed = strrev($number);
        return view('reverse-result', compact('number', 'reversed'));
    }
}
```

**Step 3: Routes** (`routes/web.php`)
```php
use App\Http\Controllers\NumberController;
use Illuminate\Support\Facades\Route;

// Show form
Route::get('/reverse', [NumberController::class, 'showForm']);

// Process form submission
Route::post('/reverse', [NumberController::class, 'reverseNumber']);

// Direct URL method
Route::get('/reverse/{number}', [NumberController::class, 'reverseFromUrl'])
    ->where('number', '[0-9]+');

// Navigation page
Route::get('/home', function() {
    return view('home');
})->name('home');
```

**Step 4: Input Form View** (`resources/views/reverse-form.blade.php`)
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Reverse Number</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 50px;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 500px;
            margin: 0 auto;
            background: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 {
            color: #008080;
            text-align: center;
        }
        input[type="number"] {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: 2px solid #008080;
            border-radius: 5px;
            font-size: 16px;
        }
        button {
            width: 100%;
            padding: 12px;
            background-color: #008080;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 16px;
            cursor: pointer;
        }
        button:hover {
            background-color: #006666;
        }
        .error {
            color: red;
            text-align: center;
            margin: 10px 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Number Reverser</h1>
        
        @if(session('error'))
            <p class="error">{{ session('error') }}</p>
        @endif
        
        <form method="POST" action="/reverse">
            @csrf
            <label><strong>Enter a Number:</strong></label>
            <input type="number" name="number" required placeholder="e.g., 12345">
            <button type="submit">Reverse Number</button>
        </form>
    </div>
</body>
</html>
```

**Step 5: Result View** (`resources/views/reverse-result.blade.php`)
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Reversed Number Result</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 50px;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            text-align: center;
        }
        h1 {
            color: #008080;
        }
        .result-box {
            margin: 30px 0;
            padding: 20px;
            background-color: #f0f8f8;
            border-radius: 10px;
            border: 2px solid #008080;
        }
        .number-display {
            font-size: 24px;
            color: #008080; /* TEAL COLOR */
            font-weight: bold;
            margin: 15px 0;
        }
        .label {
            font-size: 18px;
            color: #333;
            margin: 10px 0;
        }
        .nav-links {
            margin-top: 30px;
        }
        .nav-links a {
            display: inline-block;
            padding: 12px 25px;
            margin: 10px;
            background-color: #008080;
            color: white;
            text-decoration: none;
            border-radius: 5px;
            font-size: 16px;
        }
        .nav-links a:hover {
            background-color: #006666;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Number Reversal Result</h1>
        
        <div class="result-box">
            <p class="label"><strong>Original Number:</strong></p>
            <p class="number-display">{{ $number }}</p>
            
            <hr style="border: 1px solid #008080; margin: 20px 0;">
            
            <p class="label"><strong>Reversed Number:</strong></p>
            <p class="number-display">{{ $reversed }}</p>
        </div>
        
        <div class="nav-links">
            <a href="/reverse">Reverse Another Number</a>
            <a href="{{ route('home') }}">Go to Home</a>
        </div>
    </div>
</body>
</html>
```

**Step 6: Home Page** (`resources/views/home.blade.php`)
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Home</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 50px;
            background-color: #f5f5f5;
            text-align: center;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            padding: 40px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 {
            color: #008080;
        }
        a {
            display: inline-block;
            padding: 12px 30px;
            margin: 20px;
            background-color: #008080;
            color: white;
            text-decoration: none;
            border-radius: 5px;
            font-size: 16px;
        }
        a:hover {
            background-color: #006666;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Welcome to Number Calculator</h1>
        <p style="font-size: 18px; color: #666;">Choose an operation:</p>
        <a href="/reverse">Reverse Number</a>
        <a href="/factorial">Factorial Calculator</a>
        <a href="/prime">Prime Checker</a>
    </div>
</body>
</html>
```

---

## 📊 Quick Reference: Color Codes for Blade

```blade
<!-- Teal -->
<p style="color: #008080;">Text</p>
<p style="color: teal;">Text</p>

<!-- Blue -->
<p style="color: #0000FF;">Text</p>
<p style="color: blue;">Text</p>

<!-- Red -->
<p style="color: #FF0000;">Text</p>
<p style="color: red;">Text</p>

<!-- Green -->
<p style="color: #008000;">Text</p>
<p style="color: green;">Text</p>

<!-- Orange -->
<p style="color: #FFA500;">Text</p>
<p style="color: orange;">Text</p>
```

---

## 🎯 Exam Tips

### ✅ Do's:
1. Always use controller for business logic
2. Display results in Blade with specified colors
3. Provide navigation links between pages
4. Use proper validation
5. Add error handling
6. Style output clearly

### ❌ Don'ts:
1. Don't write logic in routes (use controllers)
2. Don't forget navigation links
3. Don't skip input validation
4. Don't hardcode colors (use consistent scheme)

### 🔥 Most Likely Questions:
1. Reverse number (95% chance) ✓
2. Factorial calculation (90% chance)
3. Prime number check (85% chance)
4. Palindrome check (80% chance)
5. Array sorting (75% chance)
6. Form validation (95% chance)
7. Route parameters (90% chance)

---

**Total Possible Questions Identified: 50+**

**Back to:** [Main Study Guide](../STUDY_GUIDE.md) | [Exam Preparation](README.md)
