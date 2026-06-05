# Unit VI: Databases, Migrations, Query Builder, Eloquent ORM, MongoDB & REST APIs

## 📚 Table of Contents
- [Model Creation & Active Record](#model-creation--active-record)
- [Database Migrations](#database-migrations)
- [CRUD using Query Builder](#crud-using-query-builder)
- [Database Seeding & Factories](#database-seeding--factories)
- [Using MongoDB with Laravel](#using-mongodb-with-laravel)
- [CRUD using Eloquent ORM](#crud-using-eloquent-orm)
- [Implementing REST APIs](#implementing-rest-apis)
- [Common Mistakes to Avoid (Gotchas)](#common-mistakes-to-avoid-gotchas)
- [Keywords to Remember](#keywords-to-remember)
- [Exam Practice Questions](#exam-practice-questions)

---

## Model Creation & Active Record

Laravel Eloquent is an **Object-Relational Mapper (ORM)** that uses the **Active Record architectural pattern**. Each model class corresponds to a database table, and an instance of a model represents a single row in that table.

### Model Naming Conventions
Laravel automatically maps model names to their plural table names:

| Model Class (Singular + PascalCase) | DB Table Name (Plural + snake_case) |
|-------------------------------------|-------------------------------------|
| `User` | `users` |
| `Post` | `posts` |
| `FlightBooking` | `flight_bookings` |

### Generate Model
To create a model:
```bash
php artisan make:model Post
```

To create a model along with a migration file:
```bash
php artisan make:model Post -m
```

### Basic Model Properties
If your database table doesn't follow Laravel's default conventions, you can customize them inside the model class:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    // Custom table mapping
    protected $table = 'my_custom_posts_table';

    // Custom primary key (Default is 'id')
    protected $primaryKey = 'post_uuid';

    // Disable timestamps if table lacks 'created_at' and 'updated_at'
    public $timestamps = false;

    // Mass Assignment Protection
    // Attributes listed here can be created/updated in bulk using User::create()
    protected $fillable = ['title', 'content', 'status'];
}
```

---

## Database Migrations

Migrations act like **version control for your database**, allowing teams to easily modify and share database schemas.

### 1. Generating a Migration
```bash
php artisan make:migration create_posts_table
```

### 2. Migration Class Structure
Every migration file contains an `up()` method (to create/modify tables) and a `down()` method (to rollback/undo those changes).

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePostsTable extends Migration
{
    // Run the migrations to create structure
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id(); // Auto-incrementing primary key (bigint)
            $table->string('title'); // VARCHAR column
            $table->text('content'); // TEXT column
            $table->foreignId('user_id')->constrained()->onDelete('cascade'); // Foreign Key mapping to users table
            $table->boolean('is_published')->default(false); // BOOLEAN column
            $table->timestamps(); // Generates created_at and updated_at column
        });
    }

    // Reverse the migrations
    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
}
```

### 3. Migration Commands
*   **Run migrations:** `php artisan migrate`
*   **Rollback last batch:** `php artisan migrate:rollback`
*   **Rollback specific number of steps:** `php artisan migrate:rollback --step=2`
*   **Reset database:** `php artisan migrate:reset` (undoes ALL migrations)
*   **Fresh setup:** `php artisan migrate:fresh` (drops all tables and re-runs migrations from scratch)

---

## CRUD using Query Builder

Laravel's **Query Builder** uses the `DB` facade to execute SQL queries. It automatically uses PDO parameter binding to protect your application against **SQL injection attacks**.

```
                       ┌───────────────────────────────┐
                       │           DB Facade           │
                       └───────────────┬───────────────┘
                                       │
                ┌──────────────────────┴──────────────────────┐
                ▼                                             ▼
       Query Builder (Fluent API)                       Raw SQL Queries
   DB::table('users')->get()                     DB::select('SELECT * FROM users')
```

### 1. Create (Insert)
```php
use Illuminate\Support\Facades\DB;

DB::table('posts')->insert([
    'title' => 'My First Post',
    'content' => 'This is using Query Builder.',
    'user_id' => 1,
    'created_at' => now(),
    'updated_at' => now()
]);
```

### 2. Read (Select)
```php
// Retrieve all rows
$posts = DB::table('posts')->get();

// Retrieve a single row matching condition
$post = DB::table('posts')->where('id', 1)->first();

// Retrieve a single column value
$title = DB::table('posts')->where('id', 1)->value('title');

// Chunk results (useful for processing massive datasets)
DB::table('posts')->orderBy('id')->chunk(100, function ($posts) {
    foreach ($posts as $post) {
        // process $post
    }
});
```

### 3. Update
```php
DB::table('posts')
    ->where('id', 1)
    ->update(['title' => 'Updated Title!']);
```

### 4. Delete
```php
DB::table('posts')
    ->where('id', 1)
    ->delete();
```

---

## Database Seeding & Factories

**Seeding** allows you to populate your database with dummy or test data. This is extremely helpful for testing and development.

### 1. Generating a Seeder
```bash
php artisan make:seeder PostSeeder
```
This generates `database/seeders/PostSeeder.php`.

### 2. Writing Seeder Logic
```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class PostSeeder extends Seeder
{
    public function run(): void
    {
        DB::table('posts')->insert([
            ['title' => 'Exam Prep', 'content' => 'Score 100% in MVC', 'user_id' => 1],
            ['title' => 'Laravel ORM', 'content' => 'Eloquent is amazing', 'user_id' => 1],
        ]);
    }
}
```

### 3. Registering & Running Seeders
Register your seeder in `database/seeders/DatabaseSeeder.php`:

```php
public function run(): void
{
    $this->call([
        PostSeeder::class,
    ]);
}
```

Run seed commands:
```bash
# Run the default DatabaseSeeder
php artisan db:seed

# Run a specific seeder class
php artisan db:seed --class=PostSeeder

# Refresh database and seed in one step
php artisan migrate:fresh --seed
```

---

## Using MongoDB with Laravel

MongoDB is a document-based NoSQL database. While Laravel natively supports SQL databases (MySQL, PostgreSQL, SQLite, SQL Server), you can use MongoDB in Laravel projects using the official **Laravel MongoDB** package (`mongodb/laravel-mongodb`).

### Comparison: SQL vs NoSQL (MongoDB)

| Concept | SQL (MySQL) | NoSQL (MongoDB) |
|---------|-------------|-----------------|
| **Data Structure** | Structured Tables with Rows | Dynamic JSON-like Documents |
| **Schema** | Rigid (must define migrations) | Flexible (schemaless) |
| **Relationships** | Joins (Primary/Foreign Keys) | Embedded Documents / References |
| **Artisan Model** | Extends `Illuminate\Database\Eloquent\Model` | Extends `MongoDB\Laravel\Eloquent\Model` |

### Setting Up MongoDB in Laravel (.env)
```env
DB_CONNECTION=mongodb
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/database_name
```

### MongoDB Eloquent Model Example
```php
<?php

namespace App\Models;

use MongoDB\Laravel\Eloquent\Model; // Extends MongoDB Eloquent model instead of standard SQL

class MongoProduct extends Model
{
    protected $connection = 'mongodb'; // Specify connection
    protected $collection = 'products'; // Mongo collection name instead of table
    protected $fillable = ['product_name', 'price', 'tags'];
}
```

---

## CRUD using Eloquent ORM

Eloquent ORM is a cleaner, more object-oriented wrapper around Query Builder.

### Query Builder vs Eloquent ORM

| Feature | Query Builder | Eloquent ORM |
|---------|---------------|--------------|
| **Syntax Style** | Fluent, table-oriented (`DB::table('posts')`) | Object-oriented (`Post::all()`) |
| **Data Representation** | Returns StdClass Objects | Returns Model Instances |
| **Performance** | Faster (less memory overhead) | Slower (instantiates objects) |
| **Relationships** | Done manually using SQL joins | Managed automatically using relationships |

### 1. Create (Insert)
```php
// Method 1: Using Model Instantiation
$post = new Post;
$post->title = 'Eloquent Intro';
$post->content = 'Eloquent ORM simplifies CRUD.';
$post->user_id = 1;
$post->save();

// Method 2: Using Mass Assignment (Requires $fillable in Model)
$post = Post::create([
    'title' => 'Mass Assigned Post',
    'content' => 'Requires fillable properties.',
    'user_id' => 1
]);
```

### 2. Read (Select)
```php
// Retrieve all records
$posts = Post::all();

// Retrieve by primary key
$post = Post::find(5);

// Fail gracefully if model not found (throws 404 page)
$post = Post::findOrFail(5);

// Conditional query
$posts = Post::where('is_published', true)->get();
```

### 3. Update
```php
// Find model and save updates
$post = Post::find(1);
$post->title = 'New Title';
$post->save();

// Mass update
Post::where('is_published', false)->update(['is_published' => true]);
```

### 4. Delete
```php
// Method 1: Using instance
$post = Post::find(1);
$post->delete();

// Method 2: Delete by ID without retrieving first
Post::destroy(1);
Post::destroy([2, 3, 4]); // delete multiple IDs
```

### 5. Eloquent Relationships (Definitions)
*   **One to One** (`hasOne` / `belongsTo`): E.g., A User has one Profile.
    ```php
    // User.php
    public function profile() { return $this->hasOne(Profile::class); }
    ```
*   **One to Many** (`hasMany` / `belongsTo`): E.g., A User has many Posts.
    ```php
    // User.php
    public function posts() { return $this->hasMany(Post::class); }
    ```
*   **Many to Many** (`belongsToMany`): E.g., A Post belongs to many Tags.
    ```php
    // Post.php
    public function tags() { return $this->belongsToMany(Tag::class); }
    ```

---

## Implementing REST APIs

REST APIs communicate via JSON and handle stateless requests. API routes are declared in `routes/api.php` instead of `routes/web.php`.

### 1. Generate an API Controller
API controllers omit forms and HTML layouts, focusing solely on JSON data.
```bash
php artisan make:controller Api/BookController --api
```
This generates methods: `index`, `store`, `show`, `update`, `destroy`. (No `create` or `edit` forms).

### 2. Register API Routes (`routes/api.php`)
All routes in `routes/api.php` are automatically prefixed with `/api`.
```php
use App\Http\Controllers\Api\BookController;

Route::apiResource('books', BookController::class);
```

### 3. API Controller Implementation
```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Book;
use Illuminate\Http\Request;

class BookController extends Controller
{
    // Fetch all books (GET /api/books)
    public function index()
    {
        $books = Book::all();
        return response()->json([
            'success' => true,
            'data' => $books
        ], 200);
    }

    // Save new book (POST /api/books)
    public function store(Request $request)
    {
        $validated = $request->validate([
            'title' => 'required|string|max:100',
            'author' => 'required|string',
        ]);

        $book = Book::create($validated);

        return response()->json([
            'success' => true,
            'message' => 'Book created successfully!',
            'data' => $book
        ], 201); // 201 Created status
    }

    // Fetch individual book (GET /api/books/{id})
    public function show(string $id)
    {
        $book = Book::find($id);

        if (!$book) {
            return response()->json(['success' => false, 'message' => 'Book not found'], 404);
        }

        return response()->json(['success' => true, 'data' => $book], 200);
    }

    // Update book (PUT /api/books/{id})
    public function update(Request $request, string $id)
    {
        $book = Book::find($id);

        if (!$book) {
            return response()->json(['success' => false, 'message' => 'Book not found'], 404);
        }

        $book->update($request->all());

        return response()->json(['success' => true, 'message' => 'Book updated!', 'data' => $book], 200);
    }

    // Delete book (DELETE /api/books/{id})
    public function destroy(string $id)
    {
        $deleted = Book::destroy($id);

        if (!$deleted) {
            return response()->json(['success' => false, 'message' => 'Book not found'], 404);
        }

        return response()->json(['success' => true, 'message' => 'Book deleted successfully.'], 200);
    }
}
```

---

## Common Mistakes to Avoid (Gotchas)

### ❌ Forgetting `$fillable` for Mass Assignment
If you use `User::create(['name' => 'Amit'])` but forgot to add `'name'` to `$fillable` in the model, Laravel will throw a `MassAssignmentException`. Add fillable attributes to allow bulk inserts.

### ❌ Running `migrate:fresh` in Production
`php artisan migrate:fresh` will **DROP** all tables in your database before re-running migrations. Running this in production will result in complete data loss! Use standard `php artisan migrate` instead.

### ❌ Putting raw, unvalidated inputs in Query Builder
*Don't do this:*
```php
DB::select("SELECT * FROM users WHERE email = '" . $request->input('email') . "'");
```
This bypasses parameters binding, allowing **SQL Injection**.
*Do this:*
```php
DB::table('users')->where('email', $request->input('email'))->get();
```

---

## Keywords to Remember

1.  **Eloquent ORM**: An Active Record implementation that maps database tables to classes and table rows to class instances.
2.  **Active Record Pattern**: A structural design pattern where a class represents a database table, and an object represents a database record, wrapping both data access and business logic.
3.  **Migration**: Schema version files defining table columns, keys, and structures designed to make database states easily shareable.
4.  **Database Seeder**: An automated class used to populate database tables with initial test records or mock data.
5.  **REST API (Representational State Transfer)**: A stateless web service architecture that communicates using standard HTTP methods and interchanges data via JSON format.

---

## Exam Practice Questions

### Short Answer Questions (2-5 Marks)
1.  **Define Eloquent ORM. How is a model generated with its migration file?**
    *Answer:* Eloquent is Laravel's Active Record ORM. A model with its migration can be generated using Artisan command `php artisan make:model ModelName -m`.
2.  **Write the Query Builder command to select the `title` and `author` of all posts where `is_active` is true, ordered by `created_at` descending.**
    ```php
    $posts = DB::table('posts')
                ->select('title', 'author')
                ->where('is_active', true)
                ->orderBy('created_at', 'desc')
                ->get();
    ```
3.  **What is database seeding? Why is it useful?**
    *Answer:* Seeding is the process of populating database tables with mock or test data. It is useful for testing database queries, populating dropdowns with dynamic choices, and sharing dev environments.
4.  **Differentiate between `migrate:rollback` and `migrate:reset`.**
    *Answer:* `migrate:rollback` rolls back only the last batch of database migrations. `migrate:reset` rolls back all migrations ever run, clearing all tables.

### Long Answer Questions (10 Marks)
1.  **Compare Query Builder with Eloquent ORM. Detail three CRUD operations inside both systems with complete code examples.**
    *(Hint: Include comparison tables from notes. Show insert, read, and delete operations in both Query Builder (using DB facade) and Eloquent (using Model class).)*
2.  **Explain the architecture of a REST API. Write a complete RESTful controller implementation to support standard CRUD operations on a `Product` model returning JSON responses.**
    *(Hint: Refer to REST APIs section. Show all 5 API functions: index, store, show, update, destroy with validation and proper HTTP status codes.)*
