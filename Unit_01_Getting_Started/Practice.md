# Unit 1 Practice Lab: Installation, Artisan & Commands

## 🔬 Practical Objectives
*   Configure local PHP & Composer environments.
*   Generate a new Laravel application.
*   Explore and verify project directory namespaces.
*   Create and run a custom Artisan command.

---

## Exercise 1: Bootstrapping a Laravel Project

### Objective: Set up a fully configured Laravel application using Command Prompt.

### Step-by-Step Instructions:

1.  **Open Command Prompt** (or PowerShell) on your machine.
2.  **Verify prerequisites**:
    ```bash
    php --version
    composer --version
    ```
    *(If command not found, download and install PHP and Composer, adding them to your system environment variables PATH).*
3.  **Generate a new Laravel project** called `LpuLaravelApp`:
    ```bash
    composer create-project laravel/laravel LpuLaravelApp
    ```
4.  **Enter the project directory**:
    ```bash
    cd LpuLaravelApp
    ```
5.  **Copy environment configuration**:
    ```bash
    copy .env.example .env
    ```
6.  **Generate the unique application key**:
    ```bash
    php artisan key:generate
    ```
7.  **Start the development server**:
    ```bash
    php artisan serve
    ```
8.  **Verify in browser**: Open `http://localhost:8000`. You should see the Laravel welcome page.

---

## Exercise 2: Building a Custom Artisan Command

### Objective: Write a custom command `php artisan lpu:exam` that prints exam details.

### Step-by-Step Instructions:

1.  **Generate the command class** using Artisan:
    ```bash
    php artisan make:command LpuExamCommand
    ```
2.  **Locate the generated file** at `app/Console/Commands/LpuExamCommand.php`.
3.  **Edit the file** and customize the signature, description, and handle logic:

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;

class LpuExamCommand extends Command
{
    // The command syntax typed in terminal
    protected $signature = 'lpu:exam {student_name}';

    // Description displayed when running 'php artisan list'
    protected $description = 'Displays details of the Laravel Practical Exam for a given student';

    public function handle()
    {
        $name = $this->argument('student_name');

        $this->info("========================================");
        $this->info("      LPU LARAVEL EXAM PORTAL           ");
        $this->info("========================================");
        $this->line("Student: " . $name);
        $this->line("Course Code: INT221 (MVC Programming)");
        $this->line("Max Marks: 100% Practical");
        $this->info("Status: READY FOR PRACTICAL EXAM");
        $this->info("========================================");

        return Command::SUCCESS;
    }
}
```

4.  **Run your custom command** in the terminal:
    ```bash
    php artisan lpu:exam "Amit Kumar"
    ```
5.  **Expected Output**:
    ```
    ========================================
          LPU LARAVEL EXAM PORTAL           
    ========================================
    Student: Amit Kumar
    Course Code: INT221 (MVC Programming)
    Max Marks: 100% Practical
    Status: READY FOR PRACTICAL EXAM
    ========================================
    ```
