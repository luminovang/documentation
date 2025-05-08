# HTTP and CLI Request Routing System

***

## Overview

The routing system in Luminova is optimized for capturing, processing, and executing HTTP requests and CLI commands within routable controller classes.

***

## Introduction

**Routing** is a core component of the Luminova framework, responsible for handling incoming URI requests and matching them to the appropriate controller methods. It also manages request-level middleware authentication based on URI prefixes and patterns.

Luminova optimizes and simplifies routing by using a [URI Route Prefixing](/routing/url-prefix.md), which allows it to quickly identify the initial part of the URI as a context and load only the necessary `routes/<context>.php` file. This selective loading approach improves performance by avoiding the overhead of loading all routes into a single context.

The routing module is designed to efficiently handle both `HTTP` requests and `CLI` commands. It captures and matches URI patterns, executes commands, and applies middleware authentication when required.

In Luminova, routable controller methods also support **dependency injection**. You can specify module types as method parameters to automatically inject dependencies. By default, this feature is **disabled**. To enable it, update your [environment configuration file](/running/env.md) by setting the following:

```ini
feature.route.dependency.injection=enable
```

***

### Defining Controllers

When defining controller methods or rendering callback handlers, you can use either a `Closure` or a string that resembles a static method call (e.g., `ControllerBaseName::methodName`).  
The key difference is that you do not need a fully-qaulified namespace, the base name of the controller and the method name are required.  

**Example:**  

The application already knows the registered controller namespace internally.

```php
$route->get('/home', 'HomeController::index');
```

---

### Capturing Segments

**Segment capturing** allows you to extract parts of the URL and pass them as arguments to your controller methods for further processing.  
There are several ways to define capture patterns:

- **Regular Expressions**  
  Example: `/foo/([a-zA-Z0-9]+)/([0-9]+)`
  
- **Named Placeholders**  
  Example: `/foo/{title}/{id}`
  
- **Predefined Pattern Placeholders**  
  Example: `/foo/(:string)/(:int)`

> While regular expressions provide maximum flexibility, **named** and **pattern placeholders** offer a more convenient and readable approach, making them especially ideal for beginners and maintaining cleaner route definitions.

### View Content Caching

Luminova supports view content caching and serving cached static content to significantly improve response times and reduce server load. To learn more about how view caching works and how to configure it, [read the documentation](/cache/view-caching.md).

---

### Routing PHP Attributes

Luminova offers a `Route` attribute that allows you to define both `HTTP` and `CLI` routes directly within your controller classes. Additionally we support class-level attributes like `Prefix`, `Error` and `Group`, using attributes makes routing more organized, flexible, and readable.  [Read the documentation here](/routing/route-attribute.md) for full details.

***

### Base Usages

In your route context file `/routes/<context>.php`, the variable holding `Luminova\Routing\Router $router` and `App\Application $app` object  is already available as a global variable. Therefore, there's no need to specify the `use` keyword or import the router instance anymore except if it required which is usually for `bind` or `group` method.

```php 
// /routes/web.php

$router->get('/',  function (Application $app) {
   return $app->view('index')->render([
	   'foo' => 'Bar' // Options to pass to the view
	]);
});
```

**Using Controller Class**

When passing your controller class, you only need to provide the class base name and method, without the full namespace (e.g., `ExampleController::about`) instead of `App\Controllers\Http\ExampleController`. The namespace is already registered with the `CoreApplication` class, allowing you to pass only a controller class that extends `Luminova\Base\BaseController` by default.

```php 
// /routes/web.php

$router->get('/about', 'ExampleController::about');
```

**Bind Group**

If you need to register a URI group for routing, you can use `bind` method which accepts only `2` arguments `group pattern` and `Closure`.

```php
// /routes/web.php

use Luminova\Routing\Router;
use Luminova\Core\CoreApplication;

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
// /routes/web.php

$router->any('/contact', 'ExampleController::contact');
```

Alternatively, you can achieve the same functionality using the `capture` method with `Router::ANY_METHODS` or `ANY`:

```php
// /routes/web.php

$router->capture('ANY', '/contact', 'ExampleController::contact');
```

Using `capture` for selective `HTTP` methods.

```php 
// /routes/web.php

$router->capture('POST|GET|PUT', '/contact', 'ExampleController::contact');
```

**Dependency Injection**

Luminova routing supports dependency injection for controller methods. You can type-hint dependencies either in class methods or in callback functions to render your views.

**Using Controller Class Method:**

```php
// /app/Controllers/Http/ExampleController.php

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
// /routes/web.php

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
// /routes/cli.php

use Luminova\Routing\Router;
use Luminova\Core\CoreApplication;
use Luminova\Command\Terminal;

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

> **More Examples and Guides:**  
> Explore additional usage patterns in the [Routing Example Documentation](/routing/examples.md).  
> For advanced control over dynamic route segments, see the guide on [Segment Pattern Placeholders](/routing/route-placeholders.md).

***

## Class Definition

* Class namespace: `\Luminova\Routing\Router`
* This class implements: [\Luminova\Interface\RouterInterface](/interface/classes.md#routerinterface)
* This class is marked as **final** and can't be subclassed

***

## Methods

### context

Registers the application routing context based on URI prefixes (e.g., `web`, `cli`, `api`, `console`) or custom prefix names.

The `context` method is an essential part of Luminova’s routing optimization strategy. It initializes and loads routes based on a given URI prefix, allowing the framework to load only the relevant route files (like `routes/web.php`, `routes/api.php`, or `routes/cli.php`) at runtime. This method improves performance by avoiding loading all routes at once.

If no specific prefix is matched, the default `web` context handles the request.

The `context` method also supports defining custom error handlers for each prefix, similar to using attribute-based routing. It accepts a flexible input: either a `Luminova\Routing\Prefix` object or an array describing the prefix and error handler. If you're using PHP attributes to define routes, simply leave it blank.

> **Tip:** Using an array for context definition is particularly useful in large applications with many route groups. It helps improve maintainability and scalability.

```php
public context(\Luminova\Routing\Prefix|array<string,mixed> ...$contexts): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$contexts` | **Luminova\Routing\Prefix&#124;array<string,mixed>** | Optional arguments containing routes prefix object, an array of prefix or leave blank when using attribute routing. |

**Return Value**

`self` - Return router instance class.

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - Thrown if no context arguments are passed and route attributes are not enabled.

> **Note:**  
> The prefix name must be unique and reflect your URL structure accurately.  
> Avoid mixing `Prefix` objects and array definitions in the same call, as it may lead to unintended errors.

**See Also:**

- [Method-Based Routing URI Prefixing](/routing/url-prefix.md) — How prefixes work and how to configure them.
- [Front Controller Setup](/public/index.md) — Example of setting up the main application handler.

***

### run

Starts the application routing system by processing incoming `HTTP` requests or `CLI` commands and matching them against registered routes.

This method is responsible for identifying the matching route based on the incoming URI and request method (e.g., `GET`, `POST`, or CLI command). Once matched, it dispatches the request to the appropriate controller method. It also integrates profiling and lifecycle event handling to ensure proper execution and graceful shutdown.

```php
public run(): void
```

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) – Thrown if routing fails or if no suitable route is found for the request.

> This method is typically called in your application's front controller (`/public/index.php`) to bootstrap and run the routing system.

**Example:**

```php
// /public/index.php
declare(strict_types=1);

use Luminova\Boot;

require_once __DIR__ . '/../system/Boot.php';

// Set the correct working directory if launched via CLI
if (getcwd() . DIRECTORY_SEPARATOR !== DOCUMENT_ROOT) {
    chdir(DOCUMENT_ROOT);
}

// Initialize context and run the router
Boot::http()->router->context(...)->run();
```

> **Note:** The `run()` method should only be called once, after initializing the routing context, to handle the incoming request lifecycle correctly.

***

## HTTP Routing Methods

HTTP routing methods are designed specifically for controller classes that handle web requests, such as APIs or websites. Each method corresponds to a standard HTTP verb (e.g., `GET`, `POST`, `PUT`, `DELETE`) to make route definitions intuitive and organized. These methods allow you to clearly define how different types of HTTP requests should be handled by your controller.

### get

Get, a shorthand for route `capture` method to handle `GET` request method.

```php
public get(string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **Closure&#124;string** | The callback to execute (e.g., `ClassBaseName::methodName`). |

***

### post

Post, a shorthand for route `capture` method to handle `POST` request method.

```php
public post(string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **Closure&#124;string** | The callback to execute (e.g., `ClassBaseName::methodName`). |

***

### patch

Patch, a shorthand for route `capture` method to handle `PATCH` request method.

```php
public patch(string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **Closure&#124;string** | The callback to execute (e.g., `ClassBaseName::methodName`). |

***

### delete

Delete, a shorthand for route `capture` method to handle `DELETE` request method.

```php
public delete(string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **Closure&#124;string** | The callback to execute (e.g., `ClassBaseName::methodName`). |

***

### put

Put, a shorthand for route `capture` method to handle `PUT` request method.

```php
public put(string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **Closure&#124;string** | The callback to execute (e.g., `ClassBaseName::methodName`). |

***

### options

Options, a shorthand for route `capture` method to handle `OPTIONS` request method.

```php
public options(string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **Closure&#124;string** | The callback to execute (e.g., `ClassBaseName::methodName`). |

***

### middleware

Registers an HTTP `before` middleware to authenticate requests before handling controllers.

This method allows you to apply middleware security logic that executes prior to any associated HTTP controllers. If the middleware callback returns `STATUS_ERROR`, the routing process will terminate, preventing further execution.

Middlewares can be registered within a `bind` method for a specific URI prefix or in the global scope of the routing context. It also support defining regular expression patterns and methods to match before executing.

```php
public middleware(string $methods, string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** |  The allowed HTTP methods, separated by a `&amp;#124;` pipe symbol (e.g,. `GET&amp;#124;POST`). |
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/.*`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **Closure&#124;string** | The callback function or controller method to execute. |

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - Thrown if the method is called in non-HTTP context or the `$methods` parameter is empty.

***

### after

Registers an HTTP `after` middleware to execute logic after a controller has been handled.

This method applies middleware logic that runs after a controller processes a request. It is typically used for tasks such as cleanup or additional post-processing.

```php
public after(string $methods, string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | The allowed HTTP methods, separated by a `&amp;#124;` pipe symbol (e.g., `GET&amp;#124;POST`). |
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/`, `/home`, `/user/([0-9])`). |
| `$callback` | **Closure&#124;string** | The callback function or controller method to execute (e.g., `ClassBaseName::afterMethodName`). |

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - Thrown if the `$methods` parameter is empty.

> **Note:** This method can only be used on `HTTP` request routing, it not allowed in `CLI` mode.

***

### capture

Registers HTTP request methods, URI patterns, and corresponding callback or controller methods.

This method allows you to define routes by specifying supported HTTP methods, a URL pattern, and the callback or controller method to execute when the pattern matches a client request. Multiple HTTP methods can be specified using the pipe (`|`) symbol.

```php
public capture(string $methods, string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | The allowed method(s), multiple HTTP methods can be separated by `&amp;#124;` pipe symbol (e.g., `GET\|POST\|PUT` or `ANY`). |
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/`, `/home`, `/user/([0-9])` or `/user/(:placeholder)`). |
| `$callback` | **Closure&#124;string** | The callback function or controller method to execute (e.g., `ClassBaseName::methodName`). |

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - Thrown if an empty method string is provided.

***

### any

Capture and handle requests for any HTTP method.

This method leverages `Router::ANY_METHODS` to match and handle requests for any HTTP method. It is a convenient way to define routes that respond to all HTTP methods without explicitly specifying them.

```php
public any(string $pattern, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The route URL pattern or template name (e.g., `/`, `/home`, `/user/([0-9])`). |
| `$callback` | **Closure&#124;string** | The callback function or controller method to execute (e.g., `ClassBaseName::methodName`). |

***

### bind

Groups related routes under a specified URI prefix.

The `bind` method simplifies route organization by associating a group of routes with a common base path or pattern. It allows you to define and manage multiple nested `URI` patterns under a shared prefix, enhancing the clarity and maintainability of your routing structure. Additionally, middleware can be applied within the scope of the defined closure, enabling advanced route-specific logic.

```php
public bind(string $prefix, Closure $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prefix` | **string** | The base path or URI pattern (e.g,. `/blog`, `/account/([a-z])` or `/account/(:placeholder)`). |
| `$callback` | **Closure** | The closure containing the route definitions for the group. |

**Example:**

Using `bind` to group routes under a `/blog` prefix:

```php
$router->bind('/blog/', static function (Router $router) {
   $router->get('/', 'BlogController::listBlogs');                   // Matches '/blog/'
   $router->get('/([a-zA-Z0-9-]+)', 'BlogController::readBlog');     // Matches '/blog/{slug}'
   $router->get('/edit/([a-zA-Z0-9-]+)', 'BlogController::editBlog'); // Matches '/blog/edit/{slug}'
});
```

**Explanation:**

When you group routes using `bind`, the specified prefix (e.g., `/blog`) is prepended to all routes defined within the closure. For example:
- `http://example.com/blog/` maps to `listBlogs`.
- `http://example.com/blog/{slug}` maps to `readBlog`.
- `http://example.com/blog/edit/{slug}` maps to `editBlog`.

This approach keeps route definitions organized and manageable, especially for applications with multiple related endpoints. Additionally, middleware can be applied selectively within the closure to handle group-specific logic. 

***

## CLI Routing Methods

`CLI` routing methods are used in command controllers to define and manage grouped commands, apply middleware, and handle command execution logic effectively.

### group

Organizes CLI commands into a specific group for execution.

Similar to the HTTP `bind` method, the `group` method simplifies the organization of CLI commands by grouping related commands under a common name. This is especially useful for commands related to the same controller, making it easier to execute and maintain them.

```php
public group(string $group, Closure $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The name of the command group (e.g., `blog`, `user`). |
| `$callback` | **Closure** | A callback function that defines the commands for the group. |

**Example:**

Grouping CLI commands under a `blog` group:

```php
$router->group('blog', static function (Router $router) {
   $router->command('list', 'BlogController::blogs');     // Command to list all blogs
   $router->command('id/(:int)', 'BlogController::blog'); // Command to fetch a blog by ID
});
```

**Executing Commands:**

```bash
# Navigate to the project's public directory
cd path/to/project/public/

# Execute commands within the `blog` group
php index.php blog list       # Lists all blogs
php index.php blog id=4       # Fetches the blog with ID 4
```

> **Note:** Ensure that the group name matches the value of the `$group` property defined in the command controller class to avoid routing mismatches or execution errors.

***

### command

Registers a CLI command and its corresponding callback or controller method.

This method is used to define CLI commands, specifying the command name and the callback function or controller method to execute when the command is run in the terminal. Unlike HTTP routes, CLI commands are defined using this method specifically within a `group` method and does not support regular regex patterns. Instead, it uses a set of filter rules to capture command arguments.

```php
public command(string $command, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command group name or a command group name with filters (e.g., `foo`, `foo/(:int)/bar/(:string)`). |
| `$callback()` | **Closure&#124;string** | The callback function or controller method to execute (e.g., `ClassBaseName::methodName`). |

**Example Usage:**

Suppose you have a command group `blogs` and a blog command controller class with two methods: `list` and `get`. The `list` method accepts an `int` argument to determine the number of blogs to list, while the `get` method accepts a `string` as the blog ID. You can register these commands with filters like `/list/limit/(:int)` for the list method and `/get/id/(:string)` for the get method.

**List Command:**

```php
$router->group('blog', function(Router $router){
	$router->command('/list/limit/(:int)', 'BlogCommandController::list');
});
```

**List Command With Attribute:**

```php
// /App/Controllers/Cli/BlogCommandController.php

#[Route('list/limit/(:int)', group: 'blog')]
public function list(int $limit = 10): int
{}
```

**Get Command:**

```php
$router->group('blog', function(Router $router){
	$router->command('/get/id/(:string)', 'BlogCommandController::get');
});
```

**Get Command With Attribute:**

```php
// /App/Controllers/Cli/BlogCommandController.php

#[Route('get/id/(:string)', group: 'blog')]
public function get(string $id): int
{}
```

> **Note:**
> For commands that don't expect any arguments, you don't need to add any filters, just pass the name like `foo`.
> 
> Additionally, execution of the command controller must follow this pattern: `php index.php <command-group> <command> <argument>`.

***

### guard

Registers a CLI `before` middleware guard to authenticate commands within a specific group.

The `guard` method provides middleware security for `CLI` implementation. It applies middleware logic to CLI commands within a specified group. The middleware is executed before any command in the group. If the response code returns `STATUS_SUCCESS`, the routing process will continue to execute other commands. If the middleware callback returns `STATUS_ERROR`, the routing process will terminate, preventing further commands from executing.

Unlike the `middleware` method, which handles only `HTTP` method authentication and accepts regular `regex` patterns, the `guard` method accepts only the command group name or `global` for global middleware security.

```php
public guard(string $group, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The command group name or default `global` for middleware that applies to all commands. |
| `$callback` | **Closure&#124;string** | The callback function or controller method to execute (e.g., `ControllerClass::methodName`). |

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - Thrown if the method is used outside a CLI context.

> **Note:** To register middleware with the group name, you must define your `guard` method within the `group` method callback closure.

***

### addNamespace

Registers an `MVC` or `HMVC` controller namespace group for application routing.

This method allows the registration of new routable namespaces, making controllers within those namespaces accessible for routing in both HMVC and MVC applications.

**Namespace Pattern:**

Register your namespace up to the controller path, excluding the `Http` and `Cli` suffixes.

- **HMVC Example**: Use `App\Modules\FooModule\Controllers\` instead of `App\Modules\FooModule\Controllers\Http`.  
  This allows the router to capture both `Http` and `Cli` namespaces under the registered group.

```php
public addNamespace(string $namespace): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$namespace` | **string** | The namespace to register (e.g., `App\Controllers\`, `App\Modules\FooModule\Controllers\`). |

**Return Value:**

`self` - Returns the instance of the router class.

**Throws:**
- [\Luminova\Exceptions\RouterException](/running/exceptions.md#routerexception) - If the namespace is empty or invalid.

**Example:**

Registering namespaces for module controllers:

This setup is typically implemented in your application class, either within the `constructor` or the `onCreate` method.

```php
// /app/Application.php
namespace App;

use Luminova\Core\CoreApplication;

class Application extends CoreApplication
{
   protected function onCreate(): void
   {
      // Register HMVC module namespaces
      $this->router->addNamespace('\\App\\Modules\\Admins\\Controllers\\');
      $this->router->addNamespace('\\App\\Modules\\Users\\Controllers\\');
   }
}
```

**Note:** For MVC and HMVC applications, base controllers are already defined in the `Luminova\Core\CoreApplication` class. Use `addNamespace` only to extend routing for additional modules or custom namespaces.

***

### setErrorListener

Set the handler context error listener for `404` errors.

```php
public setErrorListener(Closure|string|array $match, Closure|array|null $callback = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$match` | **Closure&#124;string&#124;array<int,string>** | Matching route callback or segment pattern for error handling. |
| `$callback` | **Closure&#124;string&#124;array<int,string>&#124;null** | Optional error callback handler function. |

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
public getSegment(): Luminova\Routing\Segments
```

**Return Value:**

`Segments` - Return URL segment instance

**See Also**

[View Segment Instance](/routing/url-paths.md) - Helper class to retrieve view segments.