# Unified Route Attribute for HTTP and CLI

***

## Overview

Leverage the power and flexibility of route attribute, in Luminova, making your routing logic cleaner and maintainable.

***

## Introduction

The `Route` attribute in Luminova provides a convenient way to define `HTTP` and `CLI` routes directly within your controller methods. It is **repeatable** and can be applied to multiple methods, offering flexible and straightforward routing for both web and CLI applications.

Luminova uses `PHPToken` to process controller classes and match the appropriate controller to handle a given request.  
While this may seem intensive, Luminova offers **optional attribute caching** in production environments to avoid re-parsing attributes on every request, significantly enhancing performance.

Additionally, Luminova provides a helpful command through `NovaKit` to generate routes based on attribute definitions.  
This is particularly useful for larger projects where you want to compare the performance of using attributes versus the default context-based routing system.

> You can run the following command to export and cache attribute-based routes:

```bash
php novakit context --export-attr
```

This command will:
- Export all attributes into the necessary files.
- Create contexts to load each route efficiently.
- Disable the runtime use of PHP attributes for routing.

***

### Getting Started

To start using the `PHP` attribute for routing in Luminova, follow these steps:

#### 1. Enable Attribute Routing

Update your environment configuration file `.env` to enable attribute routing by setting the `feature.route.attributes` variable to `enable`.

```ini
feature.route.attributes = enable
```

#### 2. Cache Attributes

Enabling caching of route attributes to improve performance by reducing the overhead of parsing annotations on every request. This is especially useful in production environments where routes do not change frequently.

```ini
feature.route.cache.attributes = enable
```
> Cache storage directory `/writeable/caches/routes/`.

#### 3. Configure Route Context Initialization

For optimal performance, set the route context initialization arguments to `Blank` or `Null` in `public/index.php` to avoid loading unnecessary contexts.

**Original Configuration**

If your `public/index.php` router context initialization looks like this:

```php
// /public/index.php

Boot::http()->router->context(
   new Prefix(Prefix::WEB, [ErrorController::class, 'onWebError']),
   new Prefix(Prefix::API, [ErrorController::class, 'onApiError']),
   new Prefix(Prefix::CLI)
)->run();
```

**Or you are using array context initialization like this:**

```php
// /public/index.php

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

#### 4. Simplify the Configuration

Change the router context initialization method call to:

```php
// /public/index.php

Boot::http()->router->context()->run();
```

> By following these adjustment, ensures that the router does not load unnecessary contexts, which improves performance.

***

## Class Definition

* Class namespace: `\Luminova\Attributes\Route`
* This class is marked as **final** and can't be subclassed

***

## Constants 

These constants represent middleware authentication types for routes:

| **Name**        | **Value**    | **Description**                                                                                   | **Use Case**                        |
|--------------------------|--------------|---------------------------------------------------------------------------------------------------|---------------------------------------------|
| BEFORE_MIDDLEWARE    | `before`     | Middleware executed before handling the main controller logic.                                    | HTTP authentication or pre-processing.      |
| AFTER_MIDDLEWARE     | `after`      | Middleware executed after the main controller logic has been handled.                             | HTTP cleanup, logging, or additional tasks. |
| GLOBAL_MIDDLEWARE    | `global`     | Middleware applied globally to all commands, regardless of group.              | Universal CLI security checks.|
| GUARD_MIDDLEWARE     | `guard`      | Middleware executed before commands in the same CLI group are executed.                           | CLI group security checks.                  |

---

## Properties

### pattern

The `pattern` property defines the URL or command pattern that the route should match.

**Examples:**

- **In HTTP Controller**: `'/users/([0-9]+)'` - Matches a URL like `https://example.com/users/123`.
- **In CLI Controller**: `'users/limit/(:int)'` - Matches a CLI command like `php index.php group-name users limit=50`.

***

### methods

The `methods` property specifies the `HTTP` methods that the route should respond to. This is particularly useful for RESTful APIs.

***

### error

The `error` property indicates whether this route is an error handler. Error handler routes are used to catch and handle `404` errors for specified `HTTP` methods.

***

### group

The `group` property defines the command group name for `CLI` routes. Grouping commands helps organize related commands together and the group name service as the base command before other following commands.

***

### middleware

The `middleware` property specifies middleware to be applied to the route. Middleware can be used to execute code before or after the main route handler.

**Examples:**

- `Route::BEFORE_MIDDLEWARE` - HTTP Middleware will execute before any controller route is handle.
- `Route::AFTER_MIDDLEWARE` - HTTP Middleware will execute after the controller is handled.
- `Route::GLOBAL_MIDDLEWARE` - CLI Middleware applies globally to all commands.
- `Route::GUARD_MIDDLEWARE` - CLI Middleware applies to a specific command group.

***

### Route Construct

Defines a repeatable attribute for registering `HTTP` and `CLI` routes.

This attribute maps controller methods to specific URI patterns and HTTP methods (for web) or command patterns (for CLI), with optional middleware and error handling support.

```php
public __construct(
   string $pattern = '/', 
   array $methods = ['GET'], 
   bool $error = false, 
   ?string $group = null, 
   ?string $middleware = null
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route pattern for HTTP (e.g. `/`, `/blog/([0-9-.]+)`)<br />or CLI command pattern (e.g. `blogs`, `blogs/limit/(:int)`). |
| `$methods` | **array** | The HTTP methods this route responds to (default: `['GET']`).<br/>Optionally use `['ANY']` to match all HTTP methods for enhanced performance. |
| `$error` | **bool** | Indicates if this route-method is an error handler for HTTP routes. |
| `$group` | **string&#124;null** | The CLI command group this route belongs to (default: `null`).<br/>Applicable only to CLI command controllers. |
| `$middleware` | **string&#124;null** | Optional middleware authentication assignment e.g `Route::BEFORE_MIDDLEWARE` (default: `null`). |

**Throw:**

- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - If the provided middleware handler is not a valid supported in context.

***

## HTTP Examples

The `HTTP` routes can respond to basic HTTP methods such as `GET`, `POST`, `PUT`, `DELETE`, `OPTIONS`, and more.

### Setting Up HTTP

In this example, we assume you have a controller class named `RequestController`.

```php
// /app/Controllers/Http/RequestController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Route;

class RequestController extends BaseController
{
   // Your controller methods
}
```

---

### Defining Routes with Attributes

#### Basic HTTP Route

Defines an HTTP route that responds to `GET` requests at the root URL (`/`):

```php
#[Route('/', methods: ['GET'])]
public function index(): int
{
   // Your code here
}
```

**Equivalent Router Method**

```php
$route->get('/', 'RequestController::index');
```

#### HTTP Route with Middleware

Define an HTTP route with middleware that executes before the main route handler.

```php
#[Route('/', methods: ['GET'], middleware: Route::BEFORE_MIDDLEWARE)]
public function middleware(): int
{
   // Your code here
}
```

**Equivalent Router Method**

```php
$route->middleware('GET', '/', 'RequestController::middleware');
```
***

## CLI Examples

CLI behaves differently from HTTP; methods like `POST`, `GET`, and other HTTP methods are not supported. To define commands for CLI, you can use the `command` method from the router class or the `Route` pattern argument with the `group` in the attribute definition.

### Setting Up CLI

In this example, we assume you have a controller class named `CLIController`.

```php
// app/Controllers/Cli/CommandController.php

namespace App\Controllers\Cli;

use Luminova\Base\BaseCommand;
use Luminova\Attributes\Route;
use Luminova\Attributes\Group;

#[Group(name: 'bar')]
class CommandController extends BaseCommand
{
   // Your CLI controller methods
}
```

---

### Defining Routes with Attributes

#### Basic CLI Route

Define a CLI route for the command `foo` within the `bar` group. Command: `php index.php bar foo`.

```php
#[Route('foo', group: 'bar')]
public function fooMethod(): int
{
   // Your code here
}
```

**Equivalent Router Method**

```php
$route->group('bar', static function(Router $router) {
   $router->command('foo', 'CommandController::fooMethod');
});
```

---

#### CLI Route with Middleware

For `CLI` middleware, it support only `Route::GLOBAL_MIDDLEWARE` and `Route::GUARD_MIDDLEWARE` middleware.

Define a CLI route with middleware that executes before the command group handler.

```php
#[Route(group: 'bar', middleware: Route::GUARD_MIDDLEWARE)]
public function middleware(): int
{
   // Your code here
}
```

**Equivalent Router Method**

```php
$route->group('bar', static function(Router $router){
   $router->guard('bar', 'CommandController::middleware');
   // Commands
});
```

#### Global Command Middleware

Define a global command middleware that executes before any command. The middleware name can be `Route::GLOBAL_MIDDLEWARE`.

```php
#[Route(group: 'bar', middleware: Route::GLOBAL_MIDDLEWARE)]
public function middleware(): int
{
   // Your code here
}
```

**Equivalent Router Method**

```php
$router->guard('global', 'CommandController::middleware');
$route->group('group-name', static function(Router $router){
    // Commands
});
```

***

## Attribute Commands

PHP attributes made it easy for routing definitions, making them more intuitive and maintainable. However, you might wonder whether using attributes provides better performance compared to traditional routing methods and context configurations in your `public/index.php` file.

Luminova offers a straightforward command to convert your route definitions from attributes into router-based configurations. This command helps you transition smoothly if you decide to switch from attributes to router-based routing.

***

### Export Command

The `export` command extracts all route definitions specified with attributes for `CLI`, `WEB`, and `API` contexts. It generates the necessary files and contexts for routing and updates your environment variable `feature.route.attributes` to `disabled`. This allows you to switch to a router-based routing system while preserving your attribute-based route definitions.

To compare the performance of attributes versus router methods, you can enable performance profiling by setting the environment variable `debug.show.performance.profiling` to `TRUE`. This will display metrics such as `Execution Time`, `Memory Usage`, and `Files Loaded`, helping you to compare and make an informed decision.

***

### Usage

To use the export command, open your command line tool, navigate to your project's root directory (where the `novakit` file is located), and run:

```bash
php novakit context --export-attr
```

> **Note**
> After exporting and converting attributes to router methods, you can further customize the generated configuration. 
> You may also split methods based on prefixes and add more context prefixes to `public/index.php` to further optimize performance. 

This flexibility allows you to customize the routing setup to better fit your application's needs.

**Clearing Cached Attributes**

When attribute caching is enabled, changes made or new attributes added will not take effect because the cache is stored permanently without an expiration time. You can manually navigate to the `/writeable/caches/routes/` directory and delete the cached `PHP` files, or run the following command:

```bash
php novakit context --clear-attr
```

---

### Related Attributes

- [Prefix Attribute](/routing/prefix-attribute.md) - HTTP controller class level attribute for URI prefixing.
- [Error Attribute](/routing/error-attribute.md) - HTTP controller class level attribute for global URI prefix or controller error handling.
- [Group Attribute](/routing/cli-group-attribute.md) - CLI controller class level attribute for command group prefixing.