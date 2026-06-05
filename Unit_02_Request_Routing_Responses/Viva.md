# Unit 2 Viva Questions & Answers

### Q1: Can you describe the first step of the Laravel Request Lifecycle?
**Answer:** The request enters through `public/index.php`. It initializes the autoloader and bootstraps the application by loading the Laravel service container.

### Q2: What is the HTTP Kernel's primary job during request execution?
**Answer:** The HTTP Kernel (`app/Http/Kernel.php`) defines global middleware stacks that every request passes through (e.g. CSRF validation, checking maintenance mode, trim strings).

### Q3: What is the difference between `Route::match` and `Route::any`?
**Answer:**
*   `Route::match(['GET', 'POST'], ...)` registers a route that responds only to specific listed HTTP methods.
*   `Route::any(...)` registers a route that responds to all HTTP request verbs.

### Q4: If you define a route parameter as `{id}`, can it be null?
**Answer:** No. `{id}` is a required parameter. If it is omitted from the URL (e.g., visiting `/user/` instead of `/user/5`), the router will throw a 404 Not Found error.

### Q5: How do you make a route parameter optional?
**Answer:** By appending a `?` to the parameter name (e.g. `{name?}`) and assigning a default value to the variable in the route closure or controller (e.g. `$name = 'Guest'`).

### Q6: If a route has a constraint `->where('id', '[0-9]+')` and a user visits `/user/abc`, what happens?
**Answer:** The router fails to match `/user/abc` because `'abc'` is not numeric. It will throw a 404 HTTP error.

### Q7: Why should you use named routes instead of hardcoded URL paths?
**Answer:** Named routes allow you to generate URLs programmatically. If you decide to change the route's URL path in `routes/web.php` later, all your links and redirects using `route('name')` will update automatically without breaking.

### Q8: What does the helper function `compact('name', 'email')` return?
**Answer:** It returns an associative array where variable names map to keys: `['name' => $name, 'email' => $email]`.

### Q9: How do you share a piece of data with all views in your application?
**Answer:** Call `View::share('key', 'value')` inside the `boot()` method of a service provider, typically `app/Providers/AppServiceProvider.php`.

### Q10: How do you append a custom header to a response in Laravel?
**Answer:** By chaining the `header()` method to the response helper:
```php
return response($content)->header('X-Custom-Header', 'Value');
```

### Q11: Where are cookies configured and managed in an outgoing response?
**Answer:** Using the `cookie()` helper method on the response object:
```php
return response('Success')->cookie('name', 'value', $minutes);
```

### Q12: How do you delete a cookie in Laravel?
**Answer:** By returning a response with a forgotten cookie using the `Cookie::forget` method:
```php
return response('Cleared')->cookie(Cookie::forget('cookie_name'));
```

### Q13: What is the default content type returned by `response()->json(...)`?
**Answer:** It returns `application/json` as the `Content-Type` header, along with a 200 OK status code by default.

### Q14: How do you redirect a user to a specific controller action?
**Answer:** Using the `action()` method inside redirect:
```php
return redirect()->action([UserController::class, 'index']);
```

### Q15: What is flash data and how do you send it?
**Answer:** Flash data is session data that exists only for the next request. It is sent using the `with()` method:
```php
return redirect()->route('dashboard')->with('status', 'Task Complete!');
```
