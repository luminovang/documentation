# Route Attributes for HTTP and CLI Controller Methods

***

## Overview

Learn how to map HTTP requests and CLI commands directly to controller methods using Route attributes — with patterns, groups, middleware, and error handling.

***

## Introduction

The **Route Attribute** in Luminova provides a clean, declarative way to define URI routes directly inside your controller methods — for both **HTTP** and **CLI** applications.
Because this attribute is **repeatable**, you can assign multiple routes to a single method or applying it to different methods as needed.

Luminova uses **PHPToken** to parse controller classes and resolve the correct handler for each request. While this process could be heavy in theory, Luminova supports **attribute caching** for production environments, eliminating the need to re-parse attributes on every request and greatly improving performance.

For larger projects, Luminova includes a `novakit` command to automatically **export and cache attribute-based routes** and convert the to **method-based routing**. This allows you to benchmark or fully switch to an optimized, file-based routing setup without losing the convenience of attributes.

You can run:

```bash
php novakit context --export-attr
```

This command will:

* Export all route attributes into optimized method-based context files.
* Automatically create routing contexts for fast resolution.
* Disable runtime parsing of PHP attributes, using the cached routes instead.

***

### Setting up Attribute Routing

The **PHP attribute routing**, allowing you to define routes directly in your controller classes without creating separate route files. Follow these steps to enable and optimize it:

---

#### 1. Enable Attribute Routing

Edit your `.env` file and set the `feature.route.attributes` option to `enable`:

```ini
feature.route.attributes = enable
```

---

#### 2. Enable Attribute Caching

To avoid re-parsing attributes on every request (which saves time in production), enable caching:

```ini
feature.route.cache.attributes = enable
```

The cached routes will be stored in:
`/writeable/caches/routes/`

---

#### 3. Adjust Route Context Initialization

When using attributes, you don’t need to predefine route contexts manually.
In your `public/index.php`, you may currently have something like:

**Using the `Prefix` class:**

```php
Boot::http()->router->context(
   new Prefix(Prefix::WEB, [ErrorController::class, 'onWebError']),
   new Prefix(Prefix::API, [ErrorController::class, 'onApiError']),
   new Prefix(Prefix::CLI)
)->run();
```

**Or using arrays:**

```php
Boot::http()->router->context(
   [
      'prefix' => 'web',
      'error' => [ErrorController::class, 'onWebError']
   ],
   [
      'prefix' => 'api',
      'error' => [ErrorController::class, 'onApiError']
   ],
   [
      'prefix' => 'cli',
      'error' => null
   ]
)->run();
```

---

#### 4. Simplify the Configuration

When routing with attributes, switch to a blank context initialization:

```php
Boot::http()->router->context()->run();
```

> **Why simplify?**
> This prevents the router from loading unnecessary context files, reducing overhead and improving overall performance.

***

## Usages

### HTTP Controller Class Example

In this example, we assume you have a controller class named `RequestController`.
The `HTTP` routes can respond to basic methods such as `GET`, `POST`, `PUT`, `DELETE`, `OPTIONS`, and more.

```php
// /app/Controllers/Http/RequestController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;

class RequestController extends Controller
{
   // Your controller methods
}
```

---

## Defining Routes with Attributes

### Basic HTTP Route

Defines an HTTP route that responds to `GET` requests at the root URL (`/`):

```php
#[Route('/', methods: ['GET'])]
public function index(): int
{
   // Your code here
}
```

**Equivalent in Method-Based:**

```php
$route->get('/', 'RequestController::index');
```

---

### HTTP Route with Middleware

Defines an HTTP route that executes middleware **before** running the main route handler:

```php
#[Route('/', methods: ['GET'], middleware: Route::HTTP_BEFORE_MIDDLEWARE)]
public function middleware(): int
{
   // Your code here
   // return STATUS_SUCCESS or STATUS_ERROR to fail;
}
```

**Equivalent Router Method:**

```php
$route->middleware('GET', '/', 'RequestController::middleware');
```

---

### HTTP Route with Error Handling

Defines a route to handle unmatched requests or errors using the `error` flag:

```php
#[Route('/(:root)', methods: ['ANY'], error: true)]
public function error(): int
{
   // Your error response
}
```

**Equivalent in Method-Based:**

```php
$route->setErrorListener('/(:root)', 'RequestController::error');
```

***

## CLI Command Routing

The **CLI router** works differently from the **HTTP router**. Commands do not use HTTP methods like `GET` or `POST`. Instead, they are organized into **command groups** and executed directly from the terminal.

You can define CLI commands in two ways:

* Using the router’s `command` method.
* Using the `Route` attribute with the `group` argument inside your controller class.

---

### CLI Controller Example

Assume you have a controller named `CommandController`:

```php
// app/Controllers/Cli/CommandController.php

namespace App\Controllers\Cli;

use Luminova\Base\Command as BaseCommand;
use Luminova\Attributes\Route;
use Luminova\Attributes\Group;

#[Group(name: 'foo')]
class CommandController extends BaseCommand
{
   // CLI command methods go here
}
```

---

### Basic CLI Route

This creates a command `php index.php foo bar`.
The `foo` group comes from the `Group` attribute above, while the `bar` command is defined here:

```php
#[Route('bar', group: 'foo')]
public function barMethod(): int
{
   // Your CLI logic here
}
```

**Equivalent in Method-Based:**

```php
$router->group('foo', static function(Router $router) {
    $router->command('bar', 'CommandController::barMethod');
});
```

---

### CLI Route with Middleware

CLI routes only support two middleware types:

* `Route::CLI_GROUP_MIDDLEWARE`
* `Route::`

To apply middleware at the group level:

```php
#[Route(group: 'foo', middleware: Route::)]
public function middleware(): int
{
   // Your middleware logic here
}
```

**Equivalent in Method-Based:**

```php
$router->group('foo', static function(Router $router) {
   $router->guard('foo', 'CommandController::middleware');
   // Commands go here
});
```

---

### Global Command Middleware

A **global middleware** runs before any command in the application:

```php
#[Route(group: 'foo', middleware: Route::CLI_GROUP_MIDDLEWARE)]
public function middleware(): int
{
   // Your global middleware logic here
}
```

**Equivalent Router Method:**

```php
$router->guard('global', 'CommandController::middleware');
$router->group('foo', static function(Router $router) {
   // Commands go here
});
```

***

## Defining Multiple Routes for One Method

Sometimes you want one controller method to respond to several different URLs. With the **Route** attribute, there are two ways to do this:

1. **Repeat the attribute for each URL pattern**
2. **Use the `$aliases` property in a single attribute**

**Recommendations:**

* **Use `$aliases` for static paths that should behave exactly the same.**
  For example, `/`, `/home`, `/dashboard` should all load the same page.

* **Use repeated attributes for dynamic routes or when each URL needs its own configuration.**
  For example, if `/home` is `GET` only but `/dashboard` should allow both `GET` and `POST`.

---

### Repeated Route Attribute

You can add the `Route` attribute multiple times if different URLs need to point to the same method but may have different HTTP methods or settings.

```php
#[Route('/', methods: ['GET'])]
#[Route('/home', methods: ['GET'])]
#[Route('/default', methods: ['GET'])]
#[Route('/dashboard', methods: ['GET', 'POST'])]
public function home(): int 
{
   // ...
}
```

**When to use:**

* When some URLs need different HTTP methods or other custom settings.

---

### Using Aliases

If all the URLs behave the same, you can keep things cleaner by defining them in one attribute using the `$aliases` property.

```php
#[Route('/', methods: ['GET'], aliases: [
   '/home',
   '/default',
   '/dashboard'
])]
public function home(): int 
{
   // ...
}
```

**When to use:**

* When every URL should share the exact same configuration as the main route.
* To avoid repeating the same attribute multiple times.

> **Note:**
> All aliases inherit the same methods and settings as the main route (`/` in this example).

***

## Attribute Commands

PHP **attributes** make defining routes more easier and maintainable. However, you may wonder: *Do attributes perform better than traditional routing methods using context configurations in `public/index.php`?*

Luminova provides a **command to convert attribute-based routes into router-based configurations**. This allows you to switch routing methods without losing your existing route definitions.

---

### Export Command

The `export` command extracts all route definitions defined via attributes for **CLI**, **WEB**, and **API** contexts. It generates the necessary route files and contexts for the router and automatically updates the environment variable:

```ini
feature.route.attributes = disabled
```

> This disables runtime attribute routing and switches to a router-based system, while still keeping your original definitions intact.

---

### Performance Comparison

You can compare **attributes vs router methods** by enabling performance profiling:

```ini
debug.show.performance.profiling = true
```

Metrics such as:

* Execution Time
* Memory Usage
* Files Loaded

will be displayed, helping you make an informed decision about which method to use in production.

---

### Commands

Run the export command from your project root:

```bash
php novakit context --export-attr
```

> **Tip:** After exporting, you can customize the generated router configuration.
> For example, you can split routes by prefixes or add more context prefixes in `public/index.php` to improve performance further.

---

### Clearing Cached Attributes

If attribute caching is enabled, changes or new attributes may not take effect because the cache is stored permanently. To clear the cache:

* Manually delete cached PHP files from `/writeable/caches/routes/`
* Or use the Novakit command:

```bash
php novakit context --clear-attr
```

This ensures that your latest attribute definitions are applied on the next request.

---

## Class Definition

* Class namespace: `Luminova\Attributes\Route`
* This class is marked as **final** and can't be subclassed

***

## Constants

These constants define **middleware execution types** for HTTP routes and CLI commands in **Luminova**:

| **Name**                 | **Value** | **Description**                                               | **Typical Use Case**                                 |
| ------------------------ | --------- | ------------------------------------------------------------- | ---------------------------------------------------- |
| HTTP_BEFORE_MIDDLEWARE | `before`  | Runs **before** the main controller logic.                    | Request validation, authentication, input filtering. |
| HTTP_AFTER_MIDDLEWARE  | `after`   | Runs **after** the main controller logic.                     | Logging, cleanup, modifying response headers.        |
| CLI_GLOBAL_MIDDLEWARE  | `global`  | Applies to **all CLI commands**, regardless of command group. | Global CLI security or environment checks.           |
| CLI_GROUP_MIDDLEWARE   | `guard`   | Runs **before commands** in the same CLI group.               | Group-specific access control or setup.              |

***

### constructor

Defines a repeatable attribute for registering **HTTP** or **CLI** routes.

This attribute links controller methods to specific URI patterns (HTTP) or command patterns (CLI). You can also attach middleware and define error handlers for HTTP routes.

```php
public __construct(
   string $pattern = '/', 
   array $methods = ['GET'], 
   bool $error = false, 
   ?string $group = null, 
   ?string $middleware = null,
   ?array $aliases = null
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route pattern (e.g., `/blog/(:int)`, `/blogs/{$id}` or `/blog/(\d+)`). |
| `$methods` | **array** | HTTP methods this route responds to, Use ['ANY'] to match all methods (default: ['GET']). |
| `$error` | **bool** | Whether this route is an HTTP error handler (for: `HTTP` only). |
| `$group` | **string&#124;null** | CLI command group this route belongs to (for: `CLI` only). |
| `$middleware` | **string&#124;null** | Optional middleware assignment:<br/>- For `HTTP`: `Route::HTTP_BEFORE_MIDDLEWARE` or `Route::CLI_GROUP_MIDDLEWARE`.<br/>- For `CLI`: `Route::CLI_GROUP_MIDDLEWARE` (global) or `Route::` (group-specific). |
| `$aliases` | **array<int,string>&#124;null** | Optional list of alternative URI patterns that map to the same route (e.g. `['/blog/(:int)', '/blog/id/(:int)']`). |

**Throw:**

- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - If the middleware is invalid or unsupported.

---

## Properties Description

### pattern

Defines the **URL or command pattern** that the route should match.
This is how the router knows which request or command to handle.

**Examples:**

* **HTTP Route:** `'/users/([0-9]+)'` — Matches URLs like `https://example.com/users/123`.
* **CLI Route:** `'users/limit/(:int)'` — Matches CLI commands like `php index.php group-name users limit=50`.

---

### methods

Specifies the **HTTP methods** that the route should respond to (e.g., `GET`, `POST`, `PUT`).
Useful for **RESTful APIs** to restrict actions based on request type.

**Example:**
`methods: ['GET', 'POST']` — The route only handles GET and POST requests.

---

### error

Indicates whether the route is an HTTP **error handler**.
Error routes catch and handle `404` errors for the specified HTTP methods.

**Example:**
`error: true` — This route will handle requests that do not match any other route.

---

### group

Defines the **command group name** for CLI routes.
Grouping commands helps organize related commands and acts as the **base command** for other sub-commands.

**Example:**
`group: 'user'` — CLI command `php index.php user add` will match a route under this group.

---

### middleware

Specifies **middleware** to apply to the route.
Middleware can execute code **before or after** the main route handler, useful for authentication, logging, or setup tasks.

**Examples:**

* `middleware: Route::HTTP_BEFORE_MIDDLEWARE` — Runs before the main route handler (HTTP pre-processing).
* `middleware: Route::CLI_GROUP_MIDDLEWARE` — Runs for all CLI commands globally.

---

### aliases

Defines **alternative URI patterns** that map to the same route as the main pattern.
All aliases share the **same HTTP methods, middleware, and error handler** as the main route.
This helps avoid repeating multiple `Route` attributes for identical behavior.

**Example:**

```php
#[Route(pattern: '/', aliases: ['/home', '/default', '/dashboard'], methods: ['GET'])]
```
> All of these URLs will respond exactly like the main route `/`.
