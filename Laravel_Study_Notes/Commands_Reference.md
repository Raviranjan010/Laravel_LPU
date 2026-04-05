# Laravel Commands Reference Guide

## 📚 Complete Artisan & Composer Commands

Quick reference for all essential Laravel and Composer commands with explanations and examples.

---

## Table of Contents
- [Installation Commands](#installation-commands)
- [Development Server](#development-server)
- [Code Generation (Make Commands)](#code-generation-make-commands)
- [Database Commands](#database-commands)
- [Cache Management](#cache-management)
- [Routing Commands](#routing-commands)
- [Queue Commands](#queue-commands)
- [Maintenance Mode](#maintenance-mode)
- [Testing Commands](#testing-commands)
- [Authentication Scaffolding](#authentication-scaffolding)
- [Package Management](#package-management)
- [Useful Shortcuts](#useful-shortcuts)

---

## Installation Commands

### Create New Laravel Project

```bash
# Method 1: Using Composer (Recommended)
composer create-project laravel/laravel project-name

# Specify Laravel version
composer create-project laravel/laravel:^10.0 project-name

# Method 2: Using Laravel Installer
laravel new project-name

# With authentication scaffolding
laravel new project-name --breeze
laravel new project-name --jet

# Method 3: From Git
git clone https://github.com/laravel/laravel.git project-name
cd project-name
composer install
```

### Post-Installation Setup

```bash
# Navigate to project
cd project-name

# Copy environment file
cp .env.example .env
# Windows PowerShell:
Copy-Item .env.example .env

# Generate application key
php artisan key:generate

# Install PHP dependencies
composer install

# Install Node dependencies
npm install

# Run migrations
php artisan migrate

# Start development server
php artisan serve
```

---

## Development Server

### Basic Server Commands

```bash
# Start server on default port (8000)
php artisan serve

# Specify custom host and port
php artisan serve --host=0.0.0.0 --port=8080

# Make accessible from other devices
php artisan serve --host=0.0.0.0

# Stop server
# Press Ctrl+C in terminal
```

### Environment-Specific Server

```bash
# Use specific environment file
php artisan serve --env=staging

# Clear all caches before serving
php artisan optimize:clear && php artisan serve
```

---

## Code Generation (Make Commands)

### Controllers

```bash
# Basic controller
php artisan make:controller UserController

# Resource controller (CRUD)
php artisan make:controller PhotoController --resource

# API resource controller (no create/edit)
php artisan make:controller API/PhotoController --api

# Single action controller
php artisan make:controller ShowProfile --invokable

# Controller in subdirectory
php artisan make:controller Admin/DashboardController

# Controller with model
php artisan make:controller PostController --model=Post
```

**Generated Location:** `app/Http/Controllers/`

### Models

```bash
# Basic model
php artisan make:model User

# Model with migration
php artisan make:model Post -m

# Model with migration, factory, and seeder
php artisan make:model Product -mfs

# Model with all (migration, factory, seeder, controller, resource)
php artisan make:model Category -a

# Model only (no migration)
php artisan make:model Tag --no-migration
```

**Generated Locations:**
- Model: `app/Models/`
- Migration: `database/migrations/`
- Factory: `database/factories/`
- Seeder: `database/seeders/`

### Migrations

```bash
# Create migration
php artisan make:migration create_users_table

# Create migration for existing table
php artisan make:migration add_votes_to_users_table --table=users

# Create migration with model
php artisan make:migration create_posts_table --create=posts
```

**Generated Location:** `database/migrations/`

### Middleware

```bash
# Create middleware
php artisan make:middleware CheckAge

# Create middleware in subdirectory
php artisan make:middleware Admin/CheckRole
```

**Generated Location:** `app/Http/Middleware/`

**Registration Required:** Add to `app/Http/Kernel.php`

```php
protected $routeMiddleware = [
    'check.age' => \App\Http\Middleware\CheckAge::class,
];
```

### Form Requests

```bash
# Create form request validation
php artisan make:request StoreUserRequest

# Create update request
php artisan make:request UpdatePostRequest
```

**Generated Location:** `app/Http/Requests/`

### Factories

```bash
# Create factory
php artisan make:factory UserFactory

# Factory for specific model
php artisan make:factory PostFactory --model=Post
```

**Generated Location:** `database/factories/`

### Seeders

```bash
# Create seeder
php artisan make:seeder UsersTableSeeder

# Create seeder for specific table
php artisan make:seeder PostsTableSeeder
```

**Generated Location:** `database/seeders/`

### Policies

```bash
# Create policy
php artisan make:policy PostPolicy

# Policy with model
php artisan make:policy PostPolicy --model=Post
```

**Generated Location:** `app/Policies/`

**Registration Required:** Add to `AuthServiceProvider`

```php
protected $policies = [
    Post::class => PostPolicy::class,
];
```

### Events & Listeners

```bash
# Create event
php artisan make:event UserRegistered

# Create listener
php artisan make:listener SendWelcomeEmail

# Listener for specific event
php artisan make:listener SendWelcomeEmail --event=UserRegistered
```

**Generated Locations:**
- Event: `app/Events/`
- Listener: `app/Listeners/`

**Registration Required:** Add to `EventServiceProvider`

### Jobs

```bash
# Create job
php artisan make:job ProcessPodcast

# Synchronous job
php artisan make:job SendEmail --sync
```

**Generated Location:** `app/Jobs/`

### Notifications

```bash
# Create notification
php artisan make:notification InvoicePaid
```

**Generated Location:** `app/Notifications/`

### Mail

```bash
# Create mailable
php artisan make:mail OrderShipped
```

**Generated Location:** `app/Mail/`

### Console Commands

```bash
# Create custom Artisan command
php artisan make:command SendEmails
```

**Generated Location:** `app/Console/Commands/`

**Registration Required:** Add to `$commands` array in `app/Console/Kernel.php`

### Tests

```bash
# Create feature test
php artisan make:test UserTest

# Create unit test
php artisan make:test UserTest --unit
```

**Generated Locations:**
- Feature test: `tests/Feature/`
- Unit test: `tests/Unit/`

### Providers

```bash
# Create service provider
php artisan make:provider ViewServiceProvider
```

**Generated Location:** `app/Providers/`

**Registration Required:** Add to `config/app.php` providers array

### Channels

```bash
# Create broadcast channel
php artisan make:channel ChatChannel
```

**Generated Location:** `channels/`

### Rules

```bash
# Create custom validation rule
php artisan make:rule Uppercase
```

**Generated Location:** `app/Rules/`

### Resources

```bash
# Create Eloquent resource
php artisan make:resource UserResource

# Create resource collection
php artisan make:resource UserResource --collection
```

**Generated Location:** `app/Http/Resources/`

### Exceptions

```bash
# Create custom exception
php artisan make:exception CustomException
```

**Generated Location:** `app/Exceptions/`

### Observers

```bash
# Create observer
php artisan make:observer UserObserver --model=User
```

**Generated Location:** `app/Observers/`

**Registration Required:** Add to `AppServiceProvider`

```php
public function boot()
{
    User::observe(UserObserver::class);
}
```

---

## Database Commands

### Migrations

```bash
# Run all pending migrations
php artisan migrate

# Run migrations with output
php artisan migrate --verbose

# Force migration in production
php artisan migrate --force

# Rollback last migration batch
php artisan migrate:rollback

# Rollback specific number of steps
php artisan migrate:rollback --step=3

# Reset all migrations
php artisan migrate:reset

# Refresh (rollback all + migrate)
php artisan migrate:refresh

# Refresh with seeding
php artisan migrate:refresh --seed

# Fresh migration (drop all tables + migrate)
php artisan migrate:fresh

# Fresh with seeding
php artisan migrate:fresh --seed

# Check migration status
php artisan migrate:status
```

### Seeders

```bash
# Run all seeders
php artisan db:seed

# Run specific seeder
php artisan db:seed --class=UsersTableSeeder

# Seed during migration
php artisan migrate:fresh --seed

# Seed in production
php artisan db:seed --force
```

### Database

```bash
# Create database (if supported)
php artisan db:create database_name

# Drop database (if supported)
php artisan db:drop database_name

# Show database information
php artisan db:show

# Monitor database queries
php artisan db:monitor
```

### Tinker (Interactive Shell)

```bash
# Start Tinker
php artisan tinker

# Common Tinker commands
>>> User::all()
>>> User::find(1)
>>> User::create(['name' => 'John', 'email' => 'john@example.com'])
>>> DB::table('users')->get()
>>> exit
```

---

## Cache Management

### Clear Caches

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

# Clear compiled classes
php artisan clear-compiled

# Clear events
php artisan event:clear
```

### Cache Configuration (Production)

```bash
# Cache configuration (faster loading)
php artisan config:cache

# Cache routes (faster routing)
php artisan route:cache

# Cache views (faster rendering)
php artisan view:cache

# Optimize application (all caches)
php artisan optimize
```

**Note:** Run `optimize` in production only. Use `optimize:clear` during development.

### Cache Operations in Code

```php
// Store item
Cache::put('key', 'value', $seconds);

// Retrieve item
$value = Cache::get('key');
$value = Cache::get('key', 'default');

// Check if exists
if (Cache::has('key')) {
    // ...
}

// Forget item
Cache::forget('key');

// Flush all
Cache::flush();
```

---

## Routing Commands

### List Routes

```bash
# List all routes
php artisan route:list

# Filter by method
php artisan route:list --method=GET
php artisan route:list --method=POST

# Filter by name
php artisan route:list --name=admin

# Filter by path
php artisan route:list --path=api

# Show only route names
php artisan route:list --compact

# Reverse order
php artisan route:list --reverse
```

### Route Cache

```bash
# Cache routes (production)
php artisan route:cache

# Clear route cache
php artisan route:clear
```

---

## Queue Commands

### Queue Workers

```bash
# Start queue worker
php artisan queue:work

# Start with specific connection
php artisan queue:work redis

# Start with timeout
php artisan queue:work --timeout=90

# Start with max jobs
php artisan queue:work --max-jobs=100

# Listen to queue (restarts on code changes)
php artisan queue:listen

# Listen with delay
php artisan queue:listen --delay=5
```

### Failed Jobs

```bash
# List failed jobs
php artisan queue:failed

# Retry all failed jobs
php artisan queue:retry all

# Retry specific job
php artisan queue:retry 5

# Delete all failed jobs
php artisan queue:flush

# Delete specific failed job
php artisan queue:forget 5
```

### Queue Tables

```bash
# Create failed jobs table migration
php artisan queue:failed-table

# Create jobs table migration
php artisan queue:table

# Run migrations
php artisan migrate
```

---

## Maintenance Mode

### Enable/Disable

```bash
# Enable maintenance mode
php artisan down

# Enable with message
php artisan down --message="Upgrading Database"

# Enable with retry time
php artisan down --retry=60

# Enable with secret bypass
php artisan down --secret="1630542a-246b-4b66-afa1-dd72a4c43515"

# Disable maintenance mode
php artisan up
```

### Bypass Maintenance Mode

Visit URL with secret:
```
https://example.com/1630542a-246b-4b66-afa1-dd72a4c43515
```

---

## Testing Commands

### Run Tests

```bash
# Run all tests
php artisan test

# Run with PHPUnit
./vendor/bin/phpunit

# Run specific test file
php artisan test tests/Feature/UserTest.php

# Run specific test method
php artisan test --filter=test_user_can_login

# Run with coverage
php artisan test --coverage

# Run without stopping on failure
php artisan test --parallel
```

### Test Generation

```bash
# Create feature test
php artisan make:test UserTest

# Create unit test
php artisan make:test UserTest --unit
```

---

## Authentication Scaffolding

### Laravel Breeze (Simple)

```bash
# Install Breeze
composer require laravel/breeze --dev

# Install with Blade
php artisan breeze:install

# Install with React
php artisan breeze:install react

# Install with Vue
php artisan breeze:install vue

# Install with API support
php artisan breeze:install api

# Run migrations
php artisan migrate

# Compile assets
npm install && npm run dev
```

### Laravel Jetstream (Advanced)

```bash
# Install Jetstream
composer require laravel/jetstream

# Install with Livewire
php artisan jetstream:install livewire

# Install with Inertia
php artisan jetstream:install inertia

# With teams support
php artisan jetstream:install livewire --teams

# Run migrations
php artisan migrate

# Compile assets
npm install && npm run dev
```

### Laravel UI (Legacy)

```bash
# Install UI package
composer require laravel/ui

# Generate auth scaffolding
php artisan ui bootstrap --auth
php artisan ui vue --auth
php artisan ui react --auth

# Compile assets
npm install && npm run dev
```

---

## Package Management

### Composer Commands

```bash
# Install dependencies
composer install

# Update dependencies
composer update

# Update specific package
composer update vendor/package

# Add new package
composer require vendor/package

# Add dev package
composer require vendor/package --dev

# Remove package
composer remove vendor/package

# Show installed packages
composer show

# Check for outdated packages
composer outdated

# Validate composer.json
composer validate

# Dump autoload files
composer dump-autoload

# Dump optimized autoload (production)
composer dump-autoload --optimize
```

### NPM Commands

```bash
# Install dependencies
npm install

# Install specific package
npm install package-name

# Install dev dependency
npm install package-name --save-dev

# Update packages
npm update

# Build for development
npm run dev

# Build for production
npm run build

# Watch for changes
npm run watch

# Hot module replacement
npm run hot
```

---

## Useful Shortcuts

### Common Development Workflow

```bash
# 1. Create new project
composer create-project laravel/laravel myapp
cd myapp

# 2. Setup environment
cp .env.example .env
php artisan key:generate

# 3. Configure database in .env

# 4. Create model with migration
php artisan make:model Post -m

# 5. Edit migration file

# 6. Run migrations
php artisan migrate

# 7. Create controller
php artisan make:controller PostController --resource

# 8. Define routes in routes/web.php

# 9. Create views in resources/views/

# 10. Start server
php artisan serve
```

### Quick Debugging

```bash
# Clear all caches
php artisan optimize:clear

# Check routes
php artisan route:list

# Check application info
php artisan about

# Interactive shell
php artisan tinker
```

### Production Deployment

```bash
# 1. Install dependencies
composer install --optimize-autoloader --no-dev

# 2. Cache configuration
php artisan config:cache

# 3. Cache routes
php artisan route:cache

# 4. Cache views
php artisan view:cache

# 5. Run migrations
php artisan migrate --force

# 6. Restart queue workers
php artisan queue:restart
```

---

## Command Aliases & Tips

### Bash/Zsh Aliases

Add to `~/.bashrc` or `~/.zshrc`:

```bash
# Laravel aliases
alias art='php artisan'
alias serve='php artisan serve'
alias migrate='php artisan migrate'
alias fresh='php artisan migrate:fresh --seed'
alias routes='php artisan route:list'
alias tinker='php artisan tinker'
alias test='php artisan test'
alias clear='php artisan optimize:clear'
alias cache='php artisan optimize'

# Composer aliases
alias ci='composer install'
alias cu='composer update'
alias cr='composer require'
alias cda='composer dump-autoload'
```

### PowerShell Functions

Add to PowerShell profile:

```powershell
function art { php artisan $args }
function serve { php artisan serve }
function migrate { php artisan migrate }
function fresh { php artisan migrate:fresh --seed }
function routes { php artisan route:list }
function clear { php artisan optimize:clear }
```

### Keyboard Shortcuts

**Terminal:**
- `Ctrl+C` - Stop running process
- `Ctrl+L` - Clear screen
- `Up Arrow` - Previous command
- `Tab` - Auto-complete

**Tinker:**
- `exit` or `quit` - Exit tinker
- `Ctrl+D` - Exit tinker
- `Ctrl+C` - Cancel current input

---

## Troubleshooting Commands

### Common Issues

```bash
# Permission issues (Linux/Mac)
sudo chown -R $USER:www-data storage bootstrap/cache
chmod -R 775 storage bootstrap/cache

# Clear everything and rebuild
php artisan optimize:clear
composer dump-autoload
php artisan config:clear
php artisan route:clear
php artisan view:clear

# Regenerate autoload files
composer dump-autoload

# Fix "Class not found" errors
composer dump-autoload
php artisan clear-compiled

# Fix route caching issues
php artisan route:clear
php artisan route:cache

# Fix configuration caching issues
php artisan config:clear
php artisan config:cache
```

### Log Files

```bash
# View logs (Linux/Mac)
tail -f storage/logs/laravel.log

# View logs (Windows PowerShell)
Get-Content storage/logs/laravel.log -Wait -Tail 50

# Clear logs
> storage/logs/laravel.log  # Linux/Mac
Clear-Content storage/logs/laravel.log  # Windows
```

---

## Quick Reference Card

### Most Used Commands

```bash
# Daily Development
php artisan serve                    # Start server
php artisan make:controller Name     # Create controller
php artisan make:model Name -m       # Create model + migration
php artisan migrate                  # Run migrations
php artisan route:list               # List routes
php artisan optimize:clear           # Clear caches

# Database
php artisan migrate:fresh --seed     # Fresh start with data
php artisan db:seed                  # Seed database
php artisan tinker                   # Interactive shell

# Production
php artisan optimize                 # Cache everything
php artisan config:cache             # Cache config
php artisan route:cache              # Cache routes
php artisan view:cache               # Cache views

# Debugging
php artisan about                    # App info
php artisan down                     # Maintenance mode
php artisan up                       # Live mode
```

---

## Additional Resources

### Official Documentation
- Laravel Docs: https://laravel.com/docs
- Artisan CLI: https://laravel.com/docs/artisan
- Composer: https://getcomposer.org/doc

### Community Resources
- Laracasts: https://laracasts.com
- Laravel News: https://laravel-news.com
- Stack Overflow: https://stackoverflow.com/questions/tagged/laravel

---

**Tip:** Bookmark this page for quick reference during development and exams!

---

**Back to:** [Exam Preparation](Exam_Preparation/README.md) | [Main Index](../README.md)
