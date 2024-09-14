# Request Route Attributes

***

## Overview

Leverage the power and flexibility of route attribute, in Luminova, making your routing logic cleaner and maintainable.

***

## Introduction

The `Route` attribute in Luminova is designed to provide a convenient way to define `HTTP` and `CLI` routes directly within your Controller methods. This attribute is repeatable and can be applied to methods, enabling flexible and straightforward routing for your web and CLI applications.

Additionally, Luminova offers a convenient way through the `NovaKit` command to generate routes based on attribute definitions. This is particularly useful for large projects where you want to compare the performance of using attributes versus configuring routes based on the default context and route methods.

Running the command `php novakit context --export-attr` will export all attributes, create the necessary files and contexts to load each route, and disable the use of PHP attributes for routing.

***

## Error Attribute

Luminova provides an optional class-level `Error` attribute, allowing you to define a global error handler for `HTTP` routes. [Read the documentation here](/routing/error-attribute.md).

***
## Getting Started

To start using the `PHP` attribute for routing in Luminova, follow these steps:

### 1. Enable Attribute Routing

Update your environment configuration file `.env` to enable attribute routing by setting the `feature.route.attributes` variable to `enable`.

```env
feature.route.attributes = enable
```

### 2. Cache Attributes

Enabling caching of route attributes to improve performance by reducing the overhead of parsing annotations on every request. This is especially useful in production environments where routes do not change frequently.

```env
feature.route.cache.attributes = enable
```
> Cache storage directory `/writeable/caches/routes/`.

### 3. Configure Route Context Initialization

For optimal performance, set the route context initialization arguments to `Blank` or `Null` in `public/index.php` to avoid loading unnecessary contexts.

**Original Configuration**

If your `public/index.php` router context initialization looks like this:

```php
//public/index.php
<?php 
//....
Boot::http()->router->context(
    new Prefix(Prefix::WEB, [ViewErrors::class, 'onWebError']),
    new Prefix(Prefix::API, [ViewErrors::class, 'onApiError']),
    new Prefix(Prefix::CLI)
)->run();
```

**Or you are using array context initialization like this:**

```php
//public/index.php
<?php 
//....
Boot::http()->router->context(
    [
        'prefix' => 'web',
        'error' => [ViewErrors::class, 'onWebError']
    ],
    [
        'prefix' => 'api',
        'error' => [ViewErrors::class, 'onApiError']
    ],
    [
        'prefix' => 'cli',
        'error' => null
    ]
)->run();
```

### 4. Simplify the Configuration

Change the router context initialization method call to:

```php
//public/index.php
<?php 
//....
Boot::http()->router->context()->run();
```

> By following these adjustment, ensures that the router does not load unnecessary contexts, which improves performance.

***

### Route Construct

Route attribute constructor for both `HTTP` and `CLI`.

```php
public __construct(string $pattern = '/', array $methods = ['GET'], bool $error = false, string|null $group = null, string|null $middleware = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route pattern for HTTP (e.g. `/`, `/blog/([0-9-.]+)`)<br />or CLI command pattern (e.g. `blogs`, `blogs/limit/(:int)`). |
| `$methods` | **array** | The HTTP methods this route should responds to. (default: ['GET']). |
| `$error` | **bool** | Indicates if this is an error handler route for HTTP methods. |
| `$group` | **string&#124;null** | The command group name for CLI route (default: NULL). |
| `$middleware` | **string&#124;null** | Middleware type (default: NULL).<br />-   HTTP middleware route - `before` or `after`.<br />-   CLI middleware rote `global` for global middleware or `before` for command group middleware. |

***

## HTTP Examples

The `HTTP` routes can respond to basic HTTP methods such as `GET`, `POST`, `PUT`, `DELETE`, `OPTIONS`, and more.

### Setting Up HTTP

In this example, we assume you have a controller class named `HTTPController`.

```php
// app/controllers/HTTPController.php

namespace App\Controllers;

use Luminova\Base\BaseController;
use Luminova\Attributes\Route;

class HTTPController extends BaseController
{
    // Your controller methods
}
```

### Defining Routes with Attributes

#### Basic HTTP Route

Define an HTTP route that responds to `GET` requests at the root URL (`/`).

```php
#[Route('/', methods: ['GET'])]
public function index(): int
{
    // Your code here
}
```

**Equivalent Router Method**

```php
<?php 
$route->get('/', 'HTTPController::index');
```

#### HTTP Route with Middleware

Define an HTTP route with middleware that executes before the main route handler.

```php
#[Route('/', methods: ['GET'], middleware: 'before')]
public function middleware(): int
{
    // Your code here
}
```

**Equivalent Router Method**

```php
<?php 
$route->middleware('GET', '/', 'HTTPController::middleware');
```
***
## CLI Examples

CLI behaves differently from HTTP; methods like `POST`, `GET`, and other HTTP methods are not supported. To define commands for CLI, you can use the `command` method from the router class or the `Route` pattern argument with the `group` in the attribute definition.

### Setting Up CLI

In this example, we assume you have a controller class named `CLIController`.

```php
// app/Controllers/CLIController.php

namespace App\Controllers;

use Luminova\Base\BaseCommand;
use Luminova\Attributes\Route;

class CLIController extends BaseCommand
{
    // Your CLI controller methods
}
```

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
<?php 
$route->group('bar', static function(Router $router){
    $router->command('foo', 'CLIController::fooMethod');
});
```

#### CLI Route with Middleware

For `CLI` middleware, it support only `global` and `before` middleware.

Define a CLI route with middleware that executes before the command group handler.

```php
#[Route(group: 'bar', middleware: 'before')]
public function middleware(): int
{
    // Your code here
}
```

**Equivalent Router Method**

```php
<?php 
$route->group('bar', static function(Router $router){
    $router->before('bar', 'CLIController::middleware');
    // Commands
});
```

#### Global Command Middleware

Define a global command middleware that executes before any command. The middleware name can be `global`.

```php
#[Route(group: 'bar', middleware: 'global')]
// Or #[Route(group: 'bar', middleware: 'before')]
public function middleware(): int
{
    // Your code here
}
```

**Equivalent Router Method**

```php
<?php 
$router->before('global', 'CLIController::middleware');
$route->group('group-name', static function(Router $router){
    // Commands
});
```

***

## Property Descriptions

### pattern

The `pattern` property defines the URL or command pattern that the route should match.

```php
public string $pattern = '/';
```

- **HTTP Example**: `'/users/([0-9]+)'` - Matches a URL like `https://example.com/users/123`.
- **CLI Example**: `'users/limit/(:int)'` - Matches a CLI command like `php index.php group-name users limit=50`.

***

### methods

The `methods` property specifies the `HTTP` methods that the route should respond to. This is particularly useful for RESTful APIs.

```php
public array $methods = ['GET'];
```

- **Example**: `['GET', 'POST']` - The route will respond to both `GET` and `POST` requests.

***

### error

The `error` property indicates whether this route is an error handler. Error handler routes are used to catch and handle `404` errors for specified `HTTP` methods.

```php
public bool $error = false;
```

- **Example**: `true` - Marks the route as an error handler.

***

### group

The `group` property defines the command group name for `CLI` routes. Grouping commands helps organize related commands together and the group name service as the base command before other following commands.

```php
public ?string $group = null;
```

- **Example**: `'admin'` - Groups the command under the `php index admin <command> <options> <arguments>` group.

***

### middleware

The `middleware` property specifies middleware to be applied to the route. Middleware can be used to execute code before or after the main route handler.

```php
public ?string $middleware = null;
```

- **HTTP Before Middleware Example**: `'before'` - Middleware will execute before the route handler.
- **HTTP After Middleware Example**: `'after'` - Middleware will execute after the route handler.
- **CLI Global Middleware Example**: `'global'` - Middleware applies globally to CLI commands.
- **CLI Before Group Middleware Example**: `'before'` - Middleware applies to a specific command group.

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