# Unit 6 Question Bank: Database Migrations, Eloquent ORM & REST APIs

## 2-Mark Questions

### Q1. Define the Active Record Architectural Pattern used in Eloquent ORM.
**Answer:**
Active Record is an architectural pattern where a model class represents a database table, and an instance of that class represents a single row (record) in the database table. The model class wraps database access logic, properties mapping, and business logic inside a single object.

### Q2. Explain the purpose of `$fillable` and `$guarded` properties inside an Eloquent model.
**Answer:**
*   `$fillable` is an array of attributes that are allowed to be mass-assigned (whitelist).
*   `$guarded` is an array of attributes that are blocked from being mass-assigned (blacklist).
They protect your application against **Mass Assignment vulnerabilities** where an attacker submits unauthorized form data (e.g. `is_admin = true`).

### Q3. Differentiate between the database commands `php artisan migrate:fresh` and `php artisan migrate:refresh`.
**Answer:**
*   `migrate:fresh` drops **all** tables in the database (regardless of whether they were created by migrations or not) and then runs all migrations from scratch.
*   `migrate:refresh` rolls back all migrations defined in the files step-by-step (running their `down()` methods) and then re-runs them.

### Q4. Write the Query Builder syntax to retrieve the first 5 records from a table named `products` where `status` is 'active'.
**Answer:**
```php
$products = DB::table('products')
              ->where('status', 'active')
              ->limit(5)
              ->get();
```

### Q5. What is a Model Factory in Laravel, and what library does it use to generate dummy values?
**Answer:**
A Model Factory is a class that defines a default template of mock values for an Eloquent model, enabling the generation of bulk testing records. It uses the **Faker** PHP library to generate realistic random data (e.g. names, emails, sentences, dates).

---

## 5-Mark Questions

### Q6. Compare SQL Databases (e.g. MySQL) with NoSQL Databases (e.g. MongoDB) in the context of Laravel application development.
**Answer:**

| Parameter | SQL Databases (MySQL, PostgreSQL) | NoSQL Databases (MongoDB) |
|-----------|----------------------------------|---------------------------|
| **Data Schema** | Rigid, predefined columns, tables, and datatypes. | Flexible, dynamic JSON-like document structures. |
| **Migrations** | Required to alter schema (creating tables, renaming columns). | Schemaless. No migrations needed to add new properties. |
| **Relationships** | Enforces joins and foreign key constraints on the DB engine. | Embedded documents or loose references without strict enforcement. |
| **Eloquent Integration** | Natively supported. Models extend `Illuminate\Database\Eloquent\Model`. | Requires third-party package. Models extend `MongoDB\Laravel\Eloquent\Model`. |
| **Scaling** | Vertical scaling (adding hardware resources to single server). | Horizontal scaling (sharding across multiple server clusters). |

### Q7. Explain the differences between `migrate:rollback`, `migrate:reset`, and `migrate:refresh`.
**Answer:**
*   **`migrate:rollback`**: Undoes the changes made by the *most recent batch* of migrations. If 5 migrations were run at once in the last command, calling rollback reverses only those 5 migrations.
*   **`migrate:reset`**: Rolls back *every single migration* ever executed by the application. It runs the `down()` method of all migrations, effectively leaving the database empty of migration tables but preserving the migration history table itself.
*   **`migrate:refresh`**: Resets the entire database by rolling back all migrations (acting like `migrate:reset`), and immediately runs all of them again. This is useful for re-building the schema without deleting tables created manually or outside of migrations.

### Q8. Discuss Eloquent Relationships, writing the code to define a One-to-Many relationship between a `User` model and a `Post` model.
**Answer:**
A One-to-Many relationship indicates that a single record in one table can be associated with multiple records in another. In this scenario, one user can write many posts, but each post belongs to a single user.

#### 1. Define relationship in the `User` model (`app/Models/User.php`):
```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class User extends Model
{
    public function posts(): HasMany
    {
        return $this->hasMany(Post::class); // Looks for user_id in tbl_posts
    }
}
```

#### 2. Define inverse relationship in the `Post` model (`app/Models/Post.php`):
```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Post extends Model
{
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class); // Maps user_id foreign key back to users table
    }
}
```

---

## 10-Mark Questions

### Q9. Compare DB Query Builder with Eloquent ORM. Detail three CRUD operations inside both systems with complete code examples.
**Answer:**

#### 1. Comparative Analysis
*   **Performance**: DB Query Builder is faster and consumes less memory because it runs direct queries and returns generic PHP objects (`stdClass`). Eloquent ORM has overhead because it instantiates full PHP model objects, hydrates data, and handles database hooks.
*   **Aesthetics & Readability**: Eloquent ORM provides cleaner, highly readable, object-oriented syntax.
*   **Relationships**: Eloquent handles relationships using class methods, whereas Query Builder requires complex `leftJoin` or `innerJoin` statements.

#### 2. CRUD Comparison Examples

##### Operation A: Create (Insert Record)
*   **DB Query Builder**:
    ```php
    use Illuminate\Support\Facades\DB;

    DB::table('books')->insert([
        'title' => 'The Great Gatsby',
        'author' => 'F. Scott Fitzgerald',
        'price' => 12.99,
        'created_at' => now(),
        'updated_at' => now()
    ]);
    ```
*   **Eloquent ORM** (Requires `$fillable = ['title', 'author', 'price']` in `Book` model):
    ```php
    use App\Models\Book;

    Book::create([
        'title' => 'The Great Gatsby',
        'author' => 'F. Scott Fitzgerald',
        'price' => 12.99
    ]);
    ```

##### Operation B: Read (Querying Multiple Records)
*   **DB Query Builder**:
    ```php
    $books = DB::table('books')
               ->where('price', '>', 10.00)
               ->orderBy('title', 'asc')
               ->get();
    ```
*   **Eloquent ORM**:
    ```php
    $books = Book::where('price', '>', 10.00)
                 ->orderBy('title', 'asc')
                 ->get();
    ```

##### Operation C: Delete (Removing Record by ID)
*   **DB Query Builder**:
    ```php
    DB::table('books')->where('id', 15)->delete();
    ```
*   **Eloquent ORM**:
    ```php
    Book::destroy(15);
    // OR:
    // $book = Book::findOrFail(15);
    // $book->delete();
    ```

---

### Q10. Explain the architecture of REST APIs. Write a complete RESTful controller implementation to support standard CRUD operations on a `Product` model returning JSON responses.
**Answer:**

#### 1. REST API Architecture in Laravel
REST (Representational State Transfer) is an architectural style for designing networked applications. It relies on stateless client-server communication using HTTP protocols.
*   **Statelessness**: Every API request must contain all credentials and parameters needed to execute (no session states are saved).
*   **Standard Verbs**: Maps HTTP verbs to CRUD tasks: `GET` (Read), `POST` (Create), `PUT`/`PATCH` (Update), `DELETE` (Delete).
*   **Response Payload**: Interchanges data via JSON formats along with appropriate HTTP status codes:
    *   `200 OK`: Successful read or update.
    *   `201 Created`: Successful creation of record.
    *   `404 Not Found`: Target record doesn't exist.
    *   `422 Unprocessable Content`: Validation rules failed.

#### 2. REST API Controller Code (`app/Http/Controllers/Api/ProductController.php`)
```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Product;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;

class ProductController extends Controller
{
    /**
     * GET /api/products
     * Return all products.
     */
    public function index(): JsonResponse
    {
        $products = Product::all();
        return response()->json([
            'success' => true,
            'data' => $products
        ], 200);
    }

    /**
     * POST /api/products
     * Save new product.
     */
    public function store(Request $request): JsonResponse
    {
        $validated = $request->validate([
            'name' => 'required|string|max:100',
            'price' => 'required|numeric|min:0',
            'stock' => 'required|integer|min:0',
        ]);

        $product = Product::create($validated);

        return response()->json([
            'success' => true,
            'message' => 'Product created successfully!',
            'data' => $product
        ], 201);
    }

    /**
     * GET /api/products/{id}
     * Retrieve individual product.
     */
    public function show(string $id): JsonResponse
    {
        $product = Product::find($id);

        if (!$product) {
            return response()->json([
                'success' => false,
                'message' => 'Product not found.'
            ], 404);
        }

        return response()->json([
            'success' => true,
            'data' => $product
        ], 200);
    }

    /**
     * PUT/PATCH /api/products/{id}
     * Update existing product.
     */
    public function update(Request $request, string $id): JsonResponse
    {
        $product = Product::find($id);

        if (!$product) {
            return response()->json([
                'success' => false,
                'message' => 'Product not found.'
            ], 404);
        }

        $validated = $request->validate([
            'name' => 'sometimes|required|string|max:100',
            'price' => 'sometimes|required|numeric|min:0',
            'stock' => 'sometimes|required|integer|min:0',
        ]);

        $product->update($validated);

        return response()->json([
            'success' => true,
            'message' => 'Product updated successfully.',
            'data' => $product
        ], 200);
    }

    /**
     * DELETE /api/products/{id}
     * Delete product.
     */
    public function destroy(string $id): JsonResponse
    {
        $deleted = Product::destroy($id);

        if (!$deleted) {
            return response()->json([
                'success' => false,
                'message' => 'Product not found.'
            ], 404);
        }

        return response()->json([
            'success' => true,
            'message' => 'Product deleted successfully.'
        ], 200);
    }
}
```
