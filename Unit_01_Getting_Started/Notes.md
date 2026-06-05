# Unit 1: Getting Started with MVC & Laravel Framework

## 📚 Course Outcomes Coverage
*   **CO1**: Recall features of Laravel and the installation process.

---

## What is MVC Framework?

**MVC (Model-View-Controller)** is a software architectural pattern that separates an application's logic into three interconnected components. This separation decoupling ensures the separation of concerns, making code easier to test, maintain, and scale.

```
       ┌────────────────────────┐
       │   User Request (URL)   │
       └───────────┬────────────┘
                   │
                   ▼
       ┌────────────────────────┐
       │       Controller       │
       └─────┬────────────▲─────┘
             │            │
   1. Query  │            │ 2. Return
   Database  │            │ Data Object
             ▼            │
       ┌─────┴────────────┴─────┐
       │         Model          │
       └────────────────────────┘
             │
   3. Pass   │
   Data To   │
             ▼
       ┌────────────────────────┐
       │          View          │
       └───────────┬────────────┘
                   │
                   ▼
       ┌────────────────────────┐
       │   HTML Response sent   │
       └────────────────────────┘
```

### 1. Model (Data Layer)
*   **Role**: Encapsulates the application's data structure, business logic, and validation rules. It interacts directly with the database.
*   **Laravel Implementation**: Eloquent ORM. Each Model class represents a database table (e.g., `User` model represents the `users` table).

### 2. View (Presentation Layer)
*   **Role**: Renders the user interface. It displays data provided by the Controller and collects user inputs.
*   **Laravel Implementation**: Blade templates (e.g., `welcome.blade.php`), which compile down into raw optimized PHP cache files.

### 3. Controller (Traffic Controller)
*   **Role**: Receives client HTTP requests, validates payload parameters, queries models for database records, and passes findings to views for rendering.
*   **Laravel Implementation**: Controller classes stored in `app/Http/Controllers/`.

---

## Overview of Laravel Framework & Features

Laravel is a free, open-source PHP web framework created by **Taylor Otwell** in 2011. It abstracts common backend workflows, offering standard tooling for authentication, routing, sessions, database connections, and queue management.

### Key Enterprise Features

1.  **Eloquent ORM**: An Active Record implementation that treats tables as classes and database rows as objects.
2.  **Blade Templating Engine**: A lightweight engine that allows developers to inherit layouts, create reusable components, and write clean conditional statements without mixing heavy raw PHP in views.
3.  **Artisan CLI**: Command-line interface that automates migrations, database seeding, code generation, and configuration caching.
4.  **Database Migrations**: Version control system for schemas, allowing team members to define tables in code and deploy structural updates.
5.  **Middleware Pipeline**: Interceptors that inspect and filter incoming HTTP requests before they reach controllers (e.g., verifying user authentication or CSRF tokens).

---

## Introduction to Composer

Composer is the **de facto dependency manager for PHP**. It manages external packages, libraries, and frameworks that your Laravel application relies on.

### Package Manifest Files
*   **`composer.json`**: The package configuration manifest. It lists direct dependencies, dev dependencies, scripts, and PHP autoload configurations.
*   **`composer.lock`**: Stores the exact version of every dependency installed. This ensures that any team member running `composer install` installs the identical dependencies. **Always commit this file to version control**.

### PSR-4 Autoloading Rules
Laravel uses the standard PSR-4 autoloading convention. Class namespaces map directly to the folder structure:
```json
"autoload": {
    "psr-4": {
        "App\\": "app/",
        "Database\\Factories\\": "database/factories/",
        "Database\\Seeders\\": "database/seeders/"
    }
}
```
If you add a class in `app/Services/PaymentService.php`, its fully qualified class name is `App\Services\PaymentService`, and Composer loads it automatically.

---

## Installation & Setup

### Prerequisites
1.  **PHP**: Ensure PHP >= 8.1 is installed on your system.
2.  **Composer**: Verify composer is installed: `composer --version`.

### Step-by-Step Installation

```bash
# 1. Create a new project via Composer's project generator
composer create-project laravel/laravel my-laravel-app

# 2. Enter directory
cd my-laravel-app

# 3. Copy environment configuration
copy .env.example .env

# 4. Generate unique application encryption key (used for session and cookie encryption)
php artisan key:generate

# 5. Start the PHP local development server
php artisan serve
```

---

## Laravel Directory Structure

A clean overview of the purpose of key Laravel folders:

| Folder Path | Primary Responsibility |
|-------------|------------------------|
| `/app` | Core backend logic. Contains Controllers, Models, and Middleware. |
| `/config` | Configuration files (database drivers, mail settings, auth drivers). |
| `/database` | Database migrations, seeders, and factories. |
| `/public` | Entry point (`index.php`) and static assets (CSS, JS, images). |
| `/resources` | Raw assets (views, language translations, sass/js source code). |
| `/routes` | Routing definitions (`web.php` for HTTP, `api.php` for API endpoints). |
| `/storage` | Cache files, sessions, framework logs, and file uploads. |
| `/vendor` | Contains Composer packages and dependencies (never edit this folder). |

---

## Artisan CLI

Artisan is Laravel's **Command Line Interface**. It exposes helper commands that speed up development by generating boilerplate files, clearing caches, and executing database updates.

### Artisan Commands Visualized
```
                           ┌──────────────────────────┐
                           │    php artisan [cmd]     │
                           └────────────┬─────────────┘
                                        │
      ┌───────────────────┬─────────────┼───────────────┬───────────────────┐
      ▼                   ▼             ▼               ▼                   ▼
make:controller       make:model     migrate         db:seed           optimize:clear
(Generates controllers)(Gen Model) (Runs migration)(Seeds databases) (Resets all caches)
```

### Essential Artisan Command List

*   `php artisan serve` - Starts development server.
*   `php artisan make:controller ControllerName` - Generates a controller.
*   `php artisan make:model ModelName -m` - Generates a model and its migration.
*   `php artisan migrate` - Runs all pending database migrations.
*   `php artisan db:seed` - Seeds the database with test data.
*   `php artisan route:list` - Lists all registered application routes.
*   `php artisan optimize:clear` - Clears config, route, view, cache, and logs.
