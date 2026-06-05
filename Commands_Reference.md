# Laravel Commands Reference Guide

A consolidated cheatsheet of all essential Composer and Laravel Artisan CLI commands for the MVC Programming curriculum.

---

## 📦 Composer Package Management

Composer is PHP's package manager, responsible for handling framework dependencies and PSR-4 autoload rules.

```bash
# 1. Create a new Laravel project in a specific directory
composer create-project laravel/laravel project_name

# 2. Download and install dependencies specified in composer.json
composer install

# 3. Update all dependencies and write the exact versions to composer.lock
composer update

# 4. Require a new library (e.g. MongoDB integration)
composer require mongodb/laravel-mongodb

# 5. Require a development-only tool (e.g. IDE Helper or debug bars)
composer require barryvdh/laravel-debugbar --dev

# 6. Regenerate autoload class mappings (useful after adding classes manually)
composer dump-autoload
```

---

## ⚡ Server & System Configuration

```bash
# 1. Boot up the local PHP development server
php artisan serve

# 2. Start server on custom host IP or port (e.g. for access on networks)
php artisan serve --host=0.0.0.0 --port=8080

# 3. Generate a new cryptographically secure 32-character APP_KEY in the .env file
php artisan key:generate

# 4. List all registered application routes
php artisan route:list

# 5. List routes in compact, clean form
php artisan route:list --compact

# 6. Open interactive REPL shell (Tinker) to run PHP code and query databases
php artisan tinker

# 7. Create a symbolic link from public/storage to storage/app/public
php artisan storage:link
```

---

## 🛠️ Code Generation (Make Commands)

All generated files are written to the default PSR-4 layout directories.

```bash
# 1. Models
php artisan make:model Product                    # Basic Eloquent Model
php artisan make:model Product -m                 # Model with Database Migration
php artisan make:model Product -mfs               # Model + Migration + Factory + Seeder
php artisan make:model Product -a                 # Model + Migration + Factory + Seeder + Controller + Policy

# 2. Controllers
php artisan make:controller BookController                 # Basic Controller
php artisan make:controller BookController --resource      # RESTful CRUD Controller
php artisan make:controller Api/BookController --api       # RESTful API Controller (no create/edit views)
php artisan make:controller ShowDashboard --invokable      # Single Action Controller

# 3. Database Schemas & Seeds
php artisan make:migration create_books_table              # Create Migration File
php artisan make:seeder BookSeeder                         # Create Seeder Class
php artisan make:factory BookFactory --model=Book          # Create Factory Class for Model

# 4. Request Validation & Business Rules
php artisan make:request StoreBookRequest                  # Create custom Form Request validation class
php artisan make:rule MinWords                             # Create custom Validation Rule class

# 5. Routing Scaffolding (Laravel 11.x)
php artisan install:api                                    # Scaffolds api.php routing file
```

---

## 🗄️ Database Management

```bash
# 1. Run all pending migrations
php artisan migrate

# 2. Rollback the most recent batch of migrations
php artisan migrate:rollback

# 3. Rollback the last 2 migration files
php artisan migrate:rollback --step=2

# 4. Rollback all migrations ever run (leaves a blank schema)
php artisan migrate:reset

# 5. Rollback everything and run migrations again from start
php artisan migrate:refresh

# 6. Drop all database tables and run migrations from scratch (destroys data!)
php artisan migrate:fresh

# 7. Refresh database and run all seeders
php artisan migrate:fresh --seed

# 8. Check status (Ran or Pending) of every migration file
php artisan migrate:status

# 9. Execute database seeders
php artisan db:seed                                        # Runs DatabaseSeeder
php artisan db:seed --class=UserSeeder                     # Runs specific seeder class
```

---

## 🧹 Cache & Optimizations

Use these commands to clear stale configurations during development or cache resources for speed in production.

```bash
# 1. Clear ALL caches (application, config, routing, views)
php artisan optimize:clear

# 2. Compile and cache configurations for fast loading
php artisan config:cache
php artisan config:clear                                   # Clear config cache

# 3. Compile and cache application routes
php artisan route:cache
php artisan route:clear                                    # Clear routes cache

# 4. Compile and cache all Blade view templates
php artisan view:cache
php artisan view:clear                                     # Clear view cache
```
