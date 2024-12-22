# URI Prefix Attribute for Request Routing

***

## Overview

The base application controller lays the foundation for handling your software development logic, by serving as the base application class other services may rely on.

***

## Introduction

The **Prefix** attribute in the Luminova framework allows for defining a URI or URI pattern at the class level, determining which URIs a controller can handle. This routing mechanism supports regular expressions and predefined placeholders, enabling flexible matching of incoming requests. This approach is an alternative to [Manual Route Prefixing Context for URI Handling](/routing/uri-prefix.md) when attributes are enabled, allowing for cleaner and more dynamic route management.

---

### Adding Prefix Attributes

The following examples demonstrate how to use the `Prefix` attribute to define a URI pattern or placeholder for handling routes.

#### Basic Example:

```php
// /app/Controllers/Http/AdminController.php
<?php
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/page/admin')]
class AdminController extends BaseController
{
    // Class methods handling /page/admin routes
}
```

In this example, the `AdminController` handles all routes under `/page/admin`.

***

#### Example with Placeholders:

```php
// /app/Controllers/Http/Controller.php
<?php
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/page/admin/(:string)/(:root)')]
class Controller extends BaseController
{
    // Class methods handling dynamic segments in the URL
}
```

Here, the controller is equipped to handle URIs like `/page/admin/{string}/{root}`, where `{string}` and `{root}` are dynamic placeholders.

***

#### Example with Custom Error Handling:

```php
// /app/Controllers/Http/Controller.php
<?php
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Prefix;
use App\Controllers\Errors\ViewErrors;

#[Prefix(pattern: '/(:root)', onError: [ViewErrors::class, 'onRestError'])]
class Controller extends BaseController
{
    // Handles errors using custom ViewErrors handler
}
```

In this case, a custom error handler (`onRestError`) is applied to handle routing errors for this controller, which handles any routes under the root (`/`).

---

## Class Definition

* Class namespace: `\Luminova\Attributes\Prefix`
* This class is marked as **final** and can't be subclassed

---

## Attribute Constructor

The `Prefix` attribute constructor allows you to specify a URI pattern for the controller and optionally define an error handler.

```php
public __construct(string $pattern, \Closure|array|null $onError = null)
```

### Parameters:

| Parameter   | Type                        | Description                                                                 |
|-------------|-----------------------------|-----------------------------------------------------------------------------|
| `$pattern`  | **string**                   | The URI pattern the controller should handle (e.g., `/user/account`, `/(:root)`). |
| `$onError`  | **\Closure&#124;array&#124;null** | Optional error handler (Closure or array for class/method error handling).     |

> **Note:** Only one `Prefix` attribute can be applied per controller class.

---

###  Optimized Controller URI Prefix Handling

Using the `Prefix` attribute can help organize your controllers by associating them with specific URIs. However, if not properly configured, this can lead to unintended behavior in production when attribute caching is enabled. Specifically, you may end up generating cache versions of all root URI prefixes that include non-existent URI prefix, which is not the desired result.

#### Problem Overview

Let’s assume you have three custom controllers:
- The **MainController** handles root URIs (e.g., `https://example.com`, `https://example.com/*`, `https://example.com/foo`).
- The **BlogController** handles blog URIs (e.g., `https://example.com/blog`, `https://example.com/blog/*`).
- The **RestController** handles API URI Endpoints (e.g., `https://example.com/api`, `https://example.com/api/*`).

---

### Defining Prefix Patterns for Each Controller

To prevent the root controller from handling unintended prefixes, such as `blog` and `api`, you need to define the correct prefix patterns. Below are example patterns for each controller.

#### MainController

**Ignore `blog` and `api` Prefixes**

In the root controller, follow the example prefix pattern to exclude the `blog` and `api` prefixes:

```php
// /app/Controllers/Http/MainController.php
<?php
namespace App\Controllers\Http;

use Luminova\Base\BaseViewController;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/(?!blog|api).*')]
class MainController extends BaseViewController 
{
    // Your main controller methods
}
```

#### BlogController

**Accept Only the `blog` Prefix**

For the blog controller, define prefix pattern to only match URIs that start with `blog`:

```php
// /app/Controllers/Http/BlogController.php
<?php
namespace App\Controllers\Http;

use Luminova\Base\BaseViewController;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/blog/(:root)')]
class BlogController extends BaseViewController 
{
    // Your blog controller methods
}
```

#### RestController

**Accept Only the `api` Prefix**

For the API controller, restrict the URI pattern to match only `api` paths:

```php
// /app/Controllers/Http/RestController.php
<?php
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/api/(:root)')]
class RestController extends BaseController 
{
    // Your REST API controller methods
}
```

> By defining clear and specific prefix patterns for each controller, you ensure that:
> - **MainController** handles only root URIs while ignoring `blog` and `api`.
> - **BlogController** manages all blog-related URIs.
> - **RestController** deals solely with API requests.