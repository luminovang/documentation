# Error Handling Attribute for HTTP Controller Classes

***

## Overview

Defines class-level error handlers for HTTP applications, allowing flexible error routing by context, URI pattern, and handler method.

***

## Introduction

The `Error` attribute in Luminova provides a simple and organized way to define error handlers for `HTTP` applications.  
By using attributes, you can keep your routing logic and error handling closely tied to your controller classes, improving readability and maintainability.

The `Error` attribute allows you to assign a global error handler for HTTP errors, such as **404 Not Found**, or for manually triggered errors using `$this->app->router->triggerError()`.  
It should be declared at the **class level**, before the class body begins.

***

### Usage Example

```php
// /app/Controllers/Http/RequestController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Errors\Controllers\ErrorController;

#[Error('web', '/(:root)', [ErrorController::class, 'onWebError'])]
class RequestController extends BaseController
{
   // Your controller methods
}
```

In this example:
- The `RequestController` uses the `Error` attribute to define a fallback error handler for the `web` context.
- If a route under `/` or its subpaths encounters an error, it delegates handling to `ErrorController::onWebError()`.

***

**Prefix Examples:**

- `http://example.com/` - This URL has no prefix and will match a pattern like `/`.
- `http://example.com/foo` - This URL has the prefix `foo` and will match a pattern like `/foo`.
- `http://example.com/account/main/users/dashboard` - This URL has the prefix `account` and will match a pattern like `/account/.*`.

***

### Understanding URI Context and Prefix

Don’t be confused by the use of the context name `web`.  
Using `web` as the context name does **not** mean that only URLs starting with `/web` are handled by the `onWebError` method.

Instead, `web` is a **generic context** that tells Luminova to apply the error handler to **any URL** if no other more specific error handler (based on context and prefix) is defined or matched.

**Examples:**

**Examples:**

```php
#[Error('web', '/(:root)', [ErrorController::class, 'onWebError'])]
```

- `https://example.com/account/page`: Handled by the `web` error handler (`onWebError`).
- `https://example.com/web/page`: Handled by the `web` error handler (`onWebError`).
- `https://example.com/panel/page`: Handled by the `web` error handler (`onWebError`), **if no `panel` context is defined**.

---

```php
#[Error('panel', '/(:root)', [ErrorController::class, 'onPanelError'])]
```

- `https://example.com/panel/page`: Handled by the `panel` error handler (`onPanelError`) because it matches the explicitly defined `panel` context.

***

## Class Definition

* Class namespace: `\Luminova\Attributes\Error`
* This class is marked as **final** and can't be subclassed

***

### Error Attribute Constructor

Defines a repeatable attribute for handling global HTTP route errors.

This attribute assigns an error handler to a specific `URI` pattern within a given context, allowing fine-grained control over how routing errors are managed. Multiple error handlers can be defined for different URI prefix-contexts and patterns within the same controller.

```php
public __construct(string $context = 'web', string $pattern = '/', string|array|null $onError = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string** | The routing context used to categorize the URI (default: `web`).<br/>Typically, this is the first segment of the URI (e.g., `api`, `blog`). |
| `$pattern` | **string** | The route pattern to match for error handling (e.g., `/`, `/.*`, `/blog/([0-9-.]+)`, `/blog/(:placeholder)`). |
| `$onError` | **callable\|null** | A optional callable error handler, either as a string or a [class, method] array. |

**Throw:**

- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - If the provided error handler is not callable.

***

### Usage Examples

You can use the `Error` attribute to specify error handling for different parts of your application.

**Global Handler**

This example will call the `onWebError` method whenever a `404` error occurs if no other error context is defined to handle the prefix error.

```php
// app/controllers/Http/WebController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseViewController;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Errors\Controllers\ErrorController;

#[Error('web', '/(:root)', [ErrorController::class, 'onWebError'])]
class WebController extends BaseViewController
{
   #[Route('/', methods: ['GET'])]
   public function index(): void
   {
      // Your code here
   }
}
```

**Multiple Handlers**

You can define different error handlers for different prefixes by defining multiple contexts.

```php
// /app/controllers/Http/WebController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseViewController;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Controllers\Errors\WebErrors;

#[Error('admin', '/(?!dashboard/).*', [WebErrors::class, 'main'])]
#[Error('admin', '/dashboard/(:root)', [WebErrors::class, 'dashboard'])]
class WebController extends BaseViewController
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

**In the above setup:**

- The first `Error` attribute `'/ (?!dashboard/).*'` matches all routes **except** those that begin with `/dashboard/`. This utilizes a negative lookahead assertion `(?!dashboard/)` to exclude any URL that starts with `/dashboard/`.

- The second `Error` attribute `'/dashboard/(:root)'` matches any routes that start with `/dashboard`, including both `/dashboard` and `/dashboard/`. The `(:root)` placeholder allows for the capture of any additional segments after `/dashboard`, including the possibility of matching nothing (an empty string).