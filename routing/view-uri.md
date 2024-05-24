# URI Routing

***

## Overview

Utilize the Luminova router class to seamlessly capture and process both API and web page requests within your controller class.

***

## Introduction

Luminova's framework routes serve as efficient handlers for capturing URI patterns upon requests. They simplify routing configurations by enabling the definition of URI matching patterns, methods, and closures for view rendering.

In your route context file `/routes/*.php`, the `$router`, `$app` variable is already set as a global variable. Therefore, there's no need to specify the use keyword or import the router instance anymore.

```php 
<?php
$router->get('/', function () use($app) {
    $app->view('index')->render();
});
```

**Controller Class**

When passing your controller class, you only need to provide the class name, without the full namespace (e.g., `ExampleController`) instead of `App\Controllers\ExampleController`. The namespace is automatically registered, allowing you to pass only a controller class that extends `Luminova\Base\BaseController` by default.

```php 
<?php
$router->get('/about', 'ExampleController::about');
```

**Bind Group**

If you need to register a URI group for routing, you can use `bind` method which accepts only 2 parameters `group name` and `Closure`.

```php 
<?php
use \Luminova\Routing\Router;
use \Luminova\Base\BaseApplication;

$router->bind('/blog', function(Router $router, BaseApplication $app) {
	$router->get('/', 'ExampleController::blogs');
	$router->get('/([a-zA-Z0-9]+)', 'ExampleController::blog');
});
```
**Capture and HTTP method**

The `any` method allows you to capture any HTTP method for `contact us page` as shown below.

```php 
<?php
$router->any('/contact', 'ExampleController::contact');
```
If you want to use a selected methods you can do that using `capture()` method.

```php 
<?php
$router->capture('POST|GET|PUT', '/home', 'ExampleController::home');
```

Dependency injection.

```php
<?php
use \Luminova\Http\Request;
 
$router->get('/users', function (Request $request) {
    $request->getGet('name');
});
```

### Controller Command

Registering command controller routing.

```php
<?php
use \Luminova\Routing\Router;
use \Luminova\Base\BaseApplication;
use \Luminova\Command\Terminal;

$router->group("group", function(Router $router, BaseApplication $app, Terminal $term) {
    $router->command("test", 'Command::test');
    $router->command('/foo/name/(:value)', 'Command::foo');
});
```

[Routing Example](/routing/examples.md) see examples for more usages.

***

* Class namespace: `\Luminova\Routing\Router`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***

## Methods

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
public context(\Luminova\Routing\Context|array<string,mixed> ...$contextes): \Luminova\Routing\Router
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$contextes` | **Luminova\Routing\Context&#124;array<string,mixed>** | Arguments containing routing context configuration. |

**Return Value**

`Router` - Return router instance.

**Throws:**
- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - Throws if no context arguments was passed.

**See Also:**

*  [Routing Context](/routing/view-context.md) - Read more on how routing prefix work and usages. 
*  [Index Controller Handler](/public/index.md) - See example of front controller index file handler. 

> *Note:* The context name must be unique based on your `URLs`, in other to discover and load only the required file.
> Additionally, mixing context arguments with `Array` and `Context` instance may produce unintended error.

***

### addNamespace

To register controller class `namespace` that router is allowed to lookup classed in.

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

```php
public run(\Closure|null $callback = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **\Closure&#124;null** | Optional final callback function to execute after run |

**Throws:**
- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - Encounter error while executing controller callback</p>

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