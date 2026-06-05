# Unit 1 Viva Questions & Answers

### Q1: What is the main purpose of MVC architecture?
**Answer:** To separate application logic into three distinct layers: Model (Data & Rules), View (User Interface), and Controller (Flow Control). This makes applications modular, easier to test, and maintainable.

### Q2: What is Composer and why is it essential for Laravel?
**Answer:** Composer is a dependency manager for PHP. It downloads, updates, and manages external libraries and packages required by Laravel, and automatically registers PSR-4 autoload rules for classes.

### Q3: Where are all the composer packages stored in a Laravel project?
**Answer:** In the `/vendor` folder located in the project's root directory. You should never modify files in this folder directly.

### Q4: Should the `composer.lock` file be committed to Git? Why?
**Answer:** Yes. It locks the exact versions of dependencies that were installed when the project was created or updated. This ensures that every developer working on the project has the identical environment.

### Q5: What happens when you run `composer dump-autoload`?
**Answer:** Composer regenerates its class mapping and class locations database in the `vendor/composer/` directory. This is useful for resolving "Class not found" errors after adding new PHP classes.

### Q6: What is Artisan? Give three examples of Artisan commands.
**Answer:** Artisan is Laravel's built-in command-line interface. Examples:
1. `php artisan serve` (Starts local server)
2. `php artisan make:controller ControllerName` (Generates controller)
3. `php artisan migrate` (Executes database migrations)

### Q7: If you run `php artisan serve` and get an error saying "command not found", what is the cause?
**Answer:** This happens either because:
1. PHP is not installed or not added to your system's environment variable PATH.
2. You are not in the root directory of a Laravel project (the command must be run from where the `artisan` file exists).

### Q8: What does the command `php artisan key:generate` do? Where does it write the key?
**Answer:** It generates a unique 32-character random string and writes it to the `APP_KEY` variable inside your `.env` file. This key is used to encrypt sessions, cookies, and tokens.

### Q9: Where are Laravel model classes stored by default?
**Answer:** In the `app/Models/` directory.

### Q10: Where do you find the configuration file for database connections in Laravel?
**Answer:** In `config/database.php`. However, database credentials themselves are stored in the `.env` file at the root.

### Q11: What is the purpose of the `/storage` directory?
**Answer:** It stores framework-generated files, including compiled Blade views, user sessions, system logs (`storage/logs/laravel.log`), and file uploads.

### Q12: How do you check the version of Laravel installed on your system?
**Answer:** Run the command:
```bash
php artisan --version
```

### Q13: What does the `-m` flag do when running `php artisan make:model Product -m`?
**Answer:** It automatically generates a database migration file for the `products` table in the `database/migrations/` directory along with the model.

### Q14: Where do you define HTTP route paths in Laravel?
**Answer:** In the `routes/web.php` file for web requests, and `routes/api.php` for API endpoints.

### Q15: What is the role of `bootstrap/app.php`?
**Answer:** It configures the Laravel application, registers core kernels, defines routing files, and registers middleware pipelines.
