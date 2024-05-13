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
$router->bind('/blog', function(Router $router) {
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
$router->group("group", function(Router $router) {
    $router->command("test", 'Command::test');
    $router->command('/foo/name/(:value)', 'Command::foo');
});
```

see [Routing Example](/routing/examples.md) for more usages examples.

***

* Class namespace: `\Luminova\Routing\Router`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***

## Methods

***

### middleware

HTTP middleware, it captures the front controller request method and patterns to handle middleware authentication before executing other routes.

```php
public middleware(string $methods, string $pattern, \Closure|string $callback): void
```

If middleware callback returns "STATUS_ERROR" the execution will be terminated
resulting the following to be ignored as a result of failed authentication else if it return "STATUS_SUCCESS" the following routes will be executed.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | Allowed methods, can be serrated with &amp;#124; pipe symbol |
| `$pattern` | **string** | A route pattern or template view name |
| `$callback` | **\Closure&#124;string** | Callback function to execute |

***

### before

CLI middleware, it captures the front controller request method and patterns to handle middleware authentication before executing other routes.

```php
public before(\Closure|string $pattern, \Closure|string $callback = null, array $options = []): void
```

If middleware callback returns "STATUS_ERROR" the execution will be terminated
resulting the following to be ignored as a result of failed authentication else if it return "STATUS_SUCCESS" the following routes will be executed.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **\Closure&#124;string** | Allowed command pattern, script name or callback function |
| `$callback` | **\Closure&#124;string** | Callback function to execute |
| `$options` | **array** | Optional options |

***

### after

After middleware route, executes the callback function after request was executed successfully.

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

Capture front controller request method based on pattern and execute the callback.

```php
public capture(string $methods, string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | Allowed methods, can be separated with &amp;#124; pipe symbol |
| `$pattern` | **string** | A route pattern or template view name |
| `$callback` | **\Closure&#124;string** | Callback function to execute |

***

### command

Capture front controller command request names and execute callback

```php
public command(string $pattern, \Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | Command pattern or script action name |
| `$callback()` | **\Closure&#124;string** | Callback function to execute |

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

Get, a shorthand for route capture method to handle "GET" request method.

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

Post, a shorthand for route capture method to handle "POST" request method.

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

Patch, a shorthand for route capture method to handle "PATCH" request method.

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

Delete, a shorthand for route capture method to handle "DELETE" request method.

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

Put, a shorthand for route capture method to handle "PUT" request method.

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

Options, a shorthand for route capture method to handle "OPTIONS" request method.

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

Binds a collection of URI and its nested group in a single base route.

```php
public bind(string $group, \Closure $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The binding group pattern. |
| `$callback(Router $router, BaseApplication $app)` | **\Closure** | Callback group function to handle binds |

**Throws:**
- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - If invalid callback is provided

***

### group

Binds a collection of command route group.

```php
public group(string $group, \Closure $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The binding group name. |
| `$callback(Router $router, BaseApplication $app)` | **\Closure** | Callback command function to handle group |

**Throws:**
- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - If invalid callback is provided

> Your group name must match with the controller group name for command to succeed.
> 
> And you must define your group name in controller.

***

### bootstraps

Bootstrap application routing context like [web, cli, api, console etc.

```php
public bootstraps(\Luminova\Base\BaseApplication $application, \Luminova\Routing\Bootstrap $callbacks): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$application` | **\Luminova\Base\BaseApplication** | Your application instance. |
| `$callbacks` | **\Luminova\Routing\Bootstrap** | Routing context callback arguments. |

**See Also:**

*  [Context Bootstrap](/bootstrap/Bootstrap.md) - Routing context Bootstrapping 
*  [Index Controller File](/public/index.md) - Front controller handler. 

***

### addNamespace

Register a controller class namespace to use across the application routing

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

Run application routes, loop all defined routing methods to call controller
if method matches view  or command name.

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

Set the handler callback to listen for 404 or invalid view errors.

```php
public setErrorListener(\Closure|string|array $match, \Closure|array|null $callback = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$match` | **\Closure&#124;string&#124;array<int,string>** | Match route pattern or a callback. |
| `$callback` | **\Closure&#124;array<int,string>&#124;null** | Optional error callback handler function. |

> If the first parameter is passed a callback and second parameter is passed `NULL`.
> 
> The callback will be triggered on any `404`  error within its context.

***

### triggerError

Use this method to manually  trigger an error response.

```php
public triggerError(int $status = 404): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP response status code (default: 404) |

***

### getBase

Return server base path.

```php
public getBase(): string
```

**Return Value:**

Application router base path

***

### getSegment

Get the requested URI segment class instance.

```php
public getSegment(): \Luminova\Routing\Segments
```

**Return Value:**

`Segments` - Return URL segment instance

**See Also**

[View Segment Instance](/routing/view-segment.md) - Helper class to retrieve view segments.