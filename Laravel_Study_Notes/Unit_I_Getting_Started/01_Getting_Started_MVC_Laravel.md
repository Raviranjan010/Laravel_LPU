# Unit I: Getting Started with MVC Laravel Framework

## 📚 Table of Contents
- [What is MVC Framework?](#what-is-mvc-framework)
- [Overview of Laravel Framework](#overview-of-laravel-framework)
- [Introduction to Composer](#introduction-to-composer)
- [Installation Guides](#installation-guides)
- [Directory/Application Structure](#directoryapplication-structure)
- [Artisan CLI](#artisan-cli)
- [Exam Questions & Answers](#exam-questions--answers)

---

## What is MVC Framework?

### Definition
**MVC (Model-View-Controller)** is an architectural design pattern that separates an application into three interconnected components:

### Components Explained

#### 1. **Model** 🗄️
- Represents the data and business logic
- Handles database interactions
- Manages data validation and rules
- **Example**: User model handles user data, authentication logic

#### 2. **View** 👁️
- Represents the presentation layer (UI)
- Displays data to users
- Contains HTML, CSS, and minimal PHP
- **Example**: Blade templates showing user profiles

#### 3. **Controller** 🎮
- Acts as an intermediary between Model and View
- Handles user requests
- Processes input and returns responses
- **Example**: UserController manages user-related operations

### MVC Flow Diagram
```
User Request → Controller → Model (Database)
                  ↓
              View (Response)
```

### Benefits of MVC
✅ **Separation of Concerns** - Each component has a specific role  
✅ **Code Reusability** - Models and views can be reused  
✅ **Maintainability** - Easy to update individual components  
✅ **Parallel Development** - Multiple developers can work simultaneously  
✅ **Testability** - Components can be tested independently  

---

## Overview of Laravel Framework

### What is Laravel?
Laravel is a **free, open-source PHP web framework** created by Taylor Otwell in 2011. It follows the MVC architectural pattern and is designed for building modern, robust web applications.

### Key Features of Laravel

#### 1. **Elegant Syntax**
- Clean, expressive code
- Developer-friendly conventions
- Reduces boilerplate code

#### 2. **Eloquent ORM**
- Active Record implementation
- Simple database interactions
- Relationship management

```php
// Example: Fetching users with posts
$users = User::with('posts')->get();
```

#### 3. **Blade Templating Engine**
- Lightweight templating
- Template inheritance
- Control structures

#### 4. **Artisan CLI**
- Command-line interface
- Code generation
- Database migrations
- Task scheduling

#### 5. **Routing System**
- Expressive routing API
- Route parameters
- Middleware support

#### 6. **Security Features**
- CSRF protection
- Password hashing
- SQL injection prevention
- XSS protection

#### 7. **Authentication & Authorization**
- Built-in authentication scaffolding
- Role-based access control
- API authentication (Sanctum, Passport)

#### 8. **Database Migrations**
- Version control for database schema
- Team collaboration friendly
- Rollback capabilities

#### 9. **Queue System**
- Background job processing
- Multiple queue drivers
- Failed job handling

#### 10. **Event Broadcasting**
- Real-time event system
- WebSocket integration
- Push notifications

#### 11. **Testing Support**
- PHPUnit integration
- Browser testing (Dusk)
- Mocking facilities

#### 12. **Package Ecosystem**
- Composer integration
- Thousands of packages
- Easy package development

### Laravel Advantages
🚀 **Rapid Development** - Pre-built features speed up development  
🔒 **Secure by Default** - Built-in security measures  
📦 **Modular** - Package-based architecture  
🌐 **Large Community** - Extensive documentation and support  
💼 **Job Market** - High demand in industry  

---

## Introduction to Composer

### What is Composer?
Composer is a **dependency manager for PHP**. It allows you to declare and manage libraries your project depends on.

### Key Concepts

#### 1. **Dependency Management**
- Automatically downloads required packages
- Resolves version conflicts
- Updates dependencies easily

#### 2. **Autoloading**
- PSR-4 autoloading standard
- No manual `require` statements
- Efficient class loading

#### 3. **composer.json**
Main configuration file that defines:
- Project dependencies
- Autoload rules
- Scripts
- Minimum stability

```json
{
    "name": "laravel/laravel",
    "type": "project",
    "require": {
        "php": "^8.1",
        "laravel/framework": "^10.0"
    },
    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        }
    }
}
```

#### 4. **composer.lock**
- Locks exact versions of dependencies
- Ensures consistency across environments
- Should be committed to version control

### Important Composer Commands

```bash
# Install dependencies
composer install

# Update dependencies
composer update

# Add new package
composer require package/name

# Remove package
composer remove package/name

# Dump autoload files
composer dump-autoload

# Check for outdated packages
composer outdated

# Validate composer.json
composer validate
```

---

## Installation Guides

### Prerequisites
Before installing Laravel, ensure you have:
- ✅ PHP >= 8.1
- ✅ Composer (latest version)
- ✅ Web Server (Apache/Nginx)
- ✅ Database (MySQL/PostgreSQL/SQLite)
- ✅ Node.js & NPM (for frontend assets)

### Step 1: Install Composer

#### Windows Installation
1. Download installer from [getcomposer.org](https://getcomposer.org/download/)
2. Run the `.exe` installer
3. Follow the wizard steps
4. Verify installation:

```bash
composer --version
```

#### Linux/Mac Installation
```bash
# Download installer
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

# Verify installer
php -r "if (hash_file('sha384', 'composer-setup.php') === 'e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64df90f4d209da1') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

# Install globally
php composer-setup.php --install-dir=/usr/local/bin --filename=composer

# Remove installer
php -r "unlink('composer-setup.php');"

# Verify
composer --version
```

### Step 2: Install Laravel

#### Method 1: Via Composer Create-Project (Recommended)
```bash
# Create new Laravel project
composer create-project laravel/laravel project-name

# Specify version
composer create-project laravel/laravel:^10.0 project-name

# Enter project directory
cd project-name
```

#### Method 2: Via Laravel Installer
```bash
# Install Laravel installer globally
composer global require laravel/installer

# Ensure ~/.composer/vendor/bin is in PATH

# Create new project
laravel new project-name

# Quick setup with options
laravel new project-name --jet
laravel new project-name --breeze
```

#### Method 3: Via Git Clone
```bash
# Clone Laravel repository
git clone https://github.com/laravel/laravel.git project-name

# Enter directory
cd project-name

# Install dependencies
composer install

# Copy environment file
cp .env.example .env

# Generate application key
php artisan key:generate
```

### Step 3: Post-Installation Setup

```bash
# 1. Navigate to project
cd project-name

# 2. Copy environment file
cp .env.example .env

# 3. Generate application key
php artisan key:generate

# 4. Configure database in .env file
# DB_CONNECTION=mysql
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=your_database
# DB_USERNAME=your_username
# DB_PASSWORD=your_password

# 5. Run migrations
php artisan migrate

# 6. Start development server
php artisan serve
```

### Verify Installation
Visit `http://localhost:8000` in your browser. You should see the Laravel welcome page.

---

## Directory/Application Structure

### Laravel 10.x Directory Structure

```
project-name/
├── app/                    # Application core code
│   ├── Console/           # Artisan commands
│   │   └── Kernel.php     # Command scheduler
│   ├── Exceptions/        # Exception handler
│   │   └── Handler.php
│   ├── Http/             # HTTP layer
│   │   ├── Controllers/  # Application controllers
│   │   ├── Middleware/   # HTTP middleware
│   │   └── Kernel.php    # HTTP kernel
│   ├── Models/           # Eloquent models
│   ├── Providers/        # Service providers
│   │   ├── AppServiceProvider.php
│   │   ├── AuthServiceProvider.php
│   │   ├── EventServiceProvider.php
│   │   └── RouteServiceProvider.php
│   └── ...
├── bootstrap/            # Framework bootstrap files
│   ├── cache/           # Cached files
│   └── app.php          # Bootstrap script
├── config/               # Configuration files
│   ├── app.php          # Application config
│   ├── auth.php         # Authentication config
│   ├── database.php     # Database config
│   ├── mail.php         # Mail config
│   ├── queue.php        # Queue config
│   └── ...
├── database/             # Database files
│   ├── factories/       # Model factories
│   ├── migrations/      # Database migrations
│   └── seeders/         # Database seeders
├── public/               # Publicly accessible files
│   ├── index.php        # Entry point
│   ├── .htaccess        # Apache config
│   ├── css/            # Compiled CSS
│   ├── js/             # Compiled JS
│   └── images/         # Images
├── resources/            # Raw assets
│   ├── views/          # Blade templates
│   ├── lang/           # Language files
│   └── sass/           # Sass files
├── routes/               # Route definitions
│   ├── web.php         # Web routes
│   ├── api.php         # API routes
│   ├── console.php     # Console routes
│   └── channels.php    # Broadcast channels
├── storage/              # Storage files
│   ├── app/            # Application storage
│   ├── framework/      # Framework storage
│   │   ├── cache/
│   │   ├── sessions/
│   │   └── views/
│   └── logs/           # Log files
├── tests/                # Test files
│   ├── Feature/        # Feature tests
│   └── Unit/           # Unit tests
├── vendor/               # Composer dependencies
├── .env                  # Environment variables
├── .env.example          # Environment example
├── artisan               # Artisan CLI
├── composer.json         # Composer config
├── composer.lock         # Locked dependencies
├── package.json          # NPM config
├── phpunit.xml           # PHPUnit config
└── README.md             # Project readme
```

### Key Directories Explained

#### `/app` Directory
Contains the core application code following MVC pattern.

**Important Subdirectories:**
- **Controllers/**: Handle incoming requests
- **Models/**: Represent database tables
- **Middleware/**: Filter HTTP requests
- **Providers/**: Bootstrap application services

#### `/config` Directory
All configuration files. Values can be accessed using:
```php
$value = config('app.name');
```

#### `/database` Directory
Database-related files:
- **migrations/**: Schema definitions
- **seeders/**: Sample data
- **factories/**: Model factories for testing

#### `/public` Directory
Only publicly accessible directory. Contains:
- `index.php`: Application entry point
- Assets (CSS, JS, images)

#### `/resources` Directory
Raw, uncompiled assets:
- **views/**: Blade templates
- **lang/**: Localization files

#### `/routes` Directory
All route definitions:
- **web.php**: Web interface routes
- **api.php**: API routes
- **console.php**: Artisan commands

#### `/storage` Directory
Generated files:
- Logs
- Cache
- Sessions
- Compiled views
- File uploads

#### `/vendor` Directory
Composer dependencies (DO NOT MODIFY)

---

## Artisan CLI

### What is Artisan?
Artisan is Laravel's **command-line interface** that provides helpful commands for development.

### Basic Artisan Commands

#### Application Information
```bash
# Display Laravel version
php artisan --version

# List all available commands
php artisan list

# Get help for a command
php artisan help make:controller
```

#### Server Commands
```bash
# Start development server
php artisan serve

# Specify host and port
php artisan serve --host=0.0.0.0 --port=8080

# Clear application cache
php artisan optimize:clear
```

#### Cache Management
```bash
# Clear all caches
php artisan optimize:clear

# Clear application cache
php artisan cache:clear

# Clear config cache
php artisan config:clear

# Clear route cache
php artisan route:clear

# Clear view cache
php artisan view:clear

# Cache configurations (production)
php artisan config:cache

# Cache routes (production)
php artisan route:cache

# Cache views (production)
php artisan view:cache
```

#### Database Commands
```bash
# Run all migrations
php artisan migrate

# Create migration file
php artisan make:migration create_users_table

# Rollback last migration
php artisan migrate:rollback

# Reset all migrations
php artisan migrate:reset

# Refresh migrations (rollback + migrate)
php artisan migrate:refresh

# Seed database
php artisan db:seed

# Create seeder
php artisan make:seeder UsersTableSeeder

# Create factory
php artisan make:factory UserFactory
```

#### Code Generation (Make Commands)
```bash
# Create controller
php artisan make:controller UserController

# Create resource controller
php artisan make:controller UserController --resource

# Create model
php artisan make:model User

# Create model with migration
php artisan make:model User -m

# Create model with migration, factory, and seeder
php artisan make:model User -mfs

# Create middleware
php artisan make:middleware CheckAge

# Create request validation
php artisan make:request StoreUserRequest

# Create policy
php artisan make:policy UserPolicy

# Create event
php artisan make:event UserCreated

# Create listener
php artisan make:listener SendWelcomeEmail

# Create job
php artisan make:job ProcessPodcast

# Create notification
php artisan make:notification InvoicePaid

# Create test
php artisan make:test UserTest
```

#### Routing Commands
```bash
# List all routes
php artisan route:list

# List routes with specific method
php artisan route:list --method=GET

# Clear route cache
php artisan route:clear
```

#### Authentication Scaffolding
```bash
# Install Laravel Breeze (simple auth)
composer require laravel/breeze --dev
php artisan breeze:install

# Install Laravel Jetstream (advanced auth)
composer require laravel/jetstream
php artisan jetstream:install livewire
```

#### Queue Commands
```bash
# Start queue worker
php artisan queue:work

# Listen to queue
php artisan queue:listen

# Check failed jobs
php artisan queue:failed

# Retry failed jobs
php artisan queue:retry all
```

#### Maintenance Mode
```bash
# Enable maintenance mode
php artisan down

# Enable with custom message
php artisan down --message="Upgrading Database"

# Disable maintenance mode
php artisan up
```

#### Custom Commands
```bash
# Create custom command
php artisan make:command SendEmails

# Location: app/Console/Commands/SendEmails.php
```

**Custom Command Structure:**
```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;

class SendEmails extends Command
{
    protected $signature = 'emails:send {user}';
    protected $description = 'Send promotional emails to users';

    public function handle()
    {
        $userId = $this->argument('user');
        
        // Your logic here
        
        $this->info('Emails sent successfully!');
        return Command::SUCCESS;
    }
}
```

**Register in `app/Console/Kernel.php`:**
```php
protected $commands = [
    \App\Console\Commands\SendEmails::class,
];
```

**Run custom command:**
```bash
php artisan emails:send 1
```

---

## Exam Questions & Answers

### Short Answer Questions (2-5 marks)

#### Q1: What is MVC? Explain its components.
**Answer:**
MVC (Model-View-Controller) is an architectural pattern that separates application into three components:
- **Model**: Manages data and business logic
- **View**: Handles UI and presentation
- **Controller**: Processes user input and coordinates Model and View

#### Q2: List five features of Laravel framework.
**Answer:**
1. Eloquent ORM for database operations
2. Blade templating engine
3. Artisan CLI for automation
4. Built-in authentication system
5. Database migrations and seeders

#### Q3: What is Composer? Why is it used in Laravel?
**Answer:**
Composer is a PHP dependency manager. It's used in Laravel to:
- Manage third-party packages
- Handle autoloading
- Resolve version conflicts
- Simplify package updates

#### Q4: Explain the purpose of `.env` file in Laravel.
**Answer:**
The `.env` file stores environment-specific configuration:
- Database credentials
- Application URL
- Mail settings
- API keys
- Debug mode status

It keeps sensitive data out of version control.

#### Q5: What is Artisan? Give three examples of Artisan commands.
**Answer:**
Artisan is Laravel's command-line interface. Examples:
1. `php artisan serve` - Start development server
2. `php artisan migrate` - Run database migrations
3. `php artisan make:controller` - Create controller

#### Q6: What is the significance of `public/index.php` in Laravel?
**Answer:**
`public/index.php` is the entry point for all HTTP requests. It:
- Bootstraps the Laravel application
- Loads Composer autoloader
- Creates application instance
- Handles the request and sends response

#### Q7: Differentiate between `composer.json` and `composer.lock`.
**Answer:**
- **composer.json**: Defines required packages and version constraints
- **composer.lock**: Records exact installed versions for consistency

#### Q8: What are service providers in Laravel?
**Answer:**
Service providers bootstrap application services. They:
- Register bindings in service container
- Configure services
- Are listed in `config/app.php`

### Long Answer Questions (10-15 marks)

#### Q9: Explain Laravel's directory structure in detail.
**Answer:**
[See Directory Structure section above - expand each directory with examples]

#### Q10: Describe the complete process of installing Laravel on Windows.
**Answer:**
**Step 1:** Install PHP
- Download PHP from windows.php.net
- Extract and add to system PATH
- Verify: `php -v`

**Step 2:** Install Composer
- Download from getcomposer.org
- Run installer
- Verify: `composer --version`

**Step 3:** Install Laravel
```bash
composer create-project laravel/laravel myapp
cd myapp
```

**Step 4:** Configure Environment
```bash
cp .env.example .env
php artisan key:generate
```

**Step 5:** Setup Database
- Edit `.env` with database credentials
- Run: `php artisan migrate`

**Step 6:** Start Server
```bash
php artisan serve
```

#### Q11: Compare traditional PHP development with Laravel development.
**Answer:**

| Aspect | Traditional PHP | Laravel |
|--------|----------------|---------|
| Architecture | No fixed pattern | MVC pattern |
| Security | Manual implementation | Built-in protections |
| Database | Raw SQL queries | Eloquent ORM |
| Routing | Manual routing | Elegant routing system |
| Testing | Complex setup | Built-in testing |
| Development Speed | Slower | Faster |
| Learning Curve | Lower initially | Steeper but rewarding |
| Community | Fragmented | Unified & large |

### Practical Questions

#### Q12: Create a Laravel project named "BlogApp" and explain each step.
**Answer:**
```bash
# Step 1: Create project
composer create-project laravel/laravel BlogApp

# Step 2: Navigate to project
cd BlogApp

# Step 3: Copy environment file
copy .env.example .env

# Step 4: Generate key
php artisan key:generate

# Step 5: Configure database in .env

# Step 6: Run migrations
php artisan migrate

# Step 7: Start server
php artisan serve
```

#### Q13: Write Artisan commands to:
a) Create a controller named ProductController  
b) Create a model named Product with migration  
c) List all routes  
d) Clear all caches  
e) Create a middleware named AuthCheck

**Answer:**
```bash
a) php artisan make:controller ProductController
b) php artisan make:model Product -m
c) php artisan route:list
d) php artisan optimize:clear
e) php artisan make:middleware AuthCheck
```

---

## Quick Reference Commands

```bash
# Installation
composer create-project laravel/laravel project-name
cd project-name
php artisan key:generate

# Development
php artisan serve
php artisan make:controller Name
php artisan make:model Name -m
php artisan migrate

# Maintenance
php artisan down
php artisan up
php artisan optimize:clear
```

---

## Summary Checklist

After studying Unit I, you should be able to:
- ✅ Explain MVC architecture
- ✅ List Laravel features
- ✅ Install Composer and Laravel
- ✅ Understand directory structure
- ✅ Use basic Artisan commands
- ✅ Create new Laravel projects
- ✅ Configure environment variables

---

**Next:** [Unit II: Request, Routing & Responses](../Unit_II_Request_Routing_Responses/01_Request_Routing_Responses.md)
