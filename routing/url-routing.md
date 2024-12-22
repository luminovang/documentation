# Request Routing Handler

***

## Overview

Luminova's routing module simplifies capturing, processing, and executing both web page and CLI command requests within your controller class.

***

## Introduction

**Routing** is an essential part of the Luminova framework to handle incoming URI requests and matches them to the right controller methods. Its also manages request middleware authentication based on the URI prefix and patterns.

Luminova simplifies and speeds up routing by using a [Routing URI Prefix](/routing/url-prefix.md) to quickly match the initial part of the URI as context and load the `routes/<context>.php` to handle request controller routing. This means only the necessary routes are loaded, improving performance compared to loading all routes in a single context.

The routing module works efficiently for both `HTTP` and `CLI` requests, it captures and matches URI patterns, handles commands, and manages authentication with middleware as needed.

In Luminova routing, routeable controller methods support dependency injection. This means you can specify the types of modules you want as parameters in your controller methods. By default, this feature is disabled. To enable it, update your [environment configuration file](/running/env.md) setting the value of `feature.route.dependency.injection` to `enable`.

***

### Defining Controller Method

When defining controller methods or rendering callback handlers, you can use either a `Closure` or a string format that resembles a static method call (e.g., `ControllerBaseName::methodName`). The key difference is that you don’t need to specify the full class namespace, as it has already been registered in your application. You only need to provide the class base name and the method name (e.g., `ControllerBaseName::methodName`).

***

### Segment Capturing

Segment capturing is way to capture URL segments and pass them to controller as arguments for further processing, the capture pattern uses various methods like regular expression patterns (e.g., `/foo/([a-zA-Z0-9]+)/([0-9]+)`), named placeholders (e.g., `/foo/{title}/{id}`), or predefined strict placeholders (e.g., `/foo/(:string)/(int)`). While regular expressions offer flexibility, named and strict placeholders provide a more convenient and developer-friendly approach, making them especially suitable for beginners.

***

### View Caching

To learn about how Luminova view caching and static content serving work [read the documentation](/cache/view-caching.md).

***

### PHP Attributes

Luminova provides a `Route` attribute, allowing you to define both `HTTP` and `CLI` routes using attributes. [Read the documentation here](/routing/attribute.md).

***

### Base Usages

In your route context file `/routes/<context>.php`, the variable holding `Luminova\Routing\Router $router` and `App\Application $app` object  is already available as a global variable. Therefore, there's no need to specify the `use` keyword or import the router instance anymore except if it required which is usually for `bind` or `groupt` method.

```php 
// /routes/web.php

<?php
$router->get('/',  function (Application $app) {
    $app->view('index')->render([
	    // Options to pass to the view
	]);
});
```

**Using Controller Class**

When passing your controller class, you only need to provide the class base name and method, without the full namespace (e.g., `ExampleController::about`) instead of `App\Controllers\Http\ExampleController`. The namespace is already registered with the `CoreApplication` class, allowing you to pass only a controller class that extends `Luminova\Base\BaseController` by default.

```php 
<?php
$router->get('/about', 'ExampleController::about');
```

**Bind Group**

If you need to register a URI group for routing, you can use `bind` method which accepts only `2` arguments `group pattern` and `Closure`.

```php 
<?php
use \Luminova\Routing\Router;
use \Luminova\Core\CoreApplication;

$router->bind('/blog', function(Router $router, CoreApplication $app) {
    $router->middleware('GET', '/(:root)', 'Authentication::isAllowed');
    $router->get('/', 'ExampleController::blogs');
    $router->get('/(:alphanumeric)', 'ExampleController::blog');
});
```

**Capturing Any HTTP Method**

The `any` method allows you to capture any `HTTP` request method, which is especially useful for `AP`I applications.

For example, to handle all HTTP methods for a "Contact Us" page:

```php
$router->any('/contact', 'ExampleController::contact');
```

Alternatively, you can achieve the same functionality using the `capture` method with `Router::ANY_METHODS` or `ANY`:

```php
$router->capture('ANY', '/contact', 'ExampleController::contact');
```

Using `capture` for selective `HTTP` methods.

```php 
<?php
$router->capture('POST|GET|PUT', '/contact', 'ExampleController::contact');
```

**Dependency Injection**

Luminova routing supports dependency injection for controller methods. You can type-hint dependencies either in class methods or in callback functions to render your views.

**Using Controller Class Method:**

```php
// /app/Controllers/Http/ExampleController.php
<?php 
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
class ExampleController extends BaseController
{
    public function userInfo(Request $request): int 
    {
        $name = $request->getGet('name');
        // Method implementation
    }
}
```

**Using Callback Function:**

```php
use Luminova\Http\Request;

$router->get('/users', function (Request $request) {
    $name = $request->getGet('name');
    // Callback implementation
});
```

In both examples, the `Request` object is automatically injected into the method, allowing you to access request data directly.

***

### Command Controller

Registering command controller routing.
To register commands, you will need to first call the `group` method which is like your command base, within the group closure you can then call `command` method to register your commands associated to the group name.

```php
<?php
use \Luminova\Routing\Router;
use \Luminova\Core\CoreApplication;
use \Luminova\Command\Terminal;

$router->group("blog", function(Router $router, CoreApplication $app) {
    $router->command("list", 'Command::listBlogs');
    $router->command('id/(:int)', 'Command::showBlog');
});
```
To execute the above command run the following command:

To list all blogs based on the above example.
```bash
php index.php blog list
```

To get a single blog by its ID based on the above example.
```bash
php index.php blog id=2
```

***

### Class Definition

* Class namespace: `\Luminova\Routing\Router`
* This class is marked as **final** and can't be subclassed

***

## Methods

Explore additional usage examples in the [Routing Example Documentation](/routing/examples.md). For insights on using [Dynamic Segment Strict Placeholders in Routing](/routing/route-placeholders.md), visit the dedicated guide.

***

### context

The `context` method is a crucial part of routing optimization in Luminova. It initializes your routes based on the request URL path prefix. This method is called once at runtime in your application's entry point, typically `/public/index.php`.

The `context` method allows you to define URI prefixes and error handlers for specific URI prefixes. This ensures that only the necessary routes for handling requests are loaded based on the URL prefix, such as `api`, `console`, and more. The default context name `web` handles any incoming request without a unique registered prefix.

This method is flexible in its argument approach, supporting a `Context` object, an array, or `null` when using PHP attributes instead of code-based routing.

Using an array is particularly useful for scenarios with complex routing requirements or a large number of contexts. It offers better scalability and maintainability. Choose the method that best aligns with your specific use case and coding style preferences.

```php
public context(\Luminova\Routing\Prefix|array<string,mixed>|null ...$contexts): \Luminova\Routing\Router
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$contexts` | **Luminova\Routing\Prefix&#124;array<string,mixed>&#124;null** | Arguments containing routing prefix or an array of arguments. Pass `null` or leave blank only when using route attributes. |

**Return Value**

`Router` - Return router instance class.

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - Thrown if no context arguments are passed and route attributes are not enabled.

**See Also:**

- [Routing URL Prefix](/routing/url-prefix.md) - Learn more about how routing prefixes work and their usage.
- [Index Controller Handler](/public/index.md) - See an example of the front controller index file handler.

> **Note:** 
> The prefix name must be unique based on your `URLs` to discover and load only the required files. 
> Additionally, mixing context arguments with arrays and `Context` instances may produce unintended errors.

***

## HTTP Router Method

The HTTP router methods are named after common `HTTP` methods to make them simple and easy to understand. These methods help you define routes for different HTTP requests in a clear and intuitive way. 

### get

Get, a shorthand for route `capture` method to handle `GET` request method.

```php
public get(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **\Closure&#124;string** | The callback to execute (e.g `ClassBaseName::methodName`). |

***

### post

Post, a shorthand for route `capture` method to handle `POST` request method.

```php
public post(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **\Closure&#124;string** | The callback to execute (e.g `ClassBaseName::methodName`). |

***

### patch

Patch, a shorthand for route `capture` method to handle `PATCH` request method.

```php
public patch(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **\Closure&#124;string** | The callback to execute (e.g `ClassBaseName::methodName`). |

***

### delete

Delete, a shorthand for route `capture` method to handle `DELETE` request method.

```php
public delete(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **\Closure&#124;string** | The callback to execute (e.g `ClassBaseName::methodName`). |

***

### put

Put, a shorthand for route `capture` method to handle `PUT` request method.

```php
public put(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **\Closure&#124;string** | The callback to execute (e.g `ClassBaseName::methodName`). |

***

### options

Options, a shorthand for route `capture` method to handle `OPTIONS` request method.

```php
public options(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **\Closure&#124;string** | The callback to execute (e.g `ClassBaseName::methodName`). |

***

### middleware

The `HTTP` middleware security allows you to define authentication to validate requests before allowing other routes to execute or terminate their executions based on the returned response code: `STATUS_SUCCESS` (0) or `STATUS_ERROR` (1).

To instruct the `middleware` to terminate or proceed with execution, your controller method or closure must return an integer as described above.

Middlewares can be registered within a `bind` method for `URI` prefix or in the global scope of the routing context. It also support defining regular expression patterns and methods to match before executing.

```php
public middleware(string $methods, string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | The allowed methods, can be serrated with `&amp;#124;` pipe symbol (e.g. `GET&amp;#124;POST`). |
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/.*`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **\Closure&#124;string** | Callback controller or closure to execute. |

***

### before

The `before` method provides middleware security for `CLI` implementation. It allows you to define and execute authentication before other commands are executed. If the response code returns `STATUS_SUCCESS`, the subsequent commands will be executed; otherwise, if `STATUS_ERROR` is returned, other commands will not be executed.

Unlike the `middleware` method, which handles only `HTTP` method authentication and accepts regular `regex` patterns, the `before` method accepts only the command group name or `global` for global middleware security.

To register middleware with the group name, you must define your `before` method within the `group` method callback closure.

```php
public before(|string $group, \Closure|string $callback = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The command middleware group name or `global` for global middleware. |
| `$callback` | **\Closure&#124;string** | Callback controller handler (e.g `ClassBaseName::beforeMethodName` or `closure`). |

***

### after

The after route method, allow you to define a middleware to executes after route controller was executed successfully.
This method can only be used on `HTTP` request method, it not allowed in `CLI` mode.

```php
public after(string $methods, string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | The allowed methods, can be serrated with `&amp;#124;` pipe symbol (e.g. `GET&amp;#124;POST`). |
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/`, `/home`, `/user/([0-9])`). |
| `$callback` | **\Closure&#124;string** | The callback function to execute (e.g `ClassBaseName::afterMethodName`). |

***

### capture

The capture method allows you to define set of methods the controller should handle based on `HTTP` request method, and `URI` patterns to look for the listed methods. Once the method and patterns match the callback controller will be executed accordingly.

```php
public capture(string $methods, string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | The allowed methods, can be separated with `&amp;#124;` pipe symbol (e.g `GET\|POST\|PUT` or `ANY`). |
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **\Closure&#124;string** | The callback function to execute (e.g `ClassBaseName::methodName`). |

***

### command

The `command` method registers and captures command-line interface (CLI) requests, executing the specified commands. It is similar to the `capture` method but is specifically designed for CLI routing.

Unlike the `capture` method, `command` does not support regular regex patterns. Instead, it uses a set of filter rules to capture command arguments.

For example, suppose you have a command group `blogs` and a blog command controller class with two methods: `list` and `get`. The `list` method accepts an `int` argument to determine the number of blogs to list, while the `get` method accepts a `string` as the blog ID. You can register these commands with filters like `/list/limit/(:int)` for the list method and `/get/id/(:string)` for the get method.

```php
public command(string $command, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The allowed command name or command with filters (e.g `foo`, `foo/(:int)/bar/(:string)`). |
| `$callback()` | **\Closure&#124;string** | The callback function to execute (e.g `ClassBaseName::methodName`). |

**Example Usage:**

List Command:

```php
$router->group('blog', function(Router $router){
	$router->command('/list/limit/(:int)', 'BlogCommandController::list');
});
```

Get Command:

```php
$router->group('blog', function(Router $router){
	$router->command('/get/id/(:string)', 'BlogCommandController::get');
})
```

> **Note:**
> For commands that don't expect any arguments, you don't need to add any rule, just pass the name like `foo`.
> 
> Execution of the command controller must follow this pattern: `php index.php <command-group> <command> <argument>`.

***

### any

A shorthand for route `capture` method to handle any type of `HTTP` request methods.

```php
public any(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template view name (e.g `/`, `/home`, `/user/([0-9])`). |
| `$callback` | **\Closure&#124;string** | The callback to execute (e.g `ClassBaseName::methodName`). |

***

### bind

The `bind` method allows you to bind a specific pattern or path to a set of routes and middleware within the defined scope. This helps in organizing and grouping routes that share a common prefix such as `path` or `pattern`.

For example, if you have a base URL like `http://example.com/user`, using the `bind` method with the pattern `user` will group all related `URLs` under this base path. This means routes such as `http://example.com/user/account` and `http://example.com/user/dashboard` will be organized together, making your route definitions more structured and manageable.

```php
public bind(string $prefix, \Closure $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prefix` | **string** | The path prefix name or pattern (e.g. `/blog`, `/account/([a-z])` or `/account/(:placeholder)`). |
| `$callback` | **\Closure** | The callback function to handle routes group binding. |

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - If invalid callback is provided

***

### group

The group method works similar to `bind` method, except that `group` if used only in `CLI` group binding to bind  a collection of command controllers that belong to same group.

```php
public group(string $group, \Closure $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The command group name. |
| `$callback` | **\Closure** | Callback command function to handle grouped commands under same group name. |

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - If invalid callback is provided

> **Note:** Your group name must match with the defined group name in controller class.

***

### addNamespace

To register module controller class `namespace` group that router is allowed to lookup classed in.
If you wish to register more namespace this method should be called in your application class either within `constructor` or `onCreate` method.

```php
public addNamespace(string $namespace): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$namespace` | **string** | Class namespace string |

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - If namespace string is empty or contains invalid namespace characters.

> **Note:** When registering a route namespace, omit the suffix like `Cli` or `Http`. Instead, register your module's namespace ending with `Controllers` (e.g., `App\Modules\Foo\Controllers`, `App\Controllers`, `App\Modules\Controllers`) rather than `App\Modules\Foo\Controllers\Http` or `App\Modules\Foo\Controllers\Cli`.

***

### run

Run application routes, loop through registered routes and executed request methods if match is  found.
This method should be called once on `public/index.php` after calling the `context` method.

```php
public run(): void
```

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - Encounter error while executing controller callback.

***

### setErrorListener

Set the handler context error listener for `404` errors.

```php
public setErrorListener(\Closure|string|array $match, \Closure|array|null $callback = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$match` | **\Closure&#124;string&#124;array<int,string>** | Matching route callback or segment pattern for error handling. |
| `$callback` | **\Closure&#124;string&#124;array<int,string>&#124;null** | Optional error callback handler function. |

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - EThrows if callback is specified and `$match` is not a segment pattern.

> If the `$match` parameter is passed a callback handler and `$callback` parameter is passed `NULL`, then the handler will trigger the error callback whenever any `404` error occurred within the routing prefix context.
> 
> To define a custom error based on `group` or `patterns`, You must define your match patterns before callback method or closure.

***

### triggerError

To manually trigger an error handler.
This method can be used anywhere in controller class `$this->app->router->triggerError()` or routing context `$router->triggerError();`.

```php
public triggerError(int $status = 404): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP response status code (default: 404) |

***

### getBase

To get application  public controller base path.

```php
public getBase(): string
```

**Return Value:**

`string` - Return  base path.

***

### getSegment

To access more information about the requested `URI` segments.

```php
public getSegment(): \Luminova\Routing\Segments
```

**Return Value:**

`Segments` - Return URL segment instance

**See Also**

[View Segment Instance](/routing/url-paths.md) - Helper class to retrieve view segments.