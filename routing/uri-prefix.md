# Method URI Prefixing for Route Context Files

***

## Overview

The Method-Based URI prefix makes it easier to manage and optimize the routing system for request handling when using low-level method route definitions instead of attributes.

***

## Introduction

The **Method-Based URI Prefix** in Luminova helps you organize and speed up your routing system when defining routes directly in code instead of using attributes.

You typically set this up in `/public/index.php` using the router’s `context` method. It groups routes by URI prefix so that only the necessary routes are loaded for a given request, reducing overhead and improving performance.

This approach keeps your codebase cleaner by separating concerns. Each context clearly shows its URI prefix and error handler, making the routing setup easier to read and understand at a glance.

---

### Why Use Prefix

Think of route prefixes like labels on storage boxes. Instead of throwing all your stuff in one big box and digging through it every time, you put “Web,” “API,” or “CLI” on the boxes and know exactly where things live.

Without prefixes, every route sits in the same pile. That works for small projects — until it doesn’t. As your app grows, you’ll have HTTP routes mixed with admin routes, API endpoints, maybe even CLI commands. Good luck finding what’s what when you come back six months later.

With prefixes:

* You only write `/api` or `/admin` once, instead of repeating it on every single route.
* Your routes are naturally grouped — “all the web pages here, all the API calls there.”
* Changing a group later is way easier — update one prefix, done.

And it’s not just about staying organized — bad route structure can actually slow things down. Without prefixes, the routing system has to scan *every single route* to find a match. Even worse, a sloppy pattern can accidentally match the wrong route, leading to bugs that make you question reality (or at least your coffee intake).

> So, do you *have* to use prefixes? No. But do they save you from future headaches? Absolutely. 
> It’s like labeling those boxes now so you’re not unpacking your garage every time you need a screwdriver.

***

## Prefix Usages

You can define routing prefixes in **two ways**: using the `Prefix` class or using an **array configuration**.
Prefixes are registered in `/public/index.php` using the router’s `context()` method.

> **Notes:**
> * The prefix name **must match the file name** in `/routes/` for routing to work correctly.
> * Your error handler (e.g., `onApiError`) must exist in the specified class (`ErrorController` or any custom error controller).

---

### Using the Prefix Class

With a `Prefix` object, all URLs starting with the given prefix are automatically routed to the matching file in `/routes/`.
For example, requests beginning with `/api` will load `/routes/api.php`.

The `Prefix` class takes two parameters:

* **$prefix** (`string`) – The URI prefix name.
* **$onError** (`\Closure|array\<int,string>|null`) – A handler for 404 errors in this context.

```php
// /public/index.php

use Luminova\Boot;
use Luminova\Routing\Prefix;
use App\Errors\Controllers\ErrorController;

Boot::http()->router->context(
   new Prefix(Prefix::WEB, [ErrorController::class, 'onApiError']),
   new Prefix(Prefix::API, [ErrorController::class, 'onApiError']),
   new Prefix(Prefix::CLI),
   // ...
)->run();
```

---

### Using Array Configuration

You can also define prefixes as associative arrays.
This approach avoids initializing `Prefix` objects at runtime, which can be more efficient when you have many route contexts.

Each prefix must define:

* `prefix` (string) – The URI prefix name.
* `error` (Closure|array|null) – Optional 404 error handler.

```php
// /public/index.php

use Luminova\Boot;
use App\Errors\Controllers\ErrorController;

Boot::http()->router->context(
   [
      'prefix' => 'web',
      'error'  => [ErrorController::class, 'onWebError']
   ],
   [
      'prefix' => 'api',
      'error'  => [ErrorController::class, 'onApiError']
   ],
   [
      'prefix' => 'cli'
   ],
   // ...
)->run();
```

> **Tip:** 
> Use `Prefix` objects if you want clear type-safety and IDE support. Use arrays when optimizing for performance with many prefixes.

***

### Prefix Context File

After adding a new route prefix in `public/index.php`, you need to create a matching handler file in the `/routes/` directory. The file name **must match the prefix name**.

For example, if you register the prefix `admin`, create `/routes/admin.php`:

```php
// /routes/admin.php

$router->get('/', 'AdminController::login');
$router->get('/dashboard', 'AdminController::dashboard');
```

Now, any URL starting with `/admin` (e.g.,
`https://example.com/admin`,
`https://example.com/admin/foo`, or
`https://localhost/example.com/public/admin`)
will automatically load routes from `admin.php`.

***

## Class Definition

* Class namespace: `Luminova\Routing\Prefix`
* This class is marked as **final** and can't be subclassed

***

## Constants

### Predefined Prefixes

The framework reserves certain URI prefix names for common use cases. You can still define your own, but these built-ins cover the most frequent scenarios:

| Constant  | Value     | Reserved | Description                                          |
| :-------- | :-------- | :------- | :--------------------------------------------------- |
| `WEB`     | `web`     | Yes      | Default prefix for standard HTTP request URIs.       |
| `CLI`     | `cli`     | Yes      | Default prefix for all CLI commands.                 |
| `API`     | `api`     | No       | Suggested custom prefix for API routes (`/api`).     |
| `PANEL`   | `panel`   | No       | Suggested custom prefix for control panel routes.    |
| `ADMIN`   | `admin`   | No       | Suggested custom prefix for admin routes (`/admin`). |
| `CONSOLE` | `console` | No       | Suggested custom prefix for console routes.          |
| `WEBHOOK` | `webhook` | No       | Suggested custom prefix for webhook endpoints.       |

---

#### WEB Prefix

The `WEB` prefix is **reserved** and cannot be renamed.
It is automatically applied to all HTTP request URIs that don't explicitly use another prefix (for example, `https://example.com/` or other public URLs). The associated route context file is always `/routes/web.php`, and this filename cannot be changed.

---

#### CLI Prefix

The `CLI` prefix is also **reserved** and cannot be renamed.
It is used for handling all command-line routes, and the route context file is always `/routes/cli.php`.

***

## Methods

### constructor

Initialize constructor to register a router prefix.

```php
public __construct(string $prefix, \Closure|array|null $onError = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prefix` | **string** | The route URL prefix name to handle (e.g, `blog`). |
| `$onError` | **\Closure&#124;array<int,string>&#124;null** | Optional prefix context error handler. |

> Error handler supports any callable, or list array with two elements, the first element should be class name while the second is method name.