# URI Prefix Handling Attribute for HTTP Controller Classes

***

## Overview

Defines a URI prefix or pattern at the HTTP controller class level for flexible and organized routing based on static paths or placeholders.

***

## Introduction

The **Prefix** attribute in Luminova allows you to define a URI prefix or URI pattern at the HTTP controller class level, specifying which URIs the controller can handle.  

The `Prefix` attribute supports regular expressions, static prefixes, or [predefined URI pattern placeholders](/routing/prefix-attribute.md), enabling flexible and powerful route matching.

Using attributes is an alternative to [Method-Based Routing](/routing/uri-prefix.md) when attribute routing is enabled, allowing for cleaner and more dynamic route management.

---

### Using the Prefix Attribute

The following example shows how to use the `Prefix` attribute to define a URI pattern or placeholder for handling routes.

**Basic Example**

```php
// /app/Controllers/Http/AdminController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/page/admin')]
class AdminController extends BaseController
{
   // Class methods handling /page/admin routes
}
```

In this example, the `AdminController` handles all HTTP routes that start with `/page/admin`.

***

#### Example with Placeholders:

```php
// /app/Controllers/Http/Controller.php

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

namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/(:root)', onError: [ErrorController::class, 'onRestError'])]
class Controller extends BaseController
{
   // Handles errors using custom ErrorController handler
}
```

In this case, a custom error handler (`onRestError`) is applied to handle routing errors for this controller, which handles any routes under the root (`/`).

---

## Class Definition

* Class namespace: `\Luminova\Attributes\Prefix`
* This class is marked as **final** and can't be subclassed

---

## Attribute Constructor

Defines a non-repeatable routing prefix for `HTTP` controller classes, including URI prefix pattern and HTTP error handling.

This attribute assigns a `URI` prefix and an optional error handler to a controller class, enabling centralized routing and error management.

```php
public __construct(string $pattern, string|array|null $onError = null)
```

### Parameters:

| Parameter   | Type                        | Description                                                                 |
|-------------|-----------------------------|-----------------------------------------------------------------------------|
| `$pattern`  | **string**                   | The URI prefix or pattern that the controller should handle (e.g., `/user/account`, `/(:root)`). |
| `$onError`  | **callable\|null** | An optional error handler, either as a callable or a [class, method] array, for handling routing errors.     |

**Throws**

- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - If the provided error handler is not a valid callable.

> **Note:** Only one `Prefix` attribute can be be assigned to a controller.

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