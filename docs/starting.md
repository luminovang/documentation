# Complete Changelog

***

## Overview

Provides a detailed record of all changes in Luminova's latest version, updates, and improvements made to the Luminova Framework over time.

***

## Introduction

Version **3.6.8** brings a major cleanup across Luminova’s core. Many parts were renamed, optimized, or fixed, and several new helper classes were added, including **Password**, **String**, and the improved error-handling system. The template view engine and the core exception layer also received substantial tuning.

Default HTTP error templates now support grouped pages using `4xx.php` and `5xx.php`, replacing the old `404.php` and `500.php` pattern.

To begin migrating to 3.6.8, see the [Migration Guide](/introduction/migration-guide.md).

---

## Fixes

#### Added in Version 3.6.8

### [Routing System](/routing/system.md)

Fixed an issue where routes defined with HTTP `HEAD` method were incorrectly returning 404 errors.

---

## New Features

#### Added in Version 3.6.8

### [Base HTTP Controller](/controllers/http-controller#lmv-docs-properties)

Introduced a new protected `$view` property, giving controllers direct access to a lazily initialized instance of `Luminova\Template\View` for template management.

```php
$this->view->view('home')->render();

// Or directly from the application class
$this->app->view->view('home')->render();
```

---

#### HTTP Controller Methods

HTTP controller routable methods can now return `Psr\Http\Message\ResponseInterface`, `Luminova\Interface\ViewResponseInterface`, or the traditional `int`.

**Example**

Using **`Luminova\Template\Response`** class.

```php
use Luminova\Attributes\Route;
use Luminova\Interface\ViewResponseInterface;
use function Luminova\Funcs\response;

#[Route('/api/hello', methods: ['GET'])]
public function hello(): ViewResponseInterface
{
  return response()->content([
    'message' => 'Hello world'
  ]);
}
```

**With middleware authentication:**

If you want to return default content when middleware fails, otherwise return `STATUS_ERROR`:

```php
use Luminova\Attributes\Route;
use Luminova\Interface\ViewResponseInterface;
use function Luminova\Funcs\response;

#[Route('/api/(:root)', methods: ['ANY'], middleware: Route::HTTP_BEFORE_MIDDLEWARE)]
public function middleware(): ViewResponseInterface|int
{
    if (OK) {
        return STATUS_SUCCESS;
    }

    return response()->failed()->content(
        ['message' => 'Access denied'], 
        ['Content-Type' => 'application/json']
    );
}
```

---

Using **`Psr\Http\Message\ResponseInterface`** to return default content when middleware fails. 

When middleware fails, set response status code to `401` or `500`, this way the routing system will terminate execution immediately.

```php
use Luminova\Attributes\Route;
use Luminova\Http\Message\Response;
use Psr\Http\Message\ResponseInterface;

#[Route('/api/(:root)', methods: ['ANY'], middleware: Route::HTTP_BEFORE_MIDDLEWARE)]
public function middleware(): ResponseInterface|int
{
  if (OK) {
      return STATUS_SUCCESS;
  }

  return new Response(
    json_encode(['message' => 'Access denied']), 
    401, // Indicate that middleware has failed
    ['Content-Type' => 'application/json']
  );
}
```

---

### [Routing System](/routing/system#lmv-docs-pattern)

Added a new `pattern` method to define and manage custom URI segment placeholders.
This allows you create reusable patterns for route parameters, improving maintainability and reducing repetition of patterns.

```php
// In App\Application::onCreate()
Router::pattern('slug', '[a-z0-9-]+');  

// In App\Controllers
#[Luminova\Attributes\Route('/blog/(:slug)', methods: ['GET'])]
public function blog(string $slug): int 
{
  // Handle blog route using the slug value
}
```

> **Note:** 
> Once defined, the `slug` pattern can be reused across multiple routes, ensuring consistent validation.

---

### [Password Helper](/security/password.md)

A new `Password` helper has been added to handle all password-related operations in one place.

Now:

```php
use Luminova\Security\Password;

$hash = Password::hash('pass');
Password::verify('pass', $hash);

// Additional utilities:
Password::rehash('pass', $hash);
Password::strength('pass');
Password::isCompromised('pass');

// and more...
```

Before:

```php
Crypter::password('pass');
Crypter::isPassword('pass', 'hash');
```

---

### [Proxy Helper](/utilities/proxy-tool.md)

A dedicated `Proxy` helper is now available for checking and validating HTTP/SOCKS proxies.

Now:

```php
use Luminova\Utility\Proxy;

$proxy = '';
Proxy::validate($proxy);
Proxy::ping($proxy);

// and more...
```

---

### [String Helper](/utilities/string-object.md)

A new `Str` helper provides object-based string manipulation features.

Now:

```php
use Luminova\Utility\String\Str;

$str = Str::of('peter');

echo $str->length();         // 5
echo $str->toUpperCase()
         ->trim();           // PETER

// and more...
```

---

### [Core Error Monitoring System](/error-handlers/error-monitoring-system.md)

Added `Luminova\Errors\Monitor` for advanced error detection and `Luminova\Errors\Message` for structured error messages. This system improves how Luminova applications detect and respond to critical issues.

---

### [Exception Error Codes](/error-handlers/error-codes.md)

Added `Luminova\Exceptions\ErrorCode` to centralize exception and error codes. Key methods include:

* `getName` – Retrieve a descriptive name for an error code.
* `has` – Check if an error or exception code is recognized.

---

### [Abstract Base Exception Class](/error-handlers/exceptions.md)

Introduce new methods:

- `getDescription` - Return a formatted exception message without any private file path included.
- `isCode` method for conveniently checking if the current exception code matches one or more codes.
- `trace` Get the file and line of a specific call depth where the method was called.

**Example:**

```php
use Luminova\Exceptions\ErrorCode;
use Luminova\Exceptions\AppException;
use Luminova\Exceptions\RuntimeException;

try {
    throw new RuntimeException('Error', ErrorCode::NOT_SUPPORTED);
} catch (AppException $e) {
  if ($e->isCode(ErrorCode::DATABASE_ERROR)) {
    // Handle single code
  }

  if ($e->isCode([ErrorCode::NOT_ALLOWED, ErrorCode::NOT_SUPPORTED])) {
    // Handle multiple codes
  }
}
```

---

### [Template View Class](/templates/views.md)

The `Luminova\Template\View` class now includes additional methods:

* `info` – Get metadata about a template file without rendering.
* `exists` – Check if a template file exists without rendering.
* `setUriPathDepth` – Configure the relative URI parent directory depth.

These method can be used directly inside template files:

* `hasOption` – Check if a context option is available in the view.
* `isExported` – Check if a property is exported to the template scope.
* `getExports` – Retrieve all exported properties available to the template.
* `getExport` – Retrieve a specific property exported to the template scope.
* `getOptions` – Get all context options for the view.
* `getOption` – Retrieve a single context option by name.
* `getTemplate` – Get the resolved or specified template filename.
* `getStatusCode` – Get the HTTP status code used during rendering.

---

### [Backend Session Class](/sessions/session.md)

The `Session` class now supports role-based access control, with methods for finer-grained permission management:

* **`roles(array $roles)`**
  Assign user roles to the session.

* **`getRoles(): array`**
  Retrieve all roles currently stored in the session.

* **`guard(array $roles, int $mode = Session::GUARD_ANY, ?callable $onDenied = null): bool`**
  Validate roles against access rules. Returns `true` if access is denied (fail-fast pattern), `false` if access is granted.

* **`toSessionId(string $id, string $algo = 'sha256')`**
  Convert a string into a valid PHP session ID.

  **Example:**

  Convert a system identifier into a valid PHP session ID for persistent CLI logins.

  ```php
  use Luminova\Command\Terminal;
  use Luminova\Sessions\Session;

  $sid = Session::toSessionId(Terminal::getSystemId());
  ```

  **Supported Guard Modes:**

  * `Session::GUARD_ANY` (default): Access granted if at least one role matches.
  * `Session::GUARD_ALL`: Access granted only if all roles match.
  * `Session::GUARD_EXACT`: Access granted only if all and *only* the specified roles match.
  * `Session::GUARD_NONE`: Access granted only if none of the roles are present.

**Example:**

```php
if ($session->guard(['admin', 'moderator'], Session::GUARD_ALL)) {
    throw new AccessDenied('Insufficient privileges.');
}
```

---

### [PHP Route Attribute](/attributes/route.md)

A new `$aliases` property has been added to the `Route` attribute to define multiple alternative URI patterns for the same controller method.

**Before (using multiple attributes)**

Previously, to map several URIs to the same method, you had to repeat the `Route` attribute:

```php
// /app/Controllers/Http/ExampleController.php

use Luminova\Attributes\Route;

#[Route('/', methods: ['GET'])]
#[Route('/home', methods: ['GET'])]
#[Route('/default', methods: ['GET'])]
#[Route('/dashboard', methods: ['GET'])]
public function home(): int 
{
  return $this->view('index');
}
```

**Now (using aliases)**

You can achieve the same result with a **single attribute**, improving readability and reducing duplication:

```php
// /app/Controllers/Http/ExampleController.php

use Luminova\Attributes\Route;

#[Route('/', methods: ['GET'], aliases: [
  '/home',
  '/default'
  '/dashboard'
])]
public function home(): int 
{
  return $this->view('index');
}
```

---

### [PHP Prefix Attribute](/attributes/uri-prefix.md)

The `Prefix` attribute now supports a new `$exclude` property that allow you to specify URI prefixes to exclude from controller matching. This removes the need for complex negative-lookahead patterns.

**Before (manual regex exclusions)**

```php
// /app/Controllers/Http/MainController.php

use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/(?!api(?:/|$)|admin(?:/|$)).*')]
class MainController extends Controller
{
}
```

**Now (using the built-in exclude list)**

```php
// /app/Controllers/Http/MainController.php

use Luminova\Attributes\Prefix;

#[Prefix(pattern: '/(:base)', exclude: [
    'api',
    'admin'
])]
class MainController extends Controller
{
}
```

---

### [Global Helper Functions](/global/functions.md)

Added new global helper functions for improved response handling:

* **`redirect`** – Sends an HTTP redirect using the current `Response` instance.
  Supports both `Location` and `Refresh` headers and automatically terminates execution after sending.

  **Example:**

  ```php
  Luminova\Funcs\redirect(string $uri, ?string $method = null, int $status = 302);
  ```

* **`abort`** – Terminates execution and sends an error response with the specified HTTP status code.
  Useful for rejecting invalid requests or handling unexpected conditions globally.

  **Example:**

  ```php
  Luminova\Funcs\abort(int $status = 500, string $message = '', array $headers = []);
  ```

* **`back`** – Redirects the client to the previous page using the `HTTP_REFERER` header,
  falling back to `/` if no referrer is available.

  **Example:**

  ```php
  Luminova\Funcs\back(?string $method = null, int $status = 302);
  ```

* **`array_first`** – Returns the first element of an array, or `null` when the array is empty.

  **Example:**

  ```php
  use function Luminova\Funcs\array_first;

  $item = array_first(['a', 'b', 'c']); 
  // 'a'
  ```

---

* **`array_last`** – Returns the last element of an array, or `null` when the array is empty.

  **Example:**

  ```php
  use function Luminova\Funcs\array_last;

  $item = array_last(['a', 'b', 'c']); 
  // 'c'
  ```

---

## Optimization and Enhancements

#### Updated in Version 3.6.8

### [Template View Class](/templates/views.md)

The `Luminova\Template\View` class has been improved to make template rendering faster, safer, and easier to use.

**Key improvements:**

1. **Lazy Loading** – The View class is no longer a trait inside the **CoreApplication**. It is now loaded only when needed, which improves performance.
2. **Protected Scope** – Templates cannot directly access private properties or methods of the View class. This keeps your data safe and prevents accidental changes.
3. **Consistent Rendering** – Isolated rendering is strictly enforced. When not isolated, `$self` behaves exactly like `$this`, making template code predictable and easier to read.

> **Tip for beginners:**
> Think of the View class as a separate workspace for your templates. 
> It only exposes what is meant to be used in templates and keeps everything else private. This prevents errors and keeps your code clean.

---

### [Session::login](/sessions/session#lmv-docs-login)

The `login()` method now supports role assignment at the time of authentication:

```php
$session->login(roles: ['admin', 'editor']);
```

> This eliminates the need for separate role setup after login and ensures consistent session initialization.

---

### [Schema Structured Data Generator](/website-optimizations/seo-structured-data.md)

The `Luminova\Component\Seo\Schema` class has been optimized to improved performance and introduced new [structure of schema definition](/configs/structured-data.md), making it easier to extend and manager.

---

### [Luminova\Funcs::import](/global/functions#lmv-docs-import)

The global `import()` function now supports passing variables into the imported file’s scope:

```php
Luminova\Funcs\import('path/to/file.php', scope: ['name' => 'Peter']);

// Inside path/to/file.php:
echo $name; // Peter
```

---

### [Outgoing HTTP Request Helper Class (`Network`)](/http-client/network.md)

* Added support for **static method calls** (`get()`, `post()`, `postAsync()`) to simplify HTTP requests without manually creating client objects.
* Introduced `Network::config(...)` for defining default settings (`base_uri`, headers, timeout). Configuration is **locked** after first setup to prevent changes.
* Improved error handling:

  * Clear `BadMethodCallException` is thrown when a static method is called incorrectly (e.g., missing URL with no `base_uri` configured).

**Example:**

```php
use Luminova\Http\Network;

// Configure default settings
Network::config([
  'base_uri' => 'https://api.example.com/',
  'timeout'  => 10,
  'headers'  => ['Accept' => 'application/json']
]);

// Static requests
$response = Network::get('user/42/profile');

// Async
Network::postAsync('users', ['json' => ['name' => 'Alice']])
  ->then(function(Response $response){
    //...
  });
```

---

#### Without Default Configuration:

Static methods also work without pre-configured defaults — simply pass the full URL and any request options directly:

```php
use Luminova\Http\Network;

$response = Network::get('https://api.example.com/user/42/profile', [
  'timeout'  => 10,
  'headers'  => ['Accept' => 'application/json']
]);
```

---

### [Error Exception Class](/running/exceptions.md#available-exception-classes)

The `Luminova\Exceptions\ErrorException` class has been updated to **match PHP’s native `ErrorException` behavior**.
You can now pass the error **severity**, **file**, and **line number** directly when creating an instance:

```php
new Luminova\Exceptions\ErrorException(
  $message,
  $code = ErrorCode::ERROR,
  $severity = 1,
  $file = null,
  $line = null,
  $previous = null
);
```

---

### [Error Controller Interface](/error-handlers/error-view-controller.md)

The `Luminova\Interface\ErrorHandlerInterface` now **requires** an abstract method `onTrigger()` for custom HTTP status code handling. This method is called when:

* no default error handler is defined, or
* `Luminova\Routing\Router::trigger(xxx)` is explicitly invoked.

**Example:**

```php
// /app/Errors/Controllers/ErrorController.php

public static function onTrigger(Application $app, int $status = 404, array $arguments = []): int
{
  $template = match ($status) {
      500 => '5xx',
      // Add more custom templates per status code
      default => '4xx'
  };

  return $app->view->view($template)->render(
      ['data' => $arguments],
      $status
  );
}
```

---

### [Dynamic URI Segment Placeholders](/routing/dynamic-uri-placeholder.md)

Improved placeholder matching and added new built-in types for cleaner route definitions:

* `(:uuid)` – matches valid UUID values.
* `(:username)` – matches usernames containing letters, numbers, dots, underscores, or hyphens.
* `(:version)` – matches version numbers such as `1.0`, `2.5.3`, or similar.

> These placeholders make your routes easier to read and automatically validate the expected format of each URI segment.

---

## Renamed

#### Renamed in Version 3.6.8

Update all references to these namespaces:

* `Luminova\Http\Client\Curl` → **`Luminova\Http\Client\Novio`**
* `Luminova\Core\Core**` → **`Luminova\Foundation\Core\*`**
* `Luminova\Base\BaseController` → **`Luminova\Base\Controller`**
* `Luminova\Base\BaseCommand` → **`Luminova\Base\Command`**
* `Luminova\Base\BaseConsole` → **`Luminova\Base\Console`**
* `Luminova\Base\BaseConfig` → **`Luminova\Base\Configuration`**
* `Luminova\Arrays\ArrayUtil` → **`Luminova\Utility\Collections\Arr`**
* `Luminova\Arrays\Listifier` → **`Luminova\Utility\String\Listifier`**

---

### Routing System Error Handler

Setting error handler in routing system:

* `Luminova\Routing\Router::setErrorListener()` → **Use** `Luminova\Routing\Router::onError()`

---

### Exceptions

* `Luminova\Exceptions\*::getFilteredMessage()` → **Use** `Luminova\Exceptions\*::getDescription()`

---

### URI Parent Depth

Setting URL and assets parent directory depth:

* `Luminova\Template\View::setAssetDepth(N)` → **Use** `Luminova\Template\View::setUriPathDepth(N)`

---

### Route Attribute Constants

Attribute **Route** class middleware constants renamed for clarity.

Under namespace: `Luminova\Attributes\`:

* `Route::BEFORE_MIDDLEWARE` → **Use** `Route::HTTP_BEFORE_MIDDLEWARE`
* `Route::AFTER_MIDDLEWARE` → **Use** `Route::HTTP_AFTER_MIDDLEWARE`
* `Route::GLOBAL_MIDDLEWARE` → **Use** `Route::CLI_GLOBAL_MIDDLEWARE`
* `Route::GUARD_MIDDLEWARE` → **Use** `Route::CLI_GROUP_MIDDLEWARE`

---

### Structured Data Generator

Schema class methods renamed for consistency:

* `Luminova\Seo\Schema::setConfig()` → **Use** `Luminova\Component\Seo\Schema::setObject()`
* `Luminova\Seo\Schema::getSchema()` → **Use** `Luminova\Component\Seo\Schema::toJsonLd()`
* `Luminova\Seo\Schema::getMeta()` → **Use** `Luminova\Component\Seo\Schema::getHeadTags()`
* `Luminova\Seo\Schema::getMutable()` → **Use** `Luminova\Component\Seo\Schema::getObject()`
* `Luminova\Seo\Schema::getGraph()` → **Use** `Luminova\Component\Seo\Schema::getJsonLdString()`
* `Luminova\Seo\Schema::getScript()` → **Use** `Luminova\Component\Seo\Schema::getJsonLdScript()`

---

## Deprecated

#### Deprecated in Version 3.6.8

### Sitemap Generator Command

The sitemap command now allows passing arguments to override default configurations.
You can also exclude URLs using **patterns**, built-in placeholders, or even **custom defined placeholders**.

* **Deprecated:** `php novakit generate:sitemap`
* **Use instead:** `php novakit sitemap`

---

## Changes

#### Changed in Version 3.6.8

### Exception Codes Reference

Exception codes are now centralized in a dedicated `Code` class.
Update your exception constructors to reference codes from `Luminova\Exception\Code` instead of individual exception classes.

**Before:**

```php
use Luminova\Exception\RuntimeException;

throw new RuntimeException('Message', RuntimeException::INVALID);
```

**After:**

```php
use Luminova\Exception\Code;
use Luminova\Exception\RuntimeException;

throw new RuntimeException('Message', ErrorCode::INVALID);
```

---

### Routing System Error Trigger

The routing error trigger method is now **private**.
Replace calls to `triggerError()` with `trigger()`.

**Before:**

```php
$router->triggerError($code);
```

**After:**

```php
$router->trigger($code);
```

---

### Core Error Handling

The `Luminova\Errors\ErrorHandler` class has been **replaced** with `Luminova\Errors\Monitor`.
Update references to use the new class and correct method names.

**Before:**

```php
use Luminova\Errors\ErrorHandler;

ErrorHandler::*;

// Resolve error code to name
ErrorHandler::getErrorName(...);

// Deprecated error trigger
ErrorHandler::depreciate(...);
```

**After:**

```php
use Luminova\Errors\Monitor;

Monitor::*;

// Resolve error code to name
Luminova\Exception\ErrorCode::getName(...);

// Deprecated error trigger (correct spelling)
Monitor::deprecate(...);
```

> **Note:** 
> The method is now correctly named `deprecate`, replacing the misspelled `depreciate`.