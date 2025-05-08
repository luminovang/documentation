# Global Error Handling and Custom Error Controllers

***

## Overview

Implement custom error controllers to override default error handlers for web and API routes, allowing context or class-specific error views and responses.

***

## Introduction

The `ErrorController` class provides the default handling for HTTP error responses.  
It allows you to define and manage error-handling methods, customizing how errors are displayed based on URI prefixes.

Error handler methods are responsible for:

- Handling `404` errors when a view is not found during rendering.
- Handling manually triggered errors when calling `$this->app->router->triggerError()` from within a controller method.

This gives you full control over displaying meaningful and user-friendly error messages.

> **Important Notes:**
> - Error handler methods can be **static** or **non-static**.
> - Methods must be declared as **public** — not `protected` or `private`.
> - **Dependency injection** is fully supported, allowing you to automatically pass any needed classes into your handler method’s parameters.

***

## Class Definition

* Class namespace: `\App\Errors\Controllers\ErrorController`
* This class implements:
[\Luminova\Interface\RoutableInterface](/interface/classes.md#routableinterface), [\Luminova\Interface\ErrorHandlerInterface](/interface/classes.md#errorhandlerinterface)

***

## Methods

### onWebError

Handles errors for the global HTTP **Web** route context.

```php
public static onWebError(Application $app): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app`    | **\App\Application** | The current application instance. |

**Return Value:**

`int` - Return response status code either `STSTUS_SUCCESS` or `STATUS_SILENCE`. 

***

### onApiError

Handles errors for the HTTP **API** route context.

```php
public static onApiError(Application $app): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app`    | **\App\Application** | The current application instance. |

**Return Value:**

`int` - Return response status code either `STSTUS_SUCCESS` or `STATUS_SILENCE`. 

> The `API` context is determined by checking if the request URL's first segment matches the API prefix (e.g., `https://example.com/api/...`).

***

## Custom Implemenation

You can define custom error handlers by either:

- Adding new methods inside your existing `App\Errors\Controllers\ErrorController` class,  
- Or creating a new class that extends `BaseController` and implements `ErrorHandlerInterface`.

---

### Custom Class

Creating a controller class for a custom error handling.

```php
// /app/Controllers/Errors/CustomFooErrors.php

namespace App\Controllers\Errors;

use Luminova\Base\BaseController;
use App\Application;
use Luminova\Time\Time;
use Luminova\Interface\ErrorHandlerInterface;

class CustomFooErrors extends BaseController implements ErrorHandlerInterface
{
    /**
     * Custom error handling method.
     *
     * @param Application $app The current application instance.
     * 
     * @return int Must return a response status code (e.g., STATUS_SILENCE or STATUS_SUCCESS).
     */
    public static function onFooError(Application $app): int 
    {
        // Implement your custom error response
        return STATUS_SILENCE;
    }
}
```

> **Note:** Your methods must return a response status code (e.g., `STATUS_SILENCE` or `STATUS_SUCCESS`).
> Also must allow passing of the current application instance as argument.

***

### Registering Error Handlers

You can register error handlers either using **attribute-based routing** or **context-based routing**.  
Error handlers are registered by providing a callable:

- **Closure:** Example: `fn(Application $app): int => CustomFooErrors::onFooError($app)`.
- **Callable Array:** Example: `[CustomFooErrors::class, 'onFooError']`.

---

#### Context-Based Handling

For non-attribute routing, register your error handler manually in `public/index.php`.  
The handler must be a **callable** or a **[class, method] array**, where the class handles the error and the method defines the logic.

**Using `Prefix` object for URI prefixing:**

```php
// /public/index.php

use Luminova\Routing\Prefix;
use App\Controllers\Http\Errors\CustomFooErrors;

Boot::http()->router->context(
    new Prefix(
        'foo', /* URI prefix where this error handler applies */
        [CustomFooErrors::class, 'onFooError'] /* Error handler */
    )
)->run();
```

**Using `Array` for URI prefixing:**

```php
// /public/index.php

use App\Controllers\Http\Errors\CustomFooErrors;

Boot::http()->router->context(
    [
        'prefix' => 'foo',
        'error' => [CustomFooErrors::class, 'onFooError']
    ]
)->run();
```

---

#### Attribute-Based Handling

You can also define error handlers directly within controllers using attributes.

**Using non-repeatable `Prefix` route attribute:**

```php
// /app/Controllers/Http/FooController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Prefix;
use App\Controllers\Http\Errors\CustomFooErrors;

#[Prefix(
    pattern: '/foo/(:root)', /* URI prefix pattern */
    onError: [CustomFooErrors::class, 'onFooError'] /* Error handler */
)]
class FooController extends BaseController {}
```

**Using repeatable `Error` route attribute:**

```php
// /app/Controllers/Http/FooController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Error;
use App\Controllers\Http\Errors\CustomFooErrors;

#[Error(
    'foo', /* URI prefix context */
    pattern: '/foo/(:root)', /* URI prefix pattern */
    onError: [CustomFooErrors::class, 'onFooError'] /* Error handler */
)]
class FooController extends BaseController {}
```

---

> These examples show how to register custom error handlers either at the router **context level** or directly in **controller classes** using attributes.  
> The `onFooError` method from `CustomFooErrors` will handle errors like `404 Not Found` under the `/foo` URI namespace (e.g., `https://example.com/foo/invalid-route`).