# Unit 6 Viva Questions & Answers

### Q1: What is the main design pattern that Eloquent ORM is built on? Explain its core concept.
**Answer:**
Eloquent ORM is built on the **Active Record pattern**. Under this pattern, a database table corresponds directly to a PHP class (Model), and a single row in that table corresponds directly to an instance (object) of that model class. Property accessors represent column values, and instance methods handle save, update, or delete operations.

### Q2: What is the difference between `$fillable` and `$guarded` properties in a model? Can you use both together?
**Answer:**
*   `$fillable` is an array of attributes that **are** allowed to be mass-assigned (whitelist).
*   `$guarded` is an array of attributes that **are not** allowed to be mass-assigned (blacklist).
No, you should never use both in the same model class. If you want to allow all columns to be mass-assigned, set `$guarded = [];`.

### Q3: What is a database migration, and why is it referred to as "version control" for databases?
**Answer:**
A migration is a PHP script containing schema instructions (tables creation, column adjustments) that are recorded sequentially in a database control table. It acts as version control because it keeps track of which migrations have been run, allows team members to replicate identical schema states, and lets developers roll back or apply incremental changes easily.

### Q4: Explain the differences between the Artisan commands `migrate:fresh` and `migrate:refresh`.
**Answer:**
*   `php artisan migrate:fresh`: Drops **all tables** in the database directly (bypassing the `down()` methods of migrations) and runs all migrations from the beginning.
*   `php artisan migrate:refresh`: Step-by-step executes the `down()` method of all registered migrations to clear tables and then re-runs them.

### Q5: How do you check which migrations have successfully run and which ones are still pending?
**Answer:**
Run the Artisan command:
```bash
php artisan migrate:status
```
It returns a table containing the name, status (Ran or Pending), and batch number of each migration.

### Q6: What is a foreign key constraint, and how do you write it in a migration blueprint?
**Answer:**
A foreign key constraint ensures referential integrity between tables. In Laravel, you can define it using the `foreignId()` helper:
```php
$table->foreignId('user_id')->constrained('users')->onDelete('cascade');
```
This automatically creates a `user_id` column of the correct integer type, references the `id` column of the `users` table, and automatically deletes the row if the referenced user is deleted.

### Q7: What are Seeders and Factories? How do they work together?
**Answer:**
*   **Seeders** are classes used to populate database tables with initial values or test data (e.g. creating default admin accounts or dummy posts).
*   **Factories** are templates used to generate realistic mock records for models in large quantities using the Faker library.
They work together when a Seeder calls a Factory: e.g. `User::factory(50)->create();` creates 50 random user records using the configurations defined in the User Factory.

### Q8: What is Faker, and where is it configured?
**Answer:**
Faker is a PHP library built into Laravel that generates realistic dummy data such as random names, addresses, phone numbers, text, emails, and credit cards. It is configured and instantiated automatically in Laravel Model Factories via the `$this->faker` property.

### Q9: How do you configure a MongoDB connection in Laravel?
**Answer:**
1. Install the official MongoDB driver via Composer: `composer require mongodb/laravel-mongodb`.
2. Register the `mongodb` connection block in `config/database.php`.
3. Set `DB_CONNECTION=mongodb` and provide the `MONGODB_URI` connection string inside your `.env` configuration file.

### Q10: What class must a MongoDB model extend instead of the default SQL model?
**Answer:**
It must extend `MongoDB\Laravel\Eloquent\Model` instead of `Illuminate\Database\Eloquent\Model`.

### Q11: Explain the three main Eloquent relationship types and the corresponding methods used to define them.
**Answer:**
1.  **One-to-One**: Defined using `hasOne()` in the primary model and `belongsTo()` in the related model.
2.  **One-to-Many**: Defined using `hasMany()` in the primary model and `belongsTo()` in the related model.
3.  **Many-to-Many**: Defined using `belongsToMany()` in both models, which maps records using an intermediate join table (pivot table).

### Q12: What is the N+1 Query Problem in Eloquent ORM, and how do you resolve it?
**Answer:**
The N+1 Query Problem occurs when Eloquent lazily loads relationship records inside a loop. For example, if you fetch 100 books and loop through them to display their author's name (`$book->author->name`), Eloquent runs 1 query to fetch the books, and 100 separate queries to fetch the author of each book (101 queries total).
*   **Resolution**: Use **eager loading** via the `with()` method:
    ```php
    $books = Book::with('author')->get(); // Runs only 2 queries total
    ```

### Q13: In which file do you define REST API routes in Laravel, and what is their default URL prefix?
**Answer:**
API routes are defined inside the `routes/api.php` file (scaffolded via `php artisan install:api` in Laravel 11). They are automatically prefixed with `/api` (e.g., `http://127.0.0.1:8000/api/books`).

### Q14: What is the purpose of `Route::apiResource('products', ProductController::class)`?
**Answer:**
It registers a single route declaration that maps all standard RESTful endpoints for an API resource. It automatically generates 5 routes:
*   `GET /api/products` (index)
*   `POST /api/products` (store)
*   `GET /api/products/{product}` (show)
*   `PUT/PATCH /api/products/{product}` (update)
*   `DELETE /api/products/{product}` (destroy)
*(Note: It excludes the `create` and `edit` routes, which are only needed for HTML forms).*

### Q15: Match the following API scenarios to their proper HTTP Status Codes:
1. Data successfully loaded: **200 OK**
2. Validation rules failed: **422 Unprocessable Content**
3. New record successfully created: **201 Created**
4. Request authenticated user lacks permissions: **403 Forbidden**
5. Record or endpoint not found: **404 Not Found**
6. Database or server code crashed: **500 Internal Server Error**
