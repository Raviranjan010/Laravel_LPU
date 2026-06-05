# Unit 4 Viva Questions & Answers

### Q1: What does the command `php artisan storage:link` actually do?
**Answer:** It creates a symbolic link (symlink) from the public folder (`public/storage`) pointing to the private app storage folder (`storage/app/public`). This makes uploaded files accessible via browser URLs.

### Q2: What HTML attribute is required on a `<form>` tag to allow file uploads?
**Answer:** `enctype="multipart/form-data"` is required. Without it, files will not be uploaded, and `$request->file('avatar')` will return `null`.

### Q3: How do you retrieve an uploaded file and verify it is valid?
**Answer:**
```php
if ($request->hasFile('image') && $request->file('image')->isValid()) {
    $file = $request->file('image');
}
```

### Q4: What is the class name of an uploaded file in Laravel?
**Answer:** `Illuminate\Http\UploadedFile` (which inherits from Symfony's `UploadedFile` class).

### Q5: How do you generate a unique filename for an uploaded file before storing it?
**Answer:** Get the extension and concatenate a unique value like a timestamp or UUID:
```php
$name = 'file_' . time() . '.' . $file->getClientOriginalExtension();
```

### Q6: Where is mail server configuration defined?
**Answer:** In the `.env` file at the project root under configuration keys starting with `MAIL_`.

### Q7: What command generates a Mailable class?
**Answer:**
```bash
php artisan make:mail UserWelcome
```

### Q8: What does the `envelope()` method inside a Mailable class configure?
**Answer:** It configures envelope properties, including the sender's email address (`from`), dynamic subject line (`subject`), and optional CC/BCC recipients.

### Q9: Where are localization (translation) files stored in Laravel?
**Answer:** In the root `/lang` folder (or `resources/lang` in older versions). Inside, each language has its own subfolder (e.g. `/lang/en/` and `/lang/hi/`).

### Q10: How do you dynamically change the language of a Laravel app at runtime?
**Answer:**
```php
use Illuminate\Support\Facades\App;

App::setLocale('hi'); // Switches application language to Hindi
```

### Q11: What is the default session driver in a fresh Laravel installation? Where does it store data?
**Answer:** The `file` session driver. It stores session data in flat text files inside `storage/framework/sessions/`.

### Q12: How do you check if a key exists in a session and is not null?
**Answer:** Use the `session()->has('key')` method.

### Q13: What is the difference between `session()->has('key')` and `session()->exists('key')`?
**Answer:**
*   `has()` returns `true` only if the key exists **and its value is not null**.
*   `exists()` returns `true` if the key exists, **even if its value is null**.

### Q14: What is a flash session?
**Answer:** A temporary session variable that exists only for the next immediate HTTP request (commonly used to display success messages after redirecting a form).

### Q15: How do you clear all session variables?
**Answer:**
```php
session()->flush();
// or
$request->session()->flush();
```
