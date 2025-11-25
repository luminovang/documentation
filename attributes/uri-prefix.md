# Prefix Attribute for HTTP Controllers

***

## Overview

Luminova’s Prefix attribute allows you to group URI prefixes in the controller class level for routing and optionally assign an error handler or exclude prefixes for routing performance. 

***

## Introduction

The **Prefix** attribute in Luminova allows you to define a URI prefix or pattern directly on a controller class.
It’s a simple way to group related routes, similar to defining them on `/public/index.php`  `context` method and grouping routes in `/routes/web.php` file.

Using a prefix keeps your routes organized **and** improves performance.
The router knows exactly which controller should handle a request, so it resolves routes faster during attribute scanning and caching.

> **Note:**
> A controller prefix name (like `/api`) doesn’t automatically apply to every `Route` attributes in class methods.
> You must still include the prefix when defining individual route methods.

**Prefix supports:**

* Static paths such as `/api` or `/panel`
* Regular expressions for advanced matching
* [Dynamic placeholders](/routing/dynamic-uri-placeholder.md) for flexible URI patterns (e.g. `(:root)`, `(:base)`)

When attribute routing is enabled, `Prefix` provides a cleaner and faster alternative to [method-based routing](/routing/uri-prefix.md).
Think of it as giving your controller a street address, each method still has its own number, but they all live on the same street.

---

## Examples

The following examples show how to use the `Prefix` attribute to define a URI pattern or placeholder that helps organize your route handling.

---

### Prefixing Single Controller Application

In this example, assume a single controller handles all application routing.

The controller will handle **all standard website routes**, except those starting with `/api`.
This keeps your web pages and API routes cleanly separated.

```php
// /app/Controllers/Http/AppController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/(?!api).*')]
class AppController extends Controller
{
   // Handles all routes except those starting with /api
}
```

---

### Prefixing API Routes

In this example, we define a prefix for all API routes so that they are grouped under a specific controller.

The `ResetController` automatically handles every URI starting with `/api/...`.

```php
// /app/Controllers/Http/ResetController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/api/(:base)')]
class ResetController extends Controller
{
   // Handles all routes that begin with /api
}
```

---

### Advanced Prefix Exclusion

Luminova allows URI prefix exclusions in **two ways**, at the **routing system level** and the **attribute parsing level**.
Both approaches help prevent overlapping routes, improve performance, and avoid unexpected matches.

---

#### Attribute Parsing Level

At this level, you can define a URI prefix for a controller and specify which prefixes to exclude during attribute parsing.
This makes controller mapping faster and more predictable since excluded prefixes are ignored early in the routing process.

**Examples:**

```php
// /app/Controllers/Http/MainController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;

#[Prefix('/', exclude: ['api', 'blog', 'admin'])]
class MainController extends Controller
{
   // Handles all website routes except those starting with /api, /blog, or /admin
}
```

```php
// /app/Controllers/Http/BlogController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;

#[Prefix('/blog/(:base)', exclude: ['api', 'admin'])]
class BlogController extends Controller
{
   // Handles all /blog routes except those starting with /api or /admin
}
```

---

#### Routing Level

At the routing level, you can define a **custom regex pattern** that excludes certain prefixes during both
attribute parsing and route resolution. This ensures stricter validation and routing performance.

**Example:**

```php
// /app/Controllers/Http/MainController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/(?!api(?:/|$)|blog(?:/|$)|admin(?:/|$)).*')]
class MainController extends Controller
{
    // Handles all website routes except those starting with /api, /blog, or /admin
}
```

---

> **Note:**
> Avoid combining both `pattern` exclusions and `exclude` arrays in the same attribute.
> Doing so may lead to conflicting route matches or unexpected behavior.

---

### Using Pattern Placeholder

You can use a **pattern placeholder helper** to manage complex or frequently used regex patterns more cleanly.
This makes your routes easier to maintain and keeps your controller attributes readable.

**Defining a Placeholder Pattern**

```php
// /app/Application.php

namespace App;

use Luminova\Routing\Router;
use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication
{
   protected function onCreate(): void
   {
      // Define a placeholder pattern for all routes that are not API, blog, or admin
      Router::pattern('home', '(?!api(?:/|$)|blog(?:/|$)|admin(?:/|$)).*');
   }
}
```

**Applying the Placeholder Pattern**

Now apply your custom `home` pattern in the main controller:

```php
// /app/Controllers/Http/MainController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/(:home)')]
class MainController extends Controller
{
    // Handles all routes except those starting with /api, /blog, or /admin
}
```

---

### Prefix with Error Handler

You can attach a **custom error handler** directly to a prefix.
This is useful when you want all routes under a specific controller to share the same fallback logic if something goes wrong.

```php
// /app/Controllers/Http/AdminController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

#[Prefix(
    pattern: '/admin/(:base)',
    onError: [ErrorController::class, 'onAdminError']
)]
class AdminController extends Controller
{
    // All /admin/... routes are handled here.
    // If a route fails, onAdminError() is triggered automatically.
}
```

The prefix `/admin/(:base)` ensures this controller handles all routes under the `/admin` path.
If any of those routes fail (for example, the method or URI doesn’t exist), the `onAdminError()` method in `ErrorController` is called automatically.

---

## Class Definition

* Class namespace: `Luminova\Attributes\Prefix`
* This class is marked as **final** and can't be subclassed

---

## Attribute Constructor

Defines a non-repeatable routing prefix for HTTP controller classes.

This attribute assigns a URI prefix to a controller and optionally sets an error handler. It helps centralize error management and organize controllers when compiling attributes to routes for performance. 

```php
public __construct(string $pattern, ?callable $onError = null, array $exclude = [], bool $margeExcluders = false)
```

### Parameters:

| Parameter   | Type                        | Description                                                                 |
|-------------|-----------------------------|-----------------------------------------------------------------------------|
| `$pattern`  | **string**                  | The URI prefix or pattern controller handles (e.g., `/user/(:base)`, `/user` or `/user/?.*`). |
| `$onError`  | **callable\|null** | Optional error handler for routing errors.<br/>Can be a callable or a (e.g, `[class, method]`) array.      |
| `$exclude`  | **array** | An optional list of URI prefixes to exclude from class matching.<br/>This is used internally when parsing attributes routing performance.     |
| `$margeExcluders`  | **bool** | Wether to marge the exclude list with based prefix or pattern (default: `false`). |

**Throws**

- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - If the provided error handler is not a valid callable.

> **Note:** 
> Only one `Prefix` attribute can be be assigned to a controller.
> And you can optionally define error handler without needing `Error` attribute class.