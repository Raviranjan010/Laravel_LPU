# Unit 6 Practical Lab: Database Migrations, Eloquent & REST APIs

This laboratory guide walks you through configuring database connections, writing migrations, generating seeds/factories, implementing Eloquent relationships, and creating a REST API JSON endpoint.

---

## Exercise 1: Database Setup and Migrations

We will configure Laravel to use a local database and create a version-controlled database schema.

### Step 1: Database Configuration
Open the `.env` file at your project root and update the database settings. For exam environments, you can use **SQLite** (which requires zero database software installation) or **MySQL**:

```env
# Example using MySQL:
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_lpu_db
DB_USERNAME=root
DB_PASSWORD=
```

*(If using SQLite, simply set `DB_CONNECTION=sqlite` and delete other lines. Laravel will automatically build a database file inside `database/database.sqlite`).*

### Step 2: Generate Model and Migration
Run the Artisan command to create a `Product` model along with its migration:
```bash
php artisan make:model Product -m
```

### Step 3: Implement the Migration Schema
Open the newly generated migration file in `database/migrations/xxxx_xx_xx_xxxxxx_create_products_table.php` and define the table schema:

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
        Schema::create('products', function (Blueprint $table) {
            $table->id(); // Primary Key
            $table->string('name', 100)->unique();
            $table->text('description')->nullable();
            $table->decimal('price', 8, 2); // 8 total digits, 2 decimal places (e.g. 999999.99)
            $table->integer('stock')->default(0);
            
            // Foreign key referencing users table
            $table->foreignId('user_id')->constrained()->onDelete('cascade');
            
            $table->timestamps(); // created_at, updated_at
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('products');
    }
};
```

### Step 4: Run the Migration
Apply the migrations to create tables inside the database:
```bash
php artisan migrate
```
To verify the rollback functionality works properly, run:
```bash
php artisan migrate:rollback
```
Then migrate again:
```bash
php artisan migrate
```

---

## Exercise 2: Populating the Database with Seeding and Factories

We will create mock data templates to seed 20 random products into the database.

### Step 1: Generate a Model Factory
Create the factory class:
```bash
php artisan make:factory ProductFactory --model=Product
```

### Step 2: Write Factory Schema Definition
Open `database/factories/ProductFactory.php` and configure Faker fields:

```php
<?php

namespace Database\Factories;

use App\Models\Product;
use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class ProductFactory extends Factory
{
    protected $model = Product::class;

    public function definition(): array
    {
        return [
            'name' => $this->faker->unique()->words(3, true),
            'description' => $this->faker->sentence(10),
            'price' => $this->faker->randomFloat(2, 10, 500), // Prices between $10 and $500
            'stock' => $this->faker->numberBetween(0, 100),
            'user_id' => User::factory(), // Automatically generates a user record
        ];
    }
}
```

### Step 3: Run Database Seeds
Open `database/seeders/DatabaseSeeder.php` and call the factory:

```php
public function run(): void
{
    // Generate 5 users, each with 3 products
    \App\Models\User::factory(5)->create()->each(function ($user) {
        \App\Models\Product::factory(3)->create([
            'user_id' => $user->id
        ]);
    });
}
```

Execute the database seed command:
```bash
php artisan db:seed
# Or fresh-drop tables and seed together:
php artisan migrate:fresh --seed
```

---

## Exercise 3: Defining Eloquent Relationships

We will declare the One-to-Many relationship between User and Product.

### Step 1: Update the User Model
Open `app/Models/User.php` and add the relationship:

```php
use App\Models\Product;
use Illuminate\Database\Eloquent\Relations\HasMany;

public function products(): HasMany
{
    return $this->hasMany(Product::class);
}
```

### Step 2: Update the Product Model
Open `app/Models/Product.php` and add the inverse relationship and fillable whitelist:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Product extends Model
{
    use HasFactory;

    protected $fillable = ['name', 'description', 'price', 'stock', 'user_id'];

    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}
```

---

## Exercise 4: Building a RESTful API Controller

We will create JSON CRUD endpoints for products.

### Step 1: Install API Structure (if Laravel 11)
```bash
php artisan install:api
```
This generates `routes/api.php` and registers the api routing system.

### Step 2: Create API Controller
```bash
php artisan make:controller Api/ProductController --api
```

### Step 3: Write API Actions
Open `app/Http/Controllers/Api/ProductController.php` and implement the actions:

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Product;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;

class ProductController extends Controller
{
    public function index(): JsonResponse
    {
        $products = Product::with('user')->get(); // Eager load user relationship
        
        return response()->json([
            'status' => 'success',
            'count' => $products->count(),
            'data' => $products
        ], 200);
    }

    public function store(Request $request): JsonResponse
    {
        $validated = $request->validate([
            'name' => 'required|string|max:100|unique:products,name',
            'description' => 'nullable|string',
            'price' => 'required|numeric|min:0.01',
            'stock' => 'required|integer|min:0',
            'user_id' => 'required|exists:users,id'
        ]);

        $product = Product::create($validated);

        return response()->json([
            'status' => 'success',
            'message' => 'Product successfully created!',
            'data' => $product
        ], 201); // 201 Created
    }

    public function show($id): JsonResponse
    {
        $product = Product::with('user')->find($id);

        if (!$product) {
            return response()->json([
                'status' => 'error',
                'message' => 'Product with ID ' . $id . ' does not exist.'
            ], 404);
        }

        return response()->json([
            'status' => 'success',
            'data' => $product
        ], 200);
    }

    public function update(Request $request, $id): JsonResponse
    {
        $product = Product::find($id);

        if (!$product) {
            return response()->json([
                'status' => 'error',
                'message' => 'Product not found.'
            ], 404);
        }

        $validated = $request->validate([
            'name' => 'sometimes|required|string|max:100|unique:products,name,' . $id,
            'price' => 'sometimes|required|numeric|min:0.01',
            'stock' => 'sometimes|required|integer|min:0',
        ]);

        $product->update($validated);

        return response()->json([
            'status' => 'success',
            'message' => 'Product details updated.',
            'data' => $product
        ], 200);
    }

    public function destroy($id): JsonResponse
    {
        $deleted = Product::destroy($id);

        if (!$deleted) {
            return response()->json([
                'status' => 'error',
                'message' => 'Failed to delete. Product not found.'
            ], 404);
        }

        return response()->json([
            'status' => 'success',
            'message' => 'Product was successfully removed from database.'
        ], 200);
    }
}
```

### Step 4: Register API Route Resource
In `routes/api.php`, register the controller:

```php
use App\Http\Controllers\Api\ProductController;

Route::apiResource('products', ProductController::class);
```

---

## 🧪 Testing the API
To test your newly created endpoints, you can use tools like **Postman**, **Insomnia**, or simple command line **cURL** utilities:

### 1. Retrieve all products (GET)
```bash
curl -X GET http://127.0.0.1:8000/api/products
```

### 2. Save a new product (POST)
```bash
curl -X POST http://127.0.0.1:8000/api/products \
     -H "Content-Type: application/json" \
     -d '{"name": "Gaming Mouse", "description": "RGB ergonomic mouse", "price": 49.99, "stock": 10, "user_id": 1}'
```

### 3. Verify validation error (POSTing invalid payload)
Submit empty payload and notice the returned `422 Unprocessable Content` status:
```bash
curl -X POST http://127.0.0.1:8000/api/products \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     -d '{}'
```
