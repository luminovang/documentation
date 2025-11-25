# Public Index Front Controller

***

## Overview

Documentation for Luminova Entry Controller index.php

***

## Introduction

This documentation outlines the purpose of the `index.php` file and its key responsibilities in the Luminova framework.

In the Luminova framework, the `index.php` file serves as the main entry point for handling incoming HTTP and CLI requests. It is responsible for autoloading and initializing framework modules, setting up routing prefixes, and directing requests to the appropriate controllers. The `index.php` file is located in the `public` directory of your Luminova project, and its location and name cannot be changed or moved to another directory.

***

### Responsibilities

1. **Bootstrapping the Framework**: Initializes core components and loads necessary modules.
2. **Ensuring Correct Directory Context**: Verifies that the application runs from the correct directory.
3. **Defining Routing Prefixes**: Sets up routing prefixes to manage request paths effectively.
4. **Running the Application**: Executes the application by dispatching requests to the proper controllers.

***

## Customizations

Below are several examples of how `index.php` might be configured.

### Basic Setup

The URI prefix name `web` serves as a default handler for all incoming `URLs` if no other custom context prefix matches the request url prefix. This does not mean that only URLs starting with `web` are handled by this context. Instead, it is a generic way for Luminova to manage all web-related routes when no specific context prefix is defined or matched. 

If other custom context prefixes are defined, they will be matched first, and the `web` context will serve as the fallback for any remaining URLs.

```php
<?php
declare(strict_types=1);

use Luminova\Boot;
use Luminova\Routing\Prefix;
use App\Controllers\Errors\ErrorController;

// Boot framework classes 
require_once __DIR__ . '/../system/Boot.php';

/**
 * Ensure that we are in front controller while running script in cli mode
*/
if (getcwd() . DIRECTORY_SEPARATOR !== DOCUMENT_ROOT) {
    chdir(DOCUMENT_ROOT);
}

Boot::http()->router->context(
    new Prefix(Prefix::WEB, [ErrorController::class, 'onWebError']),
    new Prefix(Prefix::API, [ErrorController::class, 'onApiError']),
    new Prefix(Prefix::CLI)
)->run();
```

In this example, the router is configured with three contexts:
- `WEB`: Handles web requests and uses the `ErrorController::onWebError` method for error handling.
- `API`: Handles API requests and uses the `ErrorController::onApiError` method for error handling.
- `CLI`: Handles CLI requests without a specific error handler.

***

### Using Callbacks

This example demonstrates using anonymous functions (callbacks) for error handling in the `WEB` and `API` contexts.

```php
Boot::http()->router->context(
    new Prefix(Prefix::WEB, fn(Application $app): int => ErrorController::onWebError($app)),
    new Prefix(Prefix::API, function(){
        echo "Error";
    }),
    new Prefix(Prefix::CLI)
)->run();
```

***

### Using Arrays

In this example, contexts are defined using arrays. Each array specifies a `prefix` and an `error` handler.

```php
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

***

### Using Attributes

This example shows the simplest usage, where the router context is defined using PHP attributes, and no additional parameters are passed to the `context` method.

```php
Boot::http()->router->context()->run();
```

***

### Routing Context Method

The `context` method is a helper for managing routing by allowing you to define URI prefixes when attribute-based routing is disabled. By creating a corresponding file `/routes/<prefix-name>.php` to handle all routes related to a specific URI prefix, you can improve code organization and optimize routing performance. This ensures that only the relevant routes are loaded to handle the current request, rather than loading all routes at once.

#### Context Method Parameters

The `context` method accepts multiple arguments, which can be instances of `Context`, arrays, or `null` when using PHP attributes.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$contexts` | **Luminova\Routing\Prefix\|array<string,mixed>\|null** | Arguments containing routing contexts or an array of arguments. Pass `null` or leave blank only when using route attributes. |

**Return Value:**

`Router` - Returns the router instance.

**Throws:**

- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - Thrown if no context arguments are passed and route attributes are not enabled.

> **Notes:**
> - Ensure the context name should be unique based on your `URLs` to discover and load only the required files.
> - Mixing context arguments with arrays and `Context` instances may produce unintended errors.

***
### See Also

- [Routing Prefix](/routing/uri-prefix.md) - Learn more about how routing prefixes work and their usage.
- [Routing Class](/routing/system.md) -  Learn more about how routing and their usage.
- [Routing Attribute](/attributes/route.md) -  Learn more about how PHP attribute routing and its usage.

This documentation should provide a clear understanding of how to configure and use the `index.php` file as the entry point for a Luminova application.