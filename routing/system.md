# HTTP and CLI Routing System 

***

## Overview

The Luminova routing system helps your app handle web and CLI requests by linking each URL or command to the right controller and method.

***

## Introduction

The **Routing System** is a core part of Luminova Framework. It dispatches incoming requests to the correct controller and handler method, ensuring the intended response is presented to client. It also handles **static cache delivery** as well as **request-level middleware**, making it easy to run checks (like authentication) before a request reaches controller or being handled.

It works with both:

* **HTTP controllers [Luminova Base Controller](/controllers/http-controller.md)**
* **CLI command controllers [Luminova Base Command](/controllers/cli-command-controller.md)**

The router only loads the **relevant controller or route context** based on the request **URI prefix** or **command group**, then dispatches to the correct method. Middleware is applied automatically when required.

---

### URI Prefixing

URI prefixes helps the routing system to load **only the routes that matter** for a specific request instead of scanning every controllers, this makes routing faster and more efficient.

You can configure prefixes in two ways:

* **[Method-Based Request URI Prefix](/routing/uri-prefix.md):**
  Group routes under a shared prefix and store them in a dedicated file:
  `routes/<URI-Prefix>.php`

* **[Attribute-Based Request URI Prefix](/attributes/uri-prefix.md):**
  Use the **Prefix** attribute to define allowed prefixes directly on a controller class.

---

## Method Return Types

The Luminova routing system supports **two types of return values** for controller handler-methods: **Integer status codes** and **Response Objects**.

### 1. Integer Response Type

This is the simplest pattern, especially when:

* Using the [Template View Class](/templates/views.md) to render templates which required `int` return type.
* Writing CLI (command-line) controllers.

The routing system uses special constants to indicate whether a controller method or middleware passed, failed, or handled its own output.

#### For HTTP Controllers

* **`STATUS_SUCCESS`**

  * *In a regular method*: The request was handled successfully.
  * *In a middleware*: Authentication or checks passed — routing continues.

* **`STATUS_ERROR`**

  * *In a regular method*: Something failed — routing triggers an error response.
  * *In a middleware*: Authentication failed — routing stops immediately.

* **`STATUS_SILENCE`**

  * *In a regular method*: The request was handled internally (e.g., custom error page shown).
  * *In a middleware*: Treated as failed authentication — routing stops. *(Use `STATUS_SUCCESS` or `STATUS_ERROR` instead for middleware.)*

---

#### For CLI Controllers

Routable methods in CLI controllers can return an integer exit code:

* **`STATUS_SUCCESS`**

  * *In a regular method*: The command was recognized and executed.
  * *In a middleware*: Authentication passed — command continues.

* **`STATUS_ERROR`**

  * *In a regular method*: The command was invalid or failed.
  * *In a middleware*: Authentication failed — command stops.

---

### 2. Response Object Type

The routing system also supports returning a **`Luminova\Template\Response`** object or any class that implements **`Luminova\Interface\ViewResponseInterface`**. or **`Psr\Http\Message\ResponseInterface`**

This is useful when:

* You need full control over output (HTML, JSON, files).
* You’re working with JavaScript frameworks like React, Vue, Next.js, or Nuxt.

**Example:**

```php
// /app/Controllers/Http/*
// /app/Modules/Controllers/Http/*

use Luminova\Template\Response;

public function myMethod(): Response
{
  return new Response(200, '<h1>Hello World!</h1>');
}
```

---

## Configurations

The Luminova routing system comes with several optional features. You can turn them on or off in your [Environment Configuration](/global/env-variables.md) (`.env`) file depending on your needs.

---

### Dependency Injection

You can use **dependency injection** in your routable controller methods.
If you type-hint a class in your method parameters, Luminova will automatically provide an instance of that class when the method is called.

By default, this feature is **off**. To enable it, add this to your env:

```ini
feature.route.dependency.injection = enable
```

For managing dependency injections and dynamic arguments, see the [Dependency Injection System](/routing/dependency-injection.md) documentation.

---

### PHP Attribute Routing

Luminova supports `Route`, `Prefix`, `Error`, and `Group` attributes that allows you to define routes directly inside your **HTTP** or **CLI** controllers.
This approach is cleaner and easier to maintain than defining routes manually.

* `Route` — define individual method routes.
* `Prefix` and `Error` — apply to entire HTTP controller classes.
* `Group` — apply to CLI command classes.

For complete examples and usage details, see the [Route Attribute](/attributes/route.md) documentation.

---

### Caching Attributes

For better performance in production, you can cache route attributes so they are not re-parsed on every request:

```ini
feature.route.cache.attributes = enable
```

> **Note:**
> 
> When caching is enabled, new or modified routes won’t take effect until you clear the cache.
> To apply changes, manually delete cached files from:
> `/writeable/caches/routes/*` or based on prefix directory as needed.

---

### Disabling Attribute Routing

Attribute routing is **enabled by default**.
If you prefer to define routes manually, you can turn it off:

```ini
feature.route.attributes = disable
```

***

### Dynamic Segment Capturing

**URI Segment capturing** allows you to extract parts of the request URI and pass them as arguments to your controller methods for further processing.  

There are several ways to define capture patterns:
  
* **Predefined Pattern Placeholders (Recommended)**
  Example: `/foo/(:string)/(:int)`
  These use built-in placeholders with automatic type validation.
	
* **Regular Expressions (Advanced)**
  Example: `/foo/([a-zA-Z0-9]+)/([0-9]+)`
  Gives full control over matching but requires more complexity.
	
* **Named Placeholders (Basic)**
  Example: `/foo/{title}/{id}`
  Easier to read, but with less validation control.
	
While **regular expressions** provide maximum flexibility, **predefined** and **named placeholders** offer a more convenient and readable approach, making them especially ideal for beginners and maintaining cleaner route definitions.

> **Note:**
> 
> **Named placeholders** are less secure and not strictly validated.
> They match any value between `/` characters until the next `/`, so use them only when precise validation isn’t critical.

**Registering a Custom Placeholder**

Define reusable regular expression patterns that behave like **predefined placeholders**, allowing cleaner and more maintainable route definitions.

```php
Router::pattern('slug', '[a-z0-9-]+');
```

Once registered, you can use it in your routes just like any built-in placeholder.

For full details, see the [Dynamic URI Placeholder Pattern](/routing/dynamic-uri-placeholder.md) documentation.

---

### Controller Class Handlers

When defining route handlers, you can use either a **Closure** or a **string reference** pointing to a controller method. Luminova supports:

* **Static-style** references (`ControllerName::method`)
* **Notation-style** references (`ControllerName@method`)

You **don’t need to type the full namespace** (e.g, `App\Controllers\Http\WebController` or `App\Controllers\Cli\CommandController`), just the controller’s base name and method.
The routing system automatically resolves the full namespace from the registered controller list.

---

## Class Definition

* Class namespace: `Luminova\Routing\Router`
* This class implements: [Luminova\Interface\RouterInterface](/interface/classes.md#routerinterface)
* This class is marked as **final** and can't be subclassed

***

### Implementations

* **HTTP Controllers:**

  * Learn how to handle web requests and responses in the [HTTP Routing Guide](/routing/http.md).

* **CLI Command Controllers:**

  * Learn how to define and route console commands in the [CLI Routing Guide](/routing/cli.md).

---

## Methods

### pattern

Register a custom route placeholder with an optional grouping mode.

Allows you to replace placeholders like `(:slug)` with your own regex. You may choose whether the pattern should be raw, non-capturing, or capturing. This makes your routing clean, by providing alias to a long pattern or repeatable patterns.

> **Important:**
>
> Do **not manually include outer start, end, or delimiter characters** (e.g., `^`, `$`, `#`, `/`, `~`, etc.).
> The routing engine automatically appends them as needed when building the final route regex.

```php
public static pattern(string $name, string $pattern, ?int $group = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | Placeholder name (e.g. "slug"). |
| `$pattern` | **string** | Regular expression pattern (e.g `[a-z0-9-]+`) |
| `$group` | **int\|null** | Grouping mode: <br/>- `null`: use pattern as-is.<br/>- `0`: wrap as non-capturing group `(?:pattern)`.<br/>- `1`: wrap as capturing group `(pattern)`.<br/>If the pattern already starts with `(`, no extra wrapping is applied. |

**Throws:**
- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - If empty placeholder name was provided or reserved names (e.g, `root`, `(:root)`, `base`, `(:base)`) was used as placeholder name.

**Examples:**

```php
Router::pattern('slug', '[a-z0-9-]+');         // raw pattern
Router::pattern('slug', '[a-z0-9-]+', 0);      // non-capturing (?:[a-z0-9-]+)
Router::pattern('slug', '[a-z0-9-]+', 1);      // capturing ([a-z0-9-]+)
Router::pattern('custom', '([a-z]+)-(\d+)', 1); // stays unchanged
```

**Usages:**

Using attribute routing.

```php
// /routes/
#[Luminova\Attributes\Route('/blog/(:slug)', methods: ['GET'])]
public function blog(string $slug): int 
{
  // 
}
```

Using method-based routing.

```php
// /routes/
$router->get('/blog/(:slug)', 'BlogController::view');
```

---

### addNamespace

Registers an **MVC or HMVC** controller namespace group for application routing.

This method allows the registration of new routable namespaces, making controllers within those namespaces accessible for routing in both HMVC and MVC applications.

**Namespace Pattern:**

Register your namespace up to the controller path, excluding the `Http` and `Cli` suffixes.

- **HMVC Example**: Use `App\Modules\Blog\Controllers\` instead of `App\Modules\Blog\Controllers\Http`.  
  This allows the routing system to capture and resolve both `Http` and `Cli` namespaces under the registered group.

```php
public addNamespace(string $namespace): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$namespace` | **string** | The namespace to register (e.g., `App\Controllers\`, `App\Modules\Blog\Controllers\`). |

**Return Value:**

`self` - Returns the instance of the router class.

**Throws:**
- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - If the namespace is empty or invalid.

**Example:**

Registering namespaces for HMVC module controllers:

This setup is typically implemented in your application class, either within the `_construct` or the `onCreate` method.

```php
// /app/Application.php
namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;

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

> **Note:** 
> 
> For **MVC and HMVC** applications, the default root controllers are already defined in the `Luminova\Foundation\Core\Application` class. 
> Use `addNamespace` only to extend routing for additional custom **HMVC module** controllers.

---

### context

Registers the application routing context based on URI prefixes (e.g., `web`, `cli`, `api`, `console`) or custom prefix names.

The `context` method is an essential part of Luminova’s routing optimization strategy. It initializes and loads routes based on a given URI prefix, allowing the framework to load only the relevant route files (like `routes/web.php`, `routes/api.php`, or `routes/cli.php`) at runtime. This method improves performance by avoiding loading all routes at once.

If no specific prefix is matched, the default `web` context handles the request.

The `context` method also supports defining custom error handlers for each prefix, similar to using attribute-based routing. It accepts a flexible input: either a `Luminova\Routing\Prefix` object or an array describing the prefix and error handler. If you're using PHP attributes to define routes, simply leave it blank.

> **Tip:** 
> 
> Using an array for context definition is particularly useful in large applications with many route groups. It helps improve maintainability and scalability.

```php
public context(Luminova\Routing\Prefix|array<string,mixed> ...$contexts): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$contexts` | **Luminova\Routing\Prefix&#124;array<string,mixed>** | Optional arguments containing routes prefix object, an array of prefix or leave blank when using attribute routing. |

**Return Value**

`self` - Return router instance class.

**Throws:**
- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - Thrown if no context arguments are passed and route attributes are not enabled.

> **Note:**  
> The prefix name must be unique and reflect your URL structure accurately.  
> Avoid mixing `Prefix` objects and array definitions in the same call, as it may lead to unintended errors.

**See Also:**

- [Method-Based Routing URI Prefixing](/routing/uri-prefix.md) — How prefixes work and how to configure them.
- [Front Controller Setup](/boot/public.md) — Example of setting up the main application handler.

***

### run

Execute application routes and handle incoming requests.

This method processes all defined routes and dispatches incoming **HTTP** or **CLI** requests to the appropriate 
controller methods. It also finalize application profiling, ensuring computed profiling data is sent to the UI for debugging and triggers the `onFinish` application event before termination.

```php
public run(): void
```

**Throws:**
- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) – If an error occurs during request processing or route resolution.

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

// Initialize context and run the routing system
Boot::http()->router->context(...)->run();
```

> **Note:** 
> This method is typically invoked once in the (`/public/index.php`) file, which serves as the application front controller.