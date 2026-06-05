# Unit 6: Database Migrations, Eloquent ORM & REST APIs

## 📚 Course Outcomes Coverage
*   **CO6**: Design schemas, execute database migrations, utilize Eloquent ORM relations, and build RESTful JSON API endpoints.

---

## Model Creation & Active Record Pattern

Laravel's Eloquent ORM implements the **Active Record architectural pattern**. In this pattern, each model class represents a database table, and each instance of a model corresponds to a single row in that table.

### Model Naming & Database Conventions
Laravel uses naming conventions to automatically map models to database tables:
*   **Models**: Singular, PascalCase (e.g., `FlightBooking`, `Post`).
*   **Tables**: Plural, snake_case (e.g., `flight_bookings`, `posts`).

### 1. Generating a Model
Generate a model class using the Artisan CLI:
```bash
php artisan make:model Post
```
To generate both a Model and its corresponding database migration file:
```bash
php artisan make:model Post -m
```

### 2. Customizing Model Conventions
If you must integrate with a legacy database schema or custom structure, override standard conventions by defining public/protected properties inside your model:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    // 1. Map to a non-standard database table
    protected $table = 'tbl_posts';

    // 2. Define a custom primary key (Default is 'id')
    protected $primaryKey = 'post_uuid';

    // 3. Inform Eloquent that the primary key is non-incrementing / non-numeric
    public $incrementing = false;
    protected $keyType = 'string';

    // 4. Disable automatic timestamps (created_at / updated_at)
    public $timestamps = false;

    // 5. Specify custom names for timestamp columns if enabled
    const CREATED_AT = 'date_created';
    const UPDATED_AT = 'date_modified';

    // 6. Define Mass Assignment attributes (Allowlist)
    protected $fillable = ['title', 'content', 'status', 'user_id'];

    // 7. Alternatively, define Guarded attributes (Denylist)
    // protected $guarded = ['id', 'is_admin'];
}
```

> [!WARNING]
> Do not define both `$fillable` and `$guarded` in the same model class, as they represent opposing logic. Use `$fillable` as a best practice to protect against Mass Assignment vulnerabilities.

---

## Database Migrations

Migrations are **version control for your database**, allowing your team to define, alter, and share the database schema structure across environments.

### 1. Structure of a Migration Class
Every migration file contains a class with two methods:
*   **`up()`**: Runs when executing migrations to create or modify tables.
*   **`down()`**: Runs when rolling back migrations to reverse the actions in the `up` method.

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id(); // BigInt Auto-Incrementing Primary Key
            $table->string('title', 150); // VARCHAR(150)
            $table->text('body'); // TEXT
            $table->unsignedInteger('views')->default(0); // INT UNSIGNED DEFAULT 0
            $table->boolean('is_published')->default(false); // TINYINT(1) / BOOLEAN
            
            // Foreign Key column mapping to users.id with cascade delete
            $table->foreignId('user_id')->constrained('users')->onDelete('cascade');
            
            $table->timestamps(); // created_at & updated_at columns
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

### 2. Migration Command reference

| Command | Action |
|---------|--------|
| `php artisan migrate` | Executes all pending migrations. |
| `php artisan migrate:rollback` | Reverts the last "batch" of migrations that were run. |
| `php artisan migrate:rollback --step=2` | Reverts the last two individual migration actions. |
| `php artisan migrate:reset` | Reverts ALL database migrations, leaving a blank database. |
| `php artisan migrate:refresh` | Rolls back all migrations and then re-runs them from the beginning. |
| `php artisan migrate:fresh` | **Drops all tables** on the database and runs all migrations from scratch. |
| `php artisan migrate:status` | Shows a checklist indicating which migrations have run and which are pending. |

> [!CAUTION]
> Never run `migrate:fresh` or `migrate:reset` on a production server. It will drop all tables, leading to permanent data loss!

---

## Query Builder vs Eloquent ORM

Laravel offers two core ways to interact with SQL databases: **DB Query Builder** and **Eloquent ORM**.

### Comparative Analysis

| Feature | DB Query Builder | Eloquent ORM |
|---------|------------------|--------------|
| **Architecture** | Fluent interface utilizing raw SQL query builders. | Active Record Pattern mapping tables to PHP classes. |
| **Return Value** | Returns instances of PHP `stdClass` objects. | Returns instances of Model classes (with active properties/methods). |
| **Speed** | Highly performant. Minimal memory overhead. | Slightly slower due to class instantiation and processing. |
| **Relationships** | Requires manual writing of SQL `JOIN` statements. | Managed via relation methods (`hasMany`, `belongsTo`). |
| **Security** | Uses PDO parameters binding to protect against SQL injections automatically. | Uses PDO parameter binding and provides validation security out of the box. |

### CRUD Syntax comparison

#### Create (Insert)
*   **Query Builder**:
    ```php
    DB::table('posts')->insert([
        'title' => 'Laravel News',
        'body' => 'Query builder insert.',
        'user_id' => 1,
        'created_at' => now(),
        'updated_at' => now()
    ]);
    ```
*   **Eloquent ORM**:
    ```php
    Post::create([
        'title' => 'Laravel News',
        'body' => 'Eloquent mass insertion.',
        'user_id' => 1
    ]);
    ```

#### Read (Select)
*   **Query Builder**:
    ```php
    $posts = DB::table('posts')->where('user_id', 1)->get();
    ```
*   **Eloquent ORM**:
    ```php
    $posts = Post::where('user_id', 1)->get();
    ```

#### Update
*   **Query Builder**:
    ```php
    DB::table('posts')->where('id', 5)->update(['title' => 'New Title']);
    ```
*   **Eloquent ORM**:
    ```php
    $post = Post::findOrFail(5);
    $post->update(['title' => 'New Title']);
    ```

#### Delete
*   **Query Builder**:
    ```php
    DB::table('posts')->where('id', 5)->delete();
    ```
*   **Eloquent ORM**:
    ```php
    Post::destroy(5);
    ```

---

## Database Seeding & Model Factories

To seed initial or dummy mock data for testing, Laravel provides seeders and factories.

### 1. Generating a Seeder
```bash
php artisan make:seeder UserSeeder
```
This generates `database/seeders/UserSeeder.php`. Implement it like this:
```php
public function run(): void
{
    DB::table('users')->insert([
        'name' => 'Admin User',
        'email' => 'admin@lpu.in',
        'password' => bcrypt('password123'),
    ]);
}
```

### 2. Model Factories & Faker Integration
Factories provide a schema for creating mock model instances with realistic random data generated by the **Faker** library.
Generate a factory:
```bash
php artisan make:factory PostFactory --model=Post
```
Implement inside `database/factories/PostFactory.php`:
```php
public function definition(): array
{
    return [
        'title' => $this->faker->sentence(),
        'body' => $this->faker->paragraph(),
        'is_published' => $this->faker->boolean(),
        'user_id' => \App\Models\User::factory(), // Creates a user for each post
    ];
}
```

### 3. Executing Seeds
In `database/seeders/DatabaseSeeder.php`, call your seeders and factories:
```php
public function run(): void
{
    // Create 10 users, each with related mock posts
    \App\Models\User::factory(10)->create()->each(function ($user) {
        \App\Models\Post::factory(5)->create(['user_id' => $user->id]);
    });
}
```
Run the seeding command:
```bash
php artisan db:seed
# Or refresh database and seed in one step
php artisan migrate:fresh --seed
```

---

## Eloquent Relationships

Eloquent relationship methods are defined as functions inside your model classes.

### 1. One to One (`hasOne` & `belongsTo`)
*   *Scenario*: A User has one Profile.
*   **User Model**:
    ```php
    public function profile()
    {
        return $this->hasOne(Profile::class);
    }
    ```
*   **Profile Model**:
    ```php
    public function user()
    {
        return $this->belongsTo(User::class);
    }
    ```

### 2. One to Many (`hasMany` & `belongsTo`)
*   *Scenario*: A User has many Posts.
*   **User Model**:
    ```php
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
    ```
*   **Post Model**:
    ```php
    public function user()
    {
        return $this->belongsTo(User::class);
    }
    ```

### 3. Many to Many (`belongsToMany`)
*   *Scenario*: A Post belongs to many Tags (uses a pivot table `post_tag`).
*   **Post Model**:
    ```php
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
    ```
*   **Tag Model**:
    ```php
    public function posts()
    {
        return $this->belongsToMany(Post::class);
    }
    ```

---

## Using MongoDB with Laravel

MongoDB is a NoSQL database that structures data in flexible BSON documents. To use it in Laravel, you can use the official `mongodb/laravel-mongodb` integration package.

### MongoDB Configuration
1. Install package via Composer:
   ```bash
   composer require mongodb/laravel-mongodb
   ```
2. In `config/database.php`, register the mongodb driver:
   ```php
   'connections' => [
       'mongodb' => [
           'driver' => 'mongodb',
           'dsn' => env('MONGODB_URI', 'mongodb://127.0.0.1:27017'),
           'database' => env('MONGODB_DATABASE', 'laravel_db'),
       ],
   ]
   ```
3. Update `.env` values:
   ```env
   DB_CONNECTION=mongodb
   MONGODB_URI=mongodb://127.0.0.1:27017
   MONGODB_DATABASE=laravel_db
   ```

### MongoDB Eloquent Model
Instead of extending standard SQL model, extend the MongoDB model class:
```php
<?php

namespace App\Models;

use MongoDB\Laravel\Eloquent\Model; // Extends Mongo Model class

class MongoProduct extends Model
{
    protected $connection = 'mongodb'; // Use MongoDB connection
    protected $collection = 'products'; // Mongo collection name
    protected $fillable = ['name', 'details', 'price', 'metadata'];
}
```

---

## Implementing REST APIs

REST APIs interact by exchanging JSON files statelessly using standard HTTP methods.

### 1. Generating an API Controller
An API controller skips HTML form-rendering methods (`create` and `edit` views) to return JSON data exclusively.
```bash
php artisan make:controller Api/PostController --api
```

### 2. API Routes Structure
In Laravel 11, API routing is activated by installing the API structure:
```bash
php artisan install:api
```
This generates `routes/api.php`. Routes declared here are prefixed automatically with `/api`.
Register route:
```php
use App\Http\Controllers\Api\PostController;

Route::apiResource('posts', PostController::class);
```

### 3. JSON CRUD Action Implementation
Here is how your API controller actions look:

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Post;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;

class PostController extends Controller
{
    /**
     * Display a listing of posts.
     */
    public function index(): JsonResponse
    {
        $posts = Post::all();
        return response()->json([
            'success' => true,
            'data' => $posts
        ], 200);
    }

    /**
     * Store a newly created post.
     */
    public function store(Request $request): JsonResponse
    {
        $validated = $request->validate([
            'title' => 'required|string|max:150',
            'body' => 'required|string',
            'user_id' => 'required|exists:users,id',
        ]);

        $post = Post::create($validated);

        return response()->json([
            'success' => true,
            'message' => 'Post created successfully!',
            'data' => $post
        ], 201); // 201 Created
    }

    /**
     * Display the specified post.
     */
    public function show(string $id): JsonResponse
    {
        $post = Post::find($id);

        if (!$post) {
            return response()->json([
                'success' => false,
                'message' => 'Post not found.'
            ], 404); // 404 Not Found
        }

        return response()->json([
            'success' => true,
            'data' => $post
        ], 200);
    }

    /**
     * Update the specified post.
     */
    public function update(Request $request, string $id): JsonResponse
    {
        $post = Post::find($id);

        if (!$post) {
            return response()->json([
                'success' => false,
                'message' => 'Post not found.'
            ], 404);
        }

        $post->update($request->all());

        return response()->json([
            'success' => true,
            'message' => 'Post updated successfully.',
            'data' => $post
        ], 200);
    }

    /**
     * Remove the specified post.
     */
    public function destroy(string $id): JsonResponse
    {
        $deleted = Post::destroy($id);

        if (!$deleted) {
            return response()->json([
                'success' => false,
                'message' => 'Post not found.'
            ], 404);
        }

        return response()->json([
            'success' => true,
            'message' => 'Post deleted successfully.'
        ], 200);
    }
}
```
