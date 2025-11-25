# Centralized Error Handling for HTTP Controllers

***

## Overview

Handles application-wide errors in Luminova HTTP controllers, letting you define custom error responses globally or by route prefix.

***

## Introduction

The **ErrorController** in Luminova provides a centralized way to handle errors for any HTTP controller, whether you are using attribute-based routing or method-based routing.

This controller defines how errors are displayed for different request contexts. Its single abstract static method, `onTrigger`, serves as the central entry point for handling errors and works together with the router’s `trigger` method to manually raise errors based on HTTP status codes. 

In addition, it includes example methods such as `onApiError` and `onWebError`, which can be renamed, removed, or extended to fit your needs. These are regular static methods for registering custom error behavior within the controller scope, and you can add more methods or even create new classes to handle similar use cases.

---

### What the ErrorController does

* **Template `4xx` errors**
  Handles missing templates or routes when the router cannot resolve a path.

* **Manual error triggering via `onTrigger`**
  Allows you to explicitly trigger errors in your code using `$router->trigger(statusCode)`, making it easy to show consistent error pages across your application.

> This design gives you complete control over how errors are enforced and displayed, helping you provide meaningful and user-friendly responses instead of generic error pages.

---

> **Note:**
> * Error handler methods can be written as **static** or **non-static**.
> * All handler methods must be **public** — they cannot be `protected` or `private`.
> * **Dependency injection is fully supported**, meaning you can request any service or class directly in your error handler’s parameters, and Luminova will provide it automatically.

***

## Implementations

You can define custom error handlers in two ways:

1. Add new methods directly inside your existing `App\Errors\Controllers\ErrorController` class.
2. Create a separate class that implements `Luminova\Interface\RoutableInterface` and `Luminova\Interface\ErrorHandlerInterface`.

---

### Custom Class Example

Below is an example of creating a dedicated controller class for handling custom errors:

```php
// /app/Errors/Controllers/CustomFooErrors.php

namespace App\Errors\Controllers;

use App\Application;
use Luminova\Interface\RoutableInterface;
use Luminova\Interface\ErrorHandlerInterface;

class CustomFooErrors implements RoutableInterface, ErrorHandlerInterface
{
    public static function onTrigger(Application $app, int $status = 404, array $arguments = []): int
    {
        // Handle errors manually based on the status code
        // Example: render a specific template or log the error
    }
    
    /**
     * Example of a custom error handling method.
     * 
     * @param Application $app The current application instance.
     * @return int Must return a valid response status code (e.g., STATUS_SILENCE or STATUS_SUCCESS).
     */
    public static function onFooError(Application $app): int 
    {
        // Provide your custom error response
        return STATUS_SILENCE;
    }
}
```

> **Note:** All error handler methods must accept the current `Application` instance as an argument, and they must return a valid response status code such as `STATUS_SILENCE` or `STATUS_SUCCESS`.

***

### Registering Handlers

You can hook up custom error handlers in two ways: **attribute-based routing** or **context-based routing**. In both cases, the handler must be a callable, either a closure or a `[class, method]` array.

* **Closure example:**
  `fn(Application $app): int => ErrorController::onFooError($app)`
* **Callable array example:**
  `[ErrorController::class, 'onFooError']`

---

#### Context-Based Handling

If you are not using attributes, you can manually register an error handler in `public/index.php`. The router expects a callable or a `[class, method]` array that points to your error logic.

**Example using the `Prefix` object for URI-based contexts:**

```php
// /public/index.php

use Luminova\Boot;
use Luminova\Routing\Prefix;
use App\Errors\Controllers\ErrorController;

Boot::http()->router->context(
    new Prefix(
        'foo', /* URI prefix where this handler applies */
        [ErrorController::class, 'onFooError'] /* Error handler */
    ),
    /* More prefix... */ 
)->run();
```

**Example using an array for URI-based contexts:**

```php
// /public/index.php

use Luminova\Boot;
use App\Errors\Controllers\ErrorController;

Boot::http()->router->context(
    [
        'prefix' => 'foo',
        'error' => [ErrorController::class, 'onFooError']
    ]
)->run();
```

---

#### Attribute-Based Handling

When using attributes, you can attach error handlers directly inside your controllers (class-scope).

**Using the non-repeatable `Prefix` attribute:**

For more information see [URI Prefix Attribute](/attributes/uri-prefix.md) documentation.

```php
// /app/Controllers/Http/FooController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

#[Prefix(
    pattern: '/foo/(:root)', /* URI pattern */
    onError: [ErrorController::class, 'onFooError'] /* Error handler */
)]
class FooController extends Controller {}
```

**Using the repeatable `Error` attribute:**

For more information see [Error Attribute](/attributes/cli-group.md) documentation.

```php
// /app/Controllers/Http/FooController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Error;
use App\Errors\Controllers\CustomFooErrors;

#[Error(
    context: 'web', /* All web-based context */
    pattern: '/(:root)', /* URI pattern */
    onError: [CustomFooErrors::class, 'onWebError'] /* Error handler */
)]
#[Error(
    context: 'foo', /* Foo web-based context */
    pattern: '/foo/(:root)',
    onError: [CustomFooErrors::class, 'onFooError']
)]
class FooController extends Controller {}
```

> These examples show how to register error handlers either globally (via router context) or directly inside a controller (via attributes).
> For instance, the `onFooError` method in `CustomFooErrors` will catch errors like `404 Not Found` under the `/foo` namespace (e.g., `https://example.com/foo/invalid-route`).

For inline method scope error handling using **Route** attribute see [Route Attribute](/attributes/route.md) documentation.

***

## Class Definition

* Class namespace: `\App\Errors\Controllers\ErrorController`
* This class implements:
[Luminova\Interface\RoutableInterface](/interface/classes.md#routableinterface), [Luminova\Interface\ErrorHandlerInterface](/interface/classes.md#errorhandlerinterface)

***

## Methods

### onTrigger

Handle a manually triggered routing error.

This method is invoked when the routing system explicitly triggers an error status (e.g., via `$router->trigger(404)`).

```php
public static onTrigger(App\Application $app, int $status = 404, array $arguments = []): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app`    | **\App\Application<CoreApplication>** | Application instance providing access to core services. |
| `$status`    | **int** | HTTP status code associated with the error (default: `404`). |
| `$arguments`    | **array** | Optional request data such as URI segments or parameters. |

**Return Value:**

`int` - Return response status code either (`STATUS_SUCCESS` or `STATUS_SILENCE`). 

**Example:**

You can trigger an error anywhere in your application. 

```php
// app/Controllers/Http/FooController.php
// app/Modules/<?module>/Controllers/Http/FooController.php
 
$this->app->router->trigger(500);
```

> This is the global fallback handler for manually triggered errors.
> Renders an error view with the provided status code and request data.

***

### onWebError

Handle web-based routing errors.

A custom method to handle website errors for the global HTTP **Web** routes.

```php
public static onWebError(App\Application $app): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app`    | **\App\Application** | The current application instance. |

**Return Value:**

`int` - Return response status code either `STATUS_SUCCESS` or `STATUS_SILENCE`. 

***

### onApiError

Handle API routing errors.

A custom method to handle errors for the HTTP **APIs** routes.

```php
public static onApiError(App\Application $app): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app`    | **\App\Application** | The current application instance. |

**Return Value:**

`int` - Return response status code either `STATUS_SUCCESS` or `STATUS_SILENCE`. 

> The `API` context is identified by checking whether the first part of the request URL matches your API prefix (for example, `https://example.com/api/...`). This check is performed using the `Luminova::isApiPrefix()` method.