# HTTP Controller Routing

***

## Overview

Learn how to define routes in Luminova using view controller methods or closures, with simple static or notation-style references for APIs or Website requests handling.

***

## Introduction

The **HTTP Routing** documentation focuses exclusively on **HTTP controllers routing**, designed for handling **API** and **Web requests**. Each controller method is mapped to an HTTP verb (e.g., `GET`, `POST`, `PUT`, `DELETE`), making route definitions clean, predictable, and easy to maintain. This structure ensures every request type is clearly defined in your controller.

---

### URI Prefixing

It is recommended to define **custom controller prefix exclusions on the main entry controller**.

* Ensures **all URLs**, including invalid ones, are routed to this controller, except for explicitly excluded paths.
* Centralizes routing logic, avoiding duplication or conflicts across multiple controllers.
* Makes it easy to maintain and extend exclusions in a single place.

**Using an exclusions list:**

```php
#[Prefix('/(:base)', exclude: ['api', 'blog', 'admin'])]
class MainController extends Controller {}
```

**Equivalent using a raw pattern:**

```php
#[Prefix(pattern: '/(:base)(?!api(?:/|$)|blog(?:/|$)|admin(?:/|$)).*')]
class MainController extends Controller {}
```

In both cases, the **main controller handles all requests** except the excluded paths.
This approach ensures **clean, centralized routing** for the application entry point.

For more details on [Prefixing](/attributes/uri-prefix.md), see the documentation.

---

### Response Content Caching

Luminova supports **template content caching** to speed up responses and reduce server load by serving pre-rendered pages instead of regenerating them for every request.

It also offers **version-based caching**, which is useful for applications like a documentation or multi-version sites. For example:

```
https://example.com/1.1.0/about
```

will load content from the cache for version **1.1.0**, if available.

Learn how to enable and configure view caching in the [View Caching Guide](/cache/static-html-cache.md).

---

## Usages

### Controller Method Handlers

When defining route handlers, you can use either a **Closure** or a **string reference** pointing to a controller method. Luminova supports:

* **Static-style** references (`ControllerName::method`)
* **Notation-style** references (`ControllerName@method`)

You **don’t need to type the full namespace** — just the controller’s base name and method.
The routing system automatically resolves the full namespace from the registered controller list.

> **Note:**
> In your `/routes/<context>.php` file, the objects
> `Luminova\Routing\Router $router` and `App\Application $app` are automatically available.
> This means you don’t need to import them using `use` unless required (for example, when using `bind()` ).

---

#### Examples

**Static-style reference**

```php
// /routes/web.php

$router->get('/home', 'HomeController::index');
```

**Notation-style reference**

```php
// /routes/web.php

$router->get('/home', 'HomeController@index');
```

**Closure handler**

```php
// /routes/web.php

$router->get('/home', function () {
    // Handle request logic here
});
```

**Rendering a template from a Closure**

```php
// /routes/web.php

$router->get('/', function (Application $app) {
    return $app->view('index')->render([
        'foo' => 'Bar' // Variables passed to the view
    ]);
});
```

---

**Bind Group**

If you need to register a URI group for routing, you can use `bind` method which accepts only `2` arguments `group pattern` and `Closure`.

```php
// /routes/web.php

use Luminova\Routing\Router;
use Luminova\Foundation\Core\Application;

$router->bind('/blog/(:root)', function(Router $router, Application $app) {
   $router->middleware('GET', '/(:root)', 'Authentication::isAllowed');
   $router->get('/', 'ExampleController::blogs');
   $router->get('/(:alphanumeric)', 'ExampleController::blog');
});
```

See for more information related to [Dynamic URI Placeholders](/routing/dynamic-uri-placeholder.md) documentation.

---

### Any HTTP Method

The **any** method lets you handle requests regardless of whether they’re `GET`, `POST`, `PUT`, or any other HTTP verb. This is useful for pages like **Contact Us** where you want one handler for all request types, or when debugging routes.

**Example using `any`:**

```php
// /routes/web.php

$router->any('/contact', 'ExampleController::contact');
```

You can achieve the same result with the `capture` method using `Router::ANY_METHODS` or simply `"ANY"`:

```php
// /routes/web.php

$router->capture('ANY', '/contact', 'ExampleController::contact');
```

You can also use `capture` to handle only specific methods in a single route:

```php
// /routes/web.php

$router->capture('POST|GET|PUT', '/contact', 'ExampleController::contact');
```

---

### Dependency Injection

Luminova routing can automatically inject dependencies into controller methods or route callbacks. Just type-hint the class you need, and the framework will provide it for you.

**Example using a controller method:**

```php
// /app/Controllers/Http/ExampleController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Http\Request;

class ExampleController extends Controller
{
    public function userInfo(Request $request): int 
    {
        $name = $request->getGet('name');
        // Your logic here
    }
}
```

**Example using a closure callback:**

```php
// /routes/web.php

use Luminova\Http\Request;

$router->get('/users', function (Request $request) {
    $name = $request->getGet('name');
    // Your logic here
});
```

In both cases, the `Request` object is injected automatically, so you can access query parameters, POST data, and more without manually creating it.

---

## Class Definition

* Class namespace: `Luminova\Routing\Router`
* This class implements: [Luminova\Interface\RouterInterface](/interface/classes.md#routerinterface)
* This class is marked as **final** and can't be subclassed

***

## Methods

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
- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - Thrown if the method is called in non-HTTP context or the `$methods` parameter is empty.

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
- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - Thrown if the `$methods` parameter is empty.

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
- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - Thrown if an empty method string is provided.

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

### onError

Set a custom error handler for a specific route or globally.

You can assign either a callable array handler, controller handler or a closure as the error handler.

```php
public onError(Closure|string|array $pattern, Closure|array|null $handler = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **Closure\|string\|array** | A global error handler or URI patterns to register with `$handler`.<br/>For global error handler set callback or controller for error handler. |
| `$handler` | **Closure\|string\|array\|null** | An error callback handler or controller handler, set null for global error handler. |

**Throws:**
- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - if `$handler` is provided but `$pattern` is not a valid segment pattern.

> **Using Pattern as Global**
> - If specifying a URI, provide a string pattern or a controller callback `[ControllerClass::class, 'method']`.
> - If no URI is needed (global), provide only a closure or controller callback.

**Examples:**

```php
// Global error handler
$router->onError([ErrorController::class, 'onWeError']);

// Specific URI error handler
$router->onError('/users/', [ErrorController::class, 'onWeError']);

// Using a closure
$router->onError('/admin', function() {
    // handle error
});
```

***

### trigger

Trigger an HTTP error response and immediately halt route processing.

This method is called when no matching route is found, or when a request 
must return a specific HTTP status code (e.g., 404, 500). It attempts to 
delegate error handling in the following order:

1. If `ErrorController::onTrigger()` exists, it is called directly.
2. If a matching route-specific error handler is registered, that handler is executed.
3. If a global (`'/'`) error handler is registered, that handler is executed.
4. If no handler is found, a default error page is displayed.

```php
public static trigger(int $status = 404): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP status code to trigger (default: `404`) |

> This method can be used anywhere in controller class `$this->app->router->trigger()` or routing context `$router->trigger();`.
>
> This method clears output buffers to ensure no partial output is sent before the error response is generated. Once an error response is sent, execution stops immediately.

***

### getUriSegments

Get the current segment URI.

```php
public getUriSegments(): string
```

**Return Value:**

`string` - Return relative paths.

***

### getSegment

To access more information about the requested `URI` segments.

```php
public getSegment(): Luminova\Routing\Segments
```

**Return Value:**

`Segments` - Return URL segment instance

**See Also**

[Request URI Segment Object](/routing/url-paths.md) - Helper class to retrieve request URI segments.