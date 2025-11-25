# HTTP Routing Implementation Examples

***

## Overview

Explore practical examples of HTTP routing in Luminova. Learn how to define routes, use attributes, handle dynamic URI segments, and implement controllers for web requests.

***

## Introduction

This page shows **different examples of using Luminova HTTP routing with a View Controller**.
For deeper understanding, see these references first:

* [HTTP Controller](/controllers/http-controller.md)
* [Routing System Overview](/routing/system.md)
* [HTTP Routing](/routing/http.md)
* [Attribute Routing](/attributes.md)
* [Dynamic Placeholders](/routing/dynamic-uri-placeholder.md)

---

## Examples

### Method-Based Routes

When you are **not using attribute routing**, you define your URI prefixes in `/public/index.php`.
To learn more about prefixes and context files, see [Routing URI Prefix](/routing/uri-prefix.md).

**Example:**
The following setup maps any request URI prefix starting with:

- `/admin` to the `routes/admin.php` file.
- `/api` to the `routes/api.php` file.
- While the `Prefix::WEB` is global fall all URI prefix that doesn't have a custom handler

So visiting `https://example.com/admin/` will automatically load that route file.

```php
// /public/index.php

use Luminova\Boot;
use Luminova\Routing\Prefix;

Boot::http()->router->context(
    new Prefix(Prefix::WEB, [ErrorController::class, 'onWebError']),
    new Prefix(Prefix::API, [ErrorController::class, 'onApiError']),
    new Prefix('admin', [ErrorController::class, 'onAdminError'])
)->run();
```

> **Note:**
> Do not rename the default `Context::WEB` route. Changing its name can cause unexpected errors in your application.

---

#### Route context file

Every URI prefix you register can have a matching file in `/routes/`.
For example, the `admin` prefix above corresponds to `/routes/admin.php`.
Inside this file, you define all routes under `/admin/` using the global `$router` instance.

```php
// /routes/admin.php

/**
 * Available in file scope
 * 
 * @var Luminova\Routing\Router $router
 */

$router->get('/admin/', 'AdminController::index');
```

---

### Attribute-Based Routes

When **attribute routing is enabled**, you no longer need to manually register URI prefixes in `/public/index.php`.
Instead, you define them directly inside your controller class using the `#[Prefix]` attribute.

**Global Website Controller Example:**

The following controller automatically handles all website requests (except `/api/...` and `/admin/...`) and uses `ErrorController::onWebError` for global error handling, if an error occurs.

```php
// /app/Controllers/Http/Controller.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

// Exclude /api and /admin from this controller
#[Prefix(pattern: '/(:base)', exclude: ['api', 'admin'], onError: [ErrorController::class, 'onWebError'])]
class MainController extends Controller
{
    // Define controller methods here
}
```

**Custom Admin Prefix Controller Example:**

Define controller to handle all URI prefix with (`admin`).

```php
// /app/Controllers/Http/AdminController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/admin/(:root)', onError: [ErrorController::class, 'onWebError'])]
class AdminController extends Controller
{
    // Define controller methods here
}
```

---

**Custom API Prefix Controller Example:**

Define controller to handle all URI prefix with (`api`).

```php
// /app/Controllers/Http/AdminController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

// Optionally restrict with version code e.g /api/1.0.0
// #[Prefix(pattern: '/api/(:version)', onError: [ErrorController::class, 'onApiError'])]

#[Prefix(pattern: '/api/(:root)', onError: [ErrorController::class, 'onWebError'])]
class RestController extends Controller
{
    // Define controller methods here
}
```

---

With attribute routing, your `index.php` setup stays clean because the router automatically reads prefixes from attributes:

```php
// /public/index.php

use Luminova\Boot;

Boot::http()->router->context()->run();
```

***

## Middlewares

Middleware lets you intercept incoming requests **before they reach your controllers**.
This is useful for tasks like authentication, access control, or pre-processing.

If middleware fails (returns an error), **the controller will never be called** — instead, the defined error handler will run.

Middleware should return:

* `STATUS_SUCCESS` (allow request to proceed)
* `STATUS_ERROR` (block request and trigger error)

---

### Using middleware with a route attribute

In this example, we use **session authentication**.
We assume that the session object is available as `$this->app->session` inside your `App\Application` class.

```php
// /app/Controllers/Http/MyController.php

use Luminova\Attributes\Route;

#[Route('/(:root)', methods: ['ANY'], middleware: Route::HTTP_BEFORE_MIDDLEWARE)]
public function middleware(): int
{
    if ($this->app->session->isOnline()) {
        return STATUS_SUCCESS;
    }

    return STATUS_ERROR;
}
```

---

### Using middleware with method-based routing

If you're not using attributes, you can attach middleware directly in your route file:

```php
// /routes/web.php

$router->middleware('ANY', '/(:root)', 'MyController::middleware');
```

---

### Using middleware with a closure

You can also define middleware inline using a closure:

```php
// /routes/web.php

$router->middleware('ANY', '/(:root)', static function (App\Application $app): int {
    if ($app->session->isOnline()) {
        return STATUS_SUCCESS;
    }

    return STATUS_ERROR;
});
```

**Middleware in bind URI group**

You can also define middleware in the router's `bind` method to apply it to grouped routes.

```php
// /routes/web.php
		 
$router->bind('/user', static function() use($router, $app): void {
    // In group global scope.
	$router->middleware('ANY', '/(:root)', 'MyController::middleware');
	$router->get('/profile', 'MyController::profile');
});
```

***

## Rendering templates

Luminova uses the `Luminova\Template\View` class to render pages.
You call the `view()->render()` method to display a template and pass any variables you need.

Here's how to set up a simple landing page route.

---

### Rendering with an attribute

Define a controller method with a `#[Route]` attribute:

```php
// /app/Controllers/Http/MainController.php

use Luminova\Attributes\Route;

#[Route('/', methods: ['GET'])]
public function index(): int
{
    return $this->app->view->view('index')->render([
        'title' => 'Home Page'
    ]);
}
```

---

### Rendering with method-based routing

You can also render templates inside a route context file such as `/routes/web.php`.

**Using a controller method handler:**

```php
// /routes/web.php

$router->get('/', 'MainController::index');
```

---

**Using a closure handler:**

```php
// /routes/web.php

$router->get('/', static function (App\Application $app): int {
    return $app->view->view('index')->render([
        'title' => 'Home Page'
    ]);
});
```

---

## 5. REST API responses

While `Luminova\Template\View` can technically output JSON or other formats (as long as you use a template file in `/resources/Views/`), this approach isn't ideal for small API responses.

For APIs, it's better to use the `Luminova\Template\Response` or global `response()` helper to return JSON, XML, or any other structured format directly — without creating a template file.

---

### Returning JSON with an attribute

```php
// /app/Controllers/Http/RestController.php

use Luminova\Attributes\Route;
use Luminova\Template\Response;

#[Route('/api/info', methods: ['GET'])]
public function info(): int
{
    return (new Response(200))->json([
        'status' => 'OK',
        'title' => 'Home Page'
    ]);
}
```

---

#### Returning a response object instead

Controller routable methods can return:

* an HTTP status code (`int`),
* a `Luminova\Template\Response` object, or any class implementing `Luminova\Interface\ViewResponseInterface`.
* a `Luminova\Http\Message\Response` object, or any class implementing `Luminova\Interface\ResponseInterface` or `Psr\Http\Message\ResponseInterface`.

This gives you flexibility — use a status code for simple results or a full response object when you need more control.

```php
// /app/Controllers/Http/RestController.php

use Luminova\Attributes\Route;
use Luminova\Template\Response;

#[Route('/api/info', methods: ['GET'])]
public function info(): Response
{
    return (new Response(200))->content([
        'status' => 'OK',
        'title' => 'Home Page'
    ]);
}
```

---

### Returning JSON with method-based routing

```php
// /routes/api.php

$router->get('/api/info', 'RestController::info');
```

---

### Returning JSON with a closure

```php
// /routes/api.php

use Luminova\Template\Response;

$router->get('/api/info', static function () use ($app): int {
    return (new Response (200))->json([
        'status' => 'OK',
        'title' => 'Home Page'
    ]);
});
```

***

## Dynamic URI segments

You can capture parts of the URL using **patterns**, **named placeholders**, or **predefined placeholder patterns**.
This allows your routes to handle URLs like `https://example.com/user/peter1` dynamically.

In this example, we use **predefined placeholder patterns** because they are cleaner and enforce strict validation.
They work like regular expressions, but without the extra complexity — since Luminova already provides common patterns.

> **Tip:**
> Avoid using named placeholders like `/foo/{username}` because they do not validate the data type or format of the captured segment. Predefined placeholders automatically ensure the value matches the expected pattern.

### Capturing segments with an attribute

```php
// /app/Controllers/Http/MyController.php

use App\Models\User;
use Luminova\Attributes\Route;

#[Route('/user/(:username)', methods: ['GET'])]
public function profile(string $username): int
{
    return $this->app->view->view('user.profile')->render([
        'username' => $username,
        'user' => (new User())->find($username)
    ]);
}
```

---

### Capturing segments with method-based routing

```php
// /routes/web.php

$router->get('/user/(:username)', 'Controller::profile');
```

> These examples capture a dynamic username from the URL and pass it to the controller method to fetch and display the user profile.

---

### Handling request methods with an attribute

```php
// /app/Controllers/Http/RequestController.php

use Luminova\Attributes\Route;
use App\Models\User;

#[Route('/user/(:username)', methods: ['POST'])]
public function update(string $username): int
{
    $name = $this->request->getPost('name');
    $email = $this->request->getPost('email');

    $updated = (new User())->update($username, [
        'name' => $name,
        'email' => $email
    ]);

    return response()->json([
        'status' => $updated ? 'OK' : 'ERROR',
        // Add additional response fields as needed
    ]);
}
```

---

### Handling requests methods with method-based routing

```php
// /routes/user.php

$router->post('/user/(:username)', 'RequestController::update');
```

***

## Nested binding and Aliases

You can group related routes together under a shared path segment.
For example, if you want to handle `/user` and nested paths like `/user/peter1`, you can bind them using a closure or define them directly in a controller with attributes.

### Route Aliases (Redirects or Alternate Paths)**

Show how to point multiple URLs to the same controller method — useful for legacy routes or SEO.

```php
#[Route('/about', methods: ['GET'], aliases: ['/about-us', '/company/about'])]
public function about(): int 
{
    return $this->view('about');
}
```

### Using method-based routing with bind

You can bind all `/user/...` routes to a single block in your route file:

```php
// /routes/web.php

use Luminova\Routing\Router;
use App\Application;

$router->bind('/user/(:root)', function (Router $router, Application $app) {
    $router->get('/', 'UserController::dashboard');
    $router->get('/(:username)', 'UserController::profile');
});
```

---

### Using separate route attributes

You can map each route to its own controller method:

```php
// /app/Controllers/Http/UserController.php

use App\Models\User;
use Luminova\Attributes\Route;

#[Route('/user', methods: ['GET'])]
public function dashboard(): int
{
    return $this->view('user.dashboard');
}

#[Route('/user/(:username)', methods: ['GET'])]
public function profile(string $username): int
{
    return $this->view('user.profile', [
        'username' => $username,
        'user' => (new User())->find($username)
    ]);
}
```

---

### Using multiple route attributes on one method

If you prefer to handle both `/user` and `/user/(:username)` with the same method, you can stack route attributes, use aliases or `(:optional)` placeholder.

#### 1. Stack multiple route attributes

Define more than one `#[Route]` attribute on the same method:

```php
// /app/Controllers/Http/UserController.php

use App\Models\User;
use Luminova\Attributes\Route;

#[Route('/user', methods: ['GET'])]
#[Route('/user/(:username)', methods: ['GET'])]
public function user(?string $username = null): int
{
    if ($username === null) {
        return $this->view('user.dashboard');
    }

    return $this->view('user.profile', [
        'username' => $username,
        'user' => (new User())->find($username)
    ]);
}
```

---

#### 2. Use an optional placeholder

Use `(:optional)` if the second segment is not required:

```php
// /app/Controllers/Http/UserController.php

use App\Models\User;
use Luminova\Attributes\Route;

#[Route('/user/(:optional)', methods: ['GET'])]
public function user(?string $username = null): int
{
    if ($username === null) {
        return $this->view('user.dashboard');
    }

    return $this->view('user.profile', [
        'username' => $username,
        'user' => (new User())->find($username)
    ]);
}
```

---

#### 3. Define route aliases

Use a single `#[Route]` with an `aliases` array for cleaner definitions:

```php
// /app/Controllers/Http/UserController.php

use App\Models\User;
use Luminova\Attributes\Route;

#[Route('/user', methods: ['GET'], aliases: ['/user/(:optional)'])]
public function user(?string $username = null): int
{
    if ($username === null) {
        return $this->view('user.dashboard');
    }

    return $this->view('user.profile', [
        'username' => $username,
        'user' => (new User())->find($username)
    ]);
}
```

> **Your Choice?**
> * **Stacking attributes** — explicit but longer.
> * **Optional placeholder** — simplest if the paths are similar.
> * **Aliases** — clean and centralized.

***

## Custom Template Folder

By default, templates load from `resources/Views`. If you want to organize views in sub-folders — for example, to separate `users` templates from `admin` templates — you can set a **custom view folder** using `setFolder()`.

This can be done globally (for a whole controller or route group) or locally (just before rendering a single view).

### Setting a custom view folder in a controller

If all views in a controller should come from the same folder, call `setFolder()` inside `onCreate()`, `__construct()`, or your middleware method:

```php
// /app/Controllers/Http/UserController.php

use Luminova\Base\Controller;

class UserController extends Controller 
{
    protected function onCreate(): void 
    {
        $this->app->view->setFolder('users');
    }
}
```

---

### Setting a custom view folder in a controller method

**A per-method override inside a controller**, where only one method uses a custom folder without changing the folder for the whole controller. This shows that `setFolder()` can be called anywhere — not just at controller creation or in routes.

Example:

```php
// /app/Controllers/Http/UserController.php

use Luminova\Base\ControllerController;

class UserController extends ontroller 
{
    public function dashboard(): int
    {
        return $this->view('dashboard'); // uses default folder
    }

    public function profile(): int
    {
        return $this->app->view->setFolder('users')
            ->view('profile')
            ->render();
    }
}
```

---

### Setting a custom folder inside a route context

You can also set folders in `routes/web.php` or similar files:

```php
// /routes/web.php

use Luminova\Routing\Router;
use App\Application;

// Set globally for the entire route file
$app->view->setFolder('users');

$router->bind('/user', static function() use ($router, $app): void {
    // Set for all routes in this group
    $app->view->setFolder('users');

    $router->get('/', static function() use ($app): int {
        // Or set only for this route before rendering
        return $app->view->setFolder('users')
            ->view('dashboard')
            ->render();
    });
});
```

---

**Why use this?**

* Keeps templates organized by feature (e.g. `resources/Views/users`, `resources/Views/panel`).
* Works well with **HMVC-style structure** — each module or context can have its own view folder automatically.
* Reduces repetitive folder references in your `view()` calls.