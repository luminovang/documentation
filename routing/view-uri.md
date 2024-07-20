# URI Routing

***

## Overview

Luminova's routing module simplifies capturing, processing, and executing both web page and CLI command requests within your controller class.

***

## Introduction

Routing is a crucial part of the Luminova framework, responsible for receiving, matching, and handling URL requests to ensure the appropriate controller method is executed based on the request URL. Luminova provides an efficient routing module and methods for both `HTTP` and `CLI`  capturing, matching, command, and authenticating URI patterns with middleware upon requests. It simplifies routing initialization by using [Routing Context](/routing/view-context.md) to match the first URL prefix and load only the necessary route to execute the request. This approach ensures better performance than loading all routes before matching the required route to render.

Router handling can be done using either a `closure` or a `string` in the format like you are calling a static method (e.g, `ClassBaseName::methodName`) for view rendering.

Luminova's routing supports dependency injection, allowing you to type-hint your controller method parameters with the modules you want. By default, route dependency injection is disabled. To enable it, update your [environment configuration file](/running/env.md) with `feature.route.dependency.injection`.

### Prefer PHP Attributes?

Luminova provides a `Route` attribute, allowing you to define both `HTTP` and `CLI` routes using attributes. [Read the documentation here](/routing/attribute.md).

***

### Base Usages

In your route context file `/routes/*.php`, the `Router $router`, `BaseApplication $app` variable is already available as a global variable. Therefore, there's no need to specify the `use` keyword or import the router instance anymore except if it required.

```php 
<?php
$router->get('/',  function () use($app) {
    $app->view('index')->render([
		// Options to pass to the view
	]);
});
```

**Using Controller Class**

When passing your controller class, you only need to provide the class base name and method, without the full namespace (e.g., `ExampleController::about`) instead of `App\Controllers\ExampleController`. The namespace is already registered with the `BaseApplication` class, allowing you to pass only a controller class that extends `Luminova\Base\BaseController` by default.

```php 
<?php
$router->get('/about', 'ExampleController::about');
```

**Bind Group**

If you need to register a URI group for routing, you can use `bind` method which accepts only 2 arguments `group pattern` and `Closure`.

```php 
<?php
use \Luminova\Routing\Router;
use \Luminova\Base\BaseApplication;

$router->bind('/blog', function(Router $router, BaseApplication $app) {
	$router->get('/', 'ExampleController::blogs');
	$router->get('/([a-zA-Z0-9]+)', 'ExampleController::blog');
});
```

**Capturing any HTTP method**

The `any` method allows you to capture any HTTP method this can be useful for `API` applications.

For example `contact us page` as shown below.

```php 
<?php
$router->any('/contact', 'ExampleController::contact');
```

If you want to use a selected methods you can do that using `capture()` method.

```php 
<?php
$router->capture('POST|GET|PUT', '/contact', 'ExampleController::contact');
```

Using dependency injection type-hint.

```php
<?php
use \Luminova\Http\Request;
 
$router->get('/users', function (Request $request) {
    $name = $request->getGet('name');
});
```

***

### Command Controller

Registering command controller routing.
To register commands, you will need to first call the `group` method which is like your command base, within the group closure you can then call `command` method to register your commands associated to the group name.

```php
<?php
use \Luminova\Routing\Router;
use \Luminova\Base\BaseApplication;
use \Luminova\Command\Terminal;

$router->group("blog", function(Router $router, BaseApplication $app) {
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

* Class namespace: `\Luminova\Routing\Router`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***

## Methods

To see more usages examples read [Routing Example Documentation](/routing/examples.md).

***

### Middleware

HTTP middleware security allows you to define authentication to validate requests before allowing other routes to execute or terminate their executions based on the returned response code: `STATUS_SUCCESS` (0) or `STATUS_ERROR` (1).

To instruct the middleware to terminate or proceed with execution, your controller method or closure must return an integer as described above.

Middlewares can be registered within a `bind` method or in the global scope of the routing context. It also support defining regex patterns and methods to capture.

```php
public middleware(string $methods, string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | Allowed methods, can be serrated with &amp;#124; pipe symbol (e.g `POST|GET|OPTION`). |
| `$pattern` | **string** | A route regex pattern or template view name |
| `$callback` | **\Closure&#124;string** | Callback controller or closure to execute. |

***

### Before

The `before` method provides middleware security for `CLI` commands. It allows you to define and execute authentication before other commands are executed. If the response code returns `STATUS_SUCCESS`, the subsequent commands will be executed; otherwise, if `STATUS_ERROR` is returned, other commands will not be executed.

Unlike the `middleware` method, which handles `HTTP` middleware authentication and accepts regular `regex` patterns, the `before` method accepts only the command group name or `any` for global middleware security.

To register middleware with the group name, you must define your `before` method within the `group` method callback closure.

```php
public before(|string $group, \Closure|string $callback = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | Command group name or `any` for global middleware. |
| `$callback` | **\Closure&#124;string** | Controller callback to execute |

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
| `$methods` | **string** | Allowed methods, can be serrated with &amp;#124; pipe symbol |
| `$pattern` | **string** | A route pattern or template view name |
| `$callback` | **\Closure&#124;string** | Callback function to execute |

***

### capture

The capture method allows you to define set of methods the controller should handle based on `HTTP` request method, and `URI` patterns to look for the listed methods. Once the method and patterns match the callback controller will be executed accordingly.

```php
public capture(string $methods, string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | Allowed methods, can be separated with &amp;#124; pipe symbol (e.g. `POST|PUT|GET`). |
| `$pattern` | **string** | A route uri pattern to match methods. |
| `$callback` | **\Closure&#124;string** | Controller callback to execute. |

***

### Command

This method registers and captures command requests to execute callbacks. It is similar to the `capture` method but is intended for use only in `CLI` mode.

Unlike the `capture` method, this method does not support regular `regex` patterns. Instead, it uses a set of rules to capture command arguments.

For example, suppose you have a command group `blogs`, and in your blog command controller class, you have two methods: `list` and `get`. The `list` method accepts an `int` argument to determine the number of blogs to list, and the `get` method accepts a `string` as the blog ID. You will need to register commands with patterns like `/list/limit/(:int)` for the list method and `/get/id/(:value)` for the get method.

```php
public command(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | Command pattern or script action name |
| `$callback()` | **\Closure&#124;string** | Callback function to execute |

**Rules**

- `mixed`: Indicates that the expected parameter can be any value.
- `optional`: Indicates that the expected parameter can be blank (the parameter must be called).
- `int`: Indicates that the expected parameter must be a valid integer.
- `float`: Indicates that the expected parameter must be a valid float value.
- `string`: Indicates that the expected parameter must consist of alphanumeric characters, underscores, and hyphens.
- `alphabet`: Indicates that the expected parameter must consist of alphabetic characters only.
- `path`: Indicates that the expected parameter must be a valid file path (e.g., `/path/to/foo`).

> **Note:**
> The first segment of the pattern indicates the command name, while subsequent segments like `/foo/(:rule)/bar/(:rule)` indicate the expected parameter. Ensure to enclose the rule in brackets with a colon before the rule name.
> For commands that don't expect any parameter, you don't need to add any rule; just pass the name like `/foo`.
> 
> Execution of the command controller must follow this pattern: `php index.php <group> <command> <...argument value> <--flag>`.

***

### any

A shorthand for route capture method to handle any type of request method.

```php
public any(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | A route pattern or template view name |
| `$callback` | **\Closure&#124;string** | Handle callback for router |

***

### get

Get, a shorthand for route `capture` method to handle `GET` request method.

```php
public get(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** |  |
| `$callback` | **\Closure&#124;string** | Handle callback for router |

***

### post

Post, a shorthand for route `capture` method to handle `POST` request method.

```php
public post(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | A route pattern or template view name |
| `$callback` | **\Closure&#124;string** | Callback function to execute |

***

### patch

Patch, a shorthand for route `capture` method to handle `PATCH` request method.

```php
public patch(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | A route pattern or template view name |
| `$callback` | **\Closure&#124;string** | Handle callback for router |

***

### delete

Delete, a shorthand for route `capture` method to handle `DELETE` request method.

```php
public delete(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | A route pattern or template view name |
| `$callback` | **\Closure&#124;string** | Callback function to execute |

***

### put

Put, a shorthand for route `capture` method to handle `PUT` request method.

```php
public put(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | A route pattern or template view name |
| `$callback` | **\Closure&#124;string** | Callback function to execute |

***

### options

Options, a shorthand for route `capture` method to handle `OPTIONS` request method.

```php
public options(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | A route pattern or template view name |
| `$callback` | **\Closure&#124;string** | Callback function to execute |

***

### bind

The Bind method allow you to group a collection nested `URI`  together in a single base suffix.

For instance you have a `URL` like `http://example.com/user`, when you bind `user`, then all user related `URLs` will be in one group like `http://example.com/user/account`, `http://example.com/user/dashboard` etc.

```php
public bind(string $group, \Closure $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The binding group pattern. |
| `$callback` | **\Closure** | Callback group function to handle binds |

**Throws:**
- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - If invalid callback is provided

***

### group

The group method works similar to `bind` method, except that `group` if used only in `CLI` group binding to bind  a collection of command controllers that belong to same group.

```php
public group(string $group, \Closure $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The binding command group name. |
| `$callback` | **\Closure** | Callback command function to handle group.), are made available. |

**Throws:**
- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - If invalid callback is provided

> *Note:* Your group name must match with the defined group name in controller class.

***

### context

The `context` method enables you to define URI prefixes and error handlers for specific URI prefix names. By doing so, it ensures that only the necessary routes for handling requests are loaded based on the URL prefix, , like `api`, `console` and more. 

The `web` is the default to handle any incoming request with no unique registered context/prefix.

The flexible array arguments approach is particularly useful for scenarios with complex routing requirements or a large number of contexts. Leveraging the array approach may offer better scalability and maintainability. Choose the method that aligns best with your specific use case and coding style preferences. 

```php
public context(\Luminova\Routing\Context|array<string,mixed>|null ...$contexts): \Luminova\Routing\Router
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$contexts` | **Luminova\Routing\Context&#124;array<string,mixed>|null** | Arguments containing routing context or array of arguments.<br/>Pass `NULL` or leave blank only when using route attributes. |

**Return Value**

`Router` - Return router instance class.

**Throws:**
- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - Throws if no context arguments was passed and route attribute is not enabled.

**See Also:**

*  [Routing Context](/routing/view-context.md) - Read more on how routing prefix work and usages. 
*  [Index Controller Handler](/public/index.md) - See example of front controller index file handler. 

> *Note:* The context name must be unique based on your `URLs`, in other to discover and load only the required file.
> Additionally, mixing context arguments with `Array` and `Context` instance may produce unintended error.

***

### addNamespace

To register controller class `namespace` that router is allowed to lookup classed in.
If you wish to register more namespace this should method should ne called in your application class either within `constructor` or `onCreate` method.

```php
public addNamespace(string $namespace): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$namespace` | **string** | Class namespace string |

**Throws:**
- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - If namespace string is empty or contains invalid namespace characters.

***

### run

Run application routes, loop through registered routes and executed request methods if match is  found.
This method should be called once on `public/index.php` after calling the `context` method.

```php
public run(): void
```

**Throws:**
- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - Encounter error while executing controller callback

***

### setErrorListener

Set the handler context error listener for `404` errors.

```php
public setErrorListener(\Closure|string|array $match, \Closure|array|null $callback = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$match` | **\Closure&#124;string&#124;array<int,string>** | Match route pattern or a callback. |
| `$callback` | **\Closure&#124;string&#124;array<int,string>&#124;null** | Optional error callback handler function. |

> If the `$match` parameter is passed a callback handler and `$callback` parameter is passed `NULL`, then the handler will trigger the error callback whenever any `404` error occurred within the routing context.
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

[View Segment Instance](/routing/view-segment.md) - Helper class to retrieve view segments.