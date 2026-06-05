# Laravel Study Guide - Comprehensive Curriculum & Roadmap

## 🎓 Your Complete Laravel Learning Path (Units I-VI)

This guide maps the LPU MVC Programming (INT221) curriculum to specific learning pathways, timelines, course outcomes (COs), and exam preparation lists.

---

## 📚 Course Outcomes Mapping

| Course Outcome | Focus | Unit Mapping | Primary Files |
|----------------|-------|--------------|---------------|
| **CO1** | Recall features of Laravel and the installation process. | **Unit 1** | [Notes](file:///d:/Temp/Laravel_LPU/Unit_01_Getting_Started/Notes.md), [Practice](file:///d:/Temp/Laravel_LPU/Unit_01_Getting_Started/Practice.md) |
| **CO2** | Apply routing, handle HTTP requests, and customize responses. | **Unit 2** | [Notes](file:///d:/Temp/Laravel_LPU/Unit_02_Request_Routing_Responses/Notes.md), [Practice](file:///d:/Temp/Laravel_LPU/Unit_02_Request_Routing_Responses/Practice.md) |
| **CO3** | Develop controllers, utilize Blade templates, and apply advanced routing. | **Unit 3** | [Notes](file:///d:/Temp/Laravel_LPU/Unit_03_Controllers_Blade_Advanced_Routing/Notes.md), [Practice](file:///d:/Temp/Laravel_LPU/Unit_03_Controllers_Blade_Advanced_Routing/Practice.md) |
| **CO4** | Retrieve request inputs, handle file uploads, dispatch emails, and handle sessions. | **Unit 4** | [Notes](file:///d:/Temp/Laravel_LPU/Unit_04_URL_Generation_Request_Data_Emails_Sessions/Notes.md), [Practice](file:///d:/Temp/Laravel_LPU/Unit_04_URL_Generation_Request_Data_Emails_Sessions/Practice.md) |
| **CO5** | Design forms, implement CSRF protection, and execute validation rules. | **Unit 5** | [Notes](file:///d:/Temp/Laravel_LPU/Unit_05_Form_Validation/Notes.md), [Practice](file:///d:/Temp/Laravel_LPU/Unit_05_Form_Validation/Practice.md) |
| **CO6** | Design database schemas, execute migrations, use Eloquent, and build REST APIs. | **Unit 6** | [Notes](file:///d:/Temp/Laravel_LPU/Unit_06_Database_Migrations_Eloquent_API/Notes.md), [Practice](file:///d:/Temp/Laravel_LPU/Unit_06_Database_Migrations_Eloquent_API/Practice.md) |

---

## 🗺️ Learning Roadmap

### Phase 1: Foundations & Core Architecture (Units 1 - 2)
*   **Time Needed**: 4 Days
*   **Key Focus**:
    *   Learn Model-View-Controller design pattern separation.
    *   Understand PSR-4 Autoloading and dependency management using Composer (`composer.json` vs `composer.lock`).
    *   Trace the exact Request Lifecycle from `public/index.php` to HTTP responses.
    *   Define basic HTTP routes, routing parameters (required and optional), View creation, cookie setting, and redirection strategies.
*   **Study Links**:
    *   [Unit 1 Notes](file:///d:/Temp/Laravel_LPU/Unit_01_Getting_Started/Notes.md) | [Unit 1 Practice Labs](file:///d:/Temp/Laravel_LPU/Unit_01_Getting_Started/Practice.md)
    *   [Unit 2 Notes](file:///d:/Temp/Laravel_LPU/Unit_02_Request_Routing_Responses/Notes.md) | [Unit 2 Practice Labs](file:///d:/Temp/Laravel_LPU/Unit_02_Request_Routing_Responses/Practice.md)

---

### Phase 2: Modular Development & Templates (Unit 3)
*   **Time Needed**: 3 Days
*   **Key Focus**:
    *   Write basic and resource controllers (`php artisan make:controller`).
    *   Master Blade layout files, section directives, stacks, and template inheritance.
    *   Group routes with prefixes, apply middleware layers, and map URL generation helpers.
*   **Study Links**:
    *   [Unit 3 Notes](file:///d:/Temp/Laravel_LPU/Unit_03_Controllers_Blade_Advanced_Routing/Notes.md) | [Unit 3 Practice Labs](file:///d:/Temp/Laravel_LPU/Unit_03_Controllers_Blade_Advanced_Routing/Practice.md)

---

### Phase 3: Input Handling, Mail, and Sessions (Unit 4)
*   **Time Needed**: 3 Days
*   **Key Focus**:
    *   Retrieve requests data, handle file uploads, and save directories.
    *   Configure SMTP configurations (like Mailtrap) and dispatch custom Mailables.
    *   Implement user translation files (localization matrix) and write session variables (store, delete, flash).
*   **Study Links**:
    *   [Unit 4 Notes](file:///d:/Temp/Laravel_LPU/Unit_04_URL_Generation_Request_Data_Emails_Sessions/Notes.md) | [Unit 4 Practice Labs](file:///d:/Temp/Laravel_LPU/Unit_04_URL_Generation_Request_Data_Emails_Sessions/Practice.md)

---

### Phase 4: Form Security & Validation Rules (Unit 5)
*   **Time Needed**: 2 Days
*   **Key Focus**:
    *   Authenticate submissions using CSRF tokens to block unauthorized script execution.
    *   Implement method spoofing (`@method('PUT')`) to override standard browser limitations.
    *   Write controller-based validation and dedicated custom Form Request validator classes.
    *   Build custom validation rules classes (`php artisan make:rule`).
*   **Study Links**:
    *   [Unit 5 Notes](file:///d:/Temp/Laravel_LPU/Unit_05_Form_Validation/Notes.md) | [Unit 5 Practice Labs](file:///d:/Temp/Laravel_LPU/Unit_05_Form_Validation/Practice.md)

---

### Phase 5: Database Schema, Eloquent ORM & JSON REST APIs (Unit 6)
*   **Time Needed**: 3 Days
*   **Key Focus**:
    *   Write migrations (table definitions, keys constraints) and manage database states.
    *   Populate databases using seeders and factories (Faker integration).
    *   Compare Query Builder queries with Eloquent ORM Active Record.
    *   Establish relationships: One-to-One, One-to-Many (`hasMany`), and Many-to-Many.
    *   Configure MongoDB drivers and model extensions.
    *   Build REST API resource JSON endpoints.
*   **Study Links**:
    *   [Unit 6 Notes](file:///d:/Temp/Laravel_LPU/Unit_06_Database_Migrations_Eloquent_API/Notes.md) | [Unit 6 Practice Labs](file:///d:/Temp/Laravel_LPU/Unit_06_Database_Migrations_Eloquent_API/Practice.md)

---

## 📅 Intensive 15-Day Study Schedule

| Day | Focus | Topics | Practical Exercise |
|-----|-------|--------|--------------------|
| **1-2** | **Unit 1** | MVC framework patterns, Composer setup, Directory layout, Artisan CLI commands. | [Unit 1 Lab Exercises](file:///d:/Temp/Laravel_LPU/Unit_01_Getting_Started/Practice.md) |
| **3-4** | **Unit 2** | Request lifecycle, URL routing, views, cookies, JSON and redirect responses. | [Unit 2 Lab Exercises](file:///d:/Temp/Laravel_LPU/Unit_02_Request_Routing_Responses/Practice.md) |
| **5-6** | **Unit 3** | Controllers (Resource & Custom), Middleware, Blade layouts, URL helpers. | [Unit 3 Lab Exercises](file:///d:/Temp/Laravel_LPU/Unit_03_Controllers_Blade_Advanced_Routing/Practice.md) |
| **7-8** | **Unit 4** | Requests data, file uploads, SMTP configurations, Mailables, Sessions CRUD. | [Unit 4 Lab Exercises](file:///d:/Temp/Laravel_LPU/Unit_04_URL_Generation_Request_Data_Emails_Sessions/Practice.md) |
| **9-10** | **Unit 5** | CSRF tokens, PUT/DELETE method spoofing, Form Requests validation, custom rules. | [Unit 5 Lab Exercises](file:///d:/Temp/Laravel_LPU/Unit_05_Form_Validation/Practice.md) |
| **11-12** | **Unit 6** | Migrations schemas, seeders & factories, DB Query Builder vs Eloquent CRUD. | [Unit 6 Lab Exercises (Ex 1 & 2)](file:///d:/Temp/Laravel_LPU/Unit_06_Database_Migrations_Eloquent_API/Practice.md) |
| **13-14** | **Unit 6** | Eloquent relationships (One-to-Many), MongoDB, REST API Resource Controllers. | [Unit 6 Lab Exercises (Ex 3 & 4)](file:///d:/Temp/Laravel_LPU/Unit_06_Database_Migrations_Eloquent_API/Practice.md) |
| **15** | **Revision** | Mock Exam Prep | Review Question Banks & Viva Scenarios. |

---

## 🏆 Final Assessment checklist

Before sitting for the practical or written examinations, ensure you can check off the following tasks:

### 📖 Theory & Architecture Check
- [ ] Diagram the complete MVC Request lifecycle (from `index.php` to matching controller view output).
- [ ] Describe the difference between `composer.json` package listings and exact `composer.lock` versions.
- [ ] Differentiate DB Query Builder return types (`stdClass` array) and Eloquent ORM collection outputs.
- [ ] Describe how HTTP method field spoofing overrides browser routing limitations.
- [ ] List the primary HTTP API status codes: `200`, `201`, `400`, `401`, `403`, `404`, `422`, `500`.

### 💻 Practical Coding Check
- [ ] Register routes with constraints (Regular expression checking) and route model binding keys.
- [ ] Build a master Blade layout with template inheritance (`@extends`, `@section`, `@yield`, `@stack`).
- [ ] Construct forms that submit uploaded files (multipart encoding) and display validation errors inline.
- [ ] Implement a custom Form Request validator class (`authorize()`, `rules()`, `messages()`).
- [ ] Generate a database schema using migrations, establishing a cascading foreign key constraint.
- [ ] Write seeders and Faker model factories to generate dummy databases.
- [ ] Build a REST API resource controller returning structured JSON responses.

---

## 🎤 Viva Scenario Practice Checklist
*   Review all Unit-specific viva questions for edge cases:
    *   [Unit 1 Viva Bank](file:///d:/Temp/Laravel_LPU/Unit_01_Getting_Started/Viva.md) (Artisan, Composer issues)
    *   [Unit 2 Viva Bank](file:///d:/Temp/Laravel_LPU/Unit_02_Request_Routing_Responses/Viva.md) (Lifecycle, Response structures)
    *   [Unit 3 Viva Bank](file:///d:/Temp/Laravel_LPU/Unit_03_Controllers_Blade_Advanced_Routing/Viva.md) (Blade layout compiling)
    *   [Unit 4 Viva Bank](file:///d:/Temp/Laravel_LPU/Unit_04_URL_Generation_Request_Data_Emails_Sessions/Viva.md) (File storage & Session persistence)
    *   [Unit 5 Viva Bank](file:///d:/Temp/Laravel_LPU/Unit_05_Form_Validation/Viva.md) (CSRF 419 errors)
    *   [Unit 6 Viva Bank](file:///d:/Temp/Laravel_LPU/Unit_06_Database_Migrations_Eloquent_API/Viva.md) (N+1 query problem, migrations rollbacks)
