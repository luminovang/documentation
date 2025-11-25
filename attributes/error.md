# Error Attributes for HTTP Controllers

***

## Overview

Defines class-level error handlers for HTTP applications, allowing flexible error routing by context, URI pattern, and handler method.

***

## Introduction

The **Error** attribute in Luminova provides a simple and organized way to define error handlers for `HTTP` applications.  
By using attributes, you can keep your routing logic and error handling closely tied to your controller classes, improving readability and maintainability.

The `Error` attribute allows you to assign a global error handler for HTTP errors, such as **404 Not Found**, or for manually triggered errors using `$this->app->router->trigger()`.  
It should be declared at the **class level**, before the class body begins.

***

## Examples

### Single Handler

This example will call the `onWebError` method whenever a `404` error occurs if no other error context is defined to handle the prefix error.

```php
// app/controllers/Http/WebController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Errors\Controllers\ErrorController;

#[Error('web', '/(:root)', [ErrorController::class, 'onWebError'])]
class WebController extends Controller
{
   #[Route('/', methods: ['GET'])]
   public function index(): void
   {
      // Your code here
   }
}
```

> **In this example:**
> - The `WebController` uses the `Error` attribute to define a fallback error handler for the `web` context.
> - If a route under `/` or its subpaths encounters an error, it delegates handling to `ErrorController::onWebError()`.

***

### Multiple Handlers

You can define different error handlers for different prefixes by defining multiple contexts.

```php
// /app/controllers/Http/WebController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Controllers\Errors\ErrorController;

#[Error('admin', '/(?!dashboard/).*', [ErrorController::class, 'onWebError'])]
#[Error('admin', '/dashboard/(:root)', [ErrorController::class, 'onDashboardError'])]
class WebController extends Controller
{
   #[Route('/admin', methods: ['GET'])]
   public function home(): void
   {
      // Your code here
   }

   #[Route('/admin/dashboard', methods: ['GET'])]
   public function dashboard(): void
   {
      // Your code here
   }
}
```

> **In the above example:**
>
> - The first `Error` attribute `'/ (?!dashboard/).*'` matches all routes **except** those that begin with `/dashboard/` This utilizes a negative lookahead assertion `(?!dashboard/)` to exclude any URL that starts with `/dashboard/`.
> - The second `Error` attribute `'/dashboard/(:root)'` matches any routes that start with `/dashboard`, including both `/dashboard` and `/dashboard/`. The `(:root)` placeholder allows for the capture of any additional segments after `/dashboard`, including the possibility of matching nothing (an empty string).

***

### Web URI Prefixing

Don’t be confused by the use of the context name **web**.  
Using `web` as the context name does **not** mean that a URLs must start with `/web`, before handled by the `onWebError` method.

The **web** prefix name is a **generic context** that tells the routing system to apply the error handler to **any URL** if no other more custom error handler prefix **(based on context and prefix)** is defined or matched.

**Example:**

```php
#[Error('web', '/(:root)', [ErrorController::class, 'onWebError'])]
```

- `https://example.com/account/page`: Handled by the `web` error handler (`onWebError`).
- `https://example.com/web/page`: Handled by the `web` error handler (`onWebError`).
- `https://example.com/panel/page`: Handled by the `web` error handler (`onWebError`), **if no `panel` context is defined**.

**Custom Prefix**

```php
#[Error('panel', '/(:root)', [ErrorController::class, 'onPanelError'])]
```

- `https://example.com/panel/page`: Handled by the `panel` error handler (`onPanelError`) because it matches the explicitly defined `panel` context.

***

## Class Definition

* Class namespace: `Luminova\Attributes\Error`
* This class is marked as **final** and can't be subclassed

***

### Error Attribute Constructor

Defines a repeatable attribute for handling global HTTP routing errors.

This attribute allows you assign an error handler to a specific **URI** or **URI pattern** within a given context. It support multiple error handlers for different URI prefixes or patterns in the same controller, giving fine-grained control over routing error management.

```php
public __construct(string $context = 'web', string $pattern = '/', string|array|null $onError = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string** | The URI prefix context name used to categorize URIs (default: `web`).<br/>Typically the first segment of the URI (e.g., `api`, `blog`). |
| `$pattern` | **string** | The route URI pattern to allow this  error handler (default: `/` root).<br/>(e.g., `/blog/(:int)`, `/blogs/{$id}`, `/blog/(\d+)`, `/` or `/.*`). |
| `$onError` | **string\|array\|null** | A callable error handler, provided as a string or `[class, method]` array.<br/>Invoked when the specified pattern matches. |

**Throw:**

- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - If the provided error handler is not callable or `null` was provided.

See reference for [Dynamic URI Segment Placeholders](/routing/dynamic-uri-placeholder.md) documentation.