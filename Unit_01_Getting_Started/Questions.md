# Unit 1 Question Bank: Getting Started with MVC & Laravel

## 2-Mark Questions

### Q1. Define the role of the Model inside the MVC pattern.
**Answer:** The Model represents the data layer and encapsulates the application's business logic. It queries and writes data directly to the database via database drivers, performs structural validations, and communicates back with the controller.

### Q2. Explain the difference between `composer.json` and `composer.lock`.
**Answer:**
*   `composer.json` declares the package dependencies along with acceptable version ranges (e.g., `^10.0`).
*   `composer.lock` records the exact installed version of every single package, ensuring all environments download the identical dependency tree.

### Q3. Explain the PSR-4 Autoloading standard.
**Answer:** PSR-4 is a PHP Standard Recommendation that maps class namespaces directly to filesystem directory paths. This allows PHP engines to automatically load classes without requiring explicit `require_once` declarations.

### Q4. What is the entry point of a Laravel application?
**Answer:** `public/index.php` is the single entry point for all HTTP requests. It loads the autoloader, instantiates the Laravel service container, retrieves the HTTP Kernel, handles the incoming request, and returns the response.

### Q5. Write the Artisan command to create a model with its corresponding migration file.
**Answer:**
```bash
php artisan make:model Product -m
```

---

## 5-Mark Questions

### Q6. Discuss the key architectural differences between traditional PHP development and modern Laravel framework development.
**Answer:**

| Feature | Traditional PHP | Laravel Framework |
|---------|-----------------|-------------------|
| **Architecture** | Typically procedural or ad-hoc OOP; files are mixed with view HTML and logic. | Strict Model-View-Controller (MVC) separation of concerns. |
| **Routing** | Done by mapping URLs to file paths (e.g., `about.php`). | Centralized routing mapping requests to controllers using HTTP verbs. |
| **Database Access** | Raw SQL queries via PDO or mysqli, manually binding params. | Eloquent Object-Relational Mapper (ORM) using Active Record. |
| **Security** | Security checks (CSRF, XSS, Hashing) must be written manually. | Default built-in middleware for CSRF, XSS filtering, and password hashing. |
| **Boilerplate** | Files and databases must be structured manually. | Command-line Artisan tool generating models, controllers, and migrations. |

### Q7. Explain the complete Laravel Directory Structure, highlighting the responsibilities of the `/app`, `/config`, `/database`, and `/public` directories.
**Answer:**
1.  **`/app` Directory**: The core directory of the application. It contains backend logic, including controllers (`app/Http/Controllers/`), models (`app/Models/`), and custom middleware (`app/Http/Middleware/`).
2.  **`/config` Directory**: Contains configuration files mapping environment variables (`.env`) to key settings, such as app name, database connections, session drivers, and mail configurations.
3.  **`/database` Directory**: Houses database structures. It contains `migrations/` for schema design, `seeders/` for populating mock data, and `factories/` for mass mock data generation.
4.  **`/public` Directory**: The only folder exposed to the web server. It contains `index.php` (application bootstrap entry point) and static resources (JavaScript, compiled CSS, media images).

### Q8. What is Composer? Describe how it manages packages and autoloader registration in Laravel.
**Answer:**
Composer is PHP's package dependency manager.
*   **Package Management**: Developers request third-party packages in `composer.json`. Running `composer install` reads the file, downloads the package contents into the `/vendor` folder, and updates the `composer.lock` file with the exact version hash.
*   **Autoloader Registration**: Composer scans all loaded packages and registers mapping structures. It outputs an autoloader file in `vendor/autoload.php` which maps namespaces (like `App\Models\User`) to specific file paths (like `app/Models/User.php`). This class mapper is loaded at runtime in `public/index.php`.

---

## 10-Mark Questions

### Q9. Explain the MVC (Model-View-Controller) architecture in detail. Provide a detailed scenario of how a request flows through the MVC layers of a Laravel application.
**Answer:**
The Model-View-Controller design pattern separates backend logic, UI presentation, and request routing to promote clean code decoupling.

#### 1. MVC Component Breakdown:
*   **Model**: Represents database schemas and records. It encapsulates SQL operations, data relationships, and validation constraints.
*   **View**: The presentation layer. It displays processed data to users via HTML templates and collects user inputs.
*   **Controller**: The traffic manager. It handles requests, interacts with models, and passes variable payloads to views.

#### 2. Request Flow Scenario:
Let's trace a user visiting `http://domain.com/books`:

```
User Request (GET /books)
  │
  ▼
public/index.php (App bootstrapped)
  │
  ▼
routes/web.php (Matched to BookController@index)
  │
  ▼
VerifyCsrfToken / Middlewares (Request checked and authenticated)
  │
  ▼
BookController@index (Invoked)
  │
  ├─> Book::all() (Queries the Book model)
  │     │
  │     ▼
  │   SQL: SELECT * FROM books (Executed via Eloquent ORM)
  │     │
  │     ▼
  │   Model returns collection of Book objects to Controller
  │
  ▼
BookController returns view('books.index', ['books' => $books])
  │
  ▼
Blade compiles books/index.blade.php containing HTML layout
  │
  ▼
HTTP 200 HTML Response returned to User's browser
```

1.  **Entry**: The client makes a `GET` request. The web server routes it to `public/index.php`.
2.  **Routing**: The router matches `/books` to `BookController@index`.
3.  **Controller processing**: The controller calls the `Book` model: `Book::all()`.
4.  **Model Database query**: The model executes the query and returns the record collection.
5.  **View compile**: The controller forwards the records to the `books/index.blade.php` view.
6.  **Response Delivery**: The view compiles into HTML and returns a 200 HTTP response.
