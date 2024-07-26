# Public Entry Controller

***

## Overview

Documentation for Luminova Entry Controller index.php

***

## Introduction

The `index.php` file serves as the front controller for a Luminova application. It initializes the framework, sets up routing contexts, and handles incoming requests. This documentation provides an overview of the file's purpose and various ways it can be configured.

***

### Overview

The `index.php` file is responsible for:

1. Bootstrapping the Luminova framework.
2. Ensuring the script runs in the correct directory.
3. Defining routing contexts.
4. Running the application.

***

## Implementations

Below are several examples of how `index.php` might be configured.

### Basic Setup

The context URI prefix `web` serves as a default handler for all URLs if no other custom context prefix matches the request. This does not mean that only URLs starting with `web` are handled by this context. Instead, it is a generic way for Luminova to manage all web-related routes when no specific context prefix is defined or matched. 

If other custom context prefixes are defined, they will be matched first, and the `web` context will serve as the fallback for any remaining URLs.

```php
<?php
declare(strict_types=1);

use \Luminova\Boot;
use \Luminova\Routing\Context;
use \App\Controllers\Errors\ViewErrors;

// Boot framework classes 
require_once __DIR__ . '/../system/Boot.php';

/**
 * Ensure that we are in front controller while running script in cli mode
*/
if (getcwd() . DIRECTORY_SEPARATOR !== FRONT_CONTROLLER) {
    chdir(FRONT_CONTROLLER);
}

Boot::http()->router->context(
    new Context(Context::WEB, [ViewErrors::class, 'onWebError']),
    new Context(Context::API, [ViewErrors::class, 'onApiError']),
    new Context(Context::CLI)
)->run();
```

In this example, the router is configured with three contexts:
- `WEB`: Handles web requests and uses the `ViewErrors::onWebError` method for error handling.
- `API`: Handles API requests and uses the `ViewErrors::onApiError` method for error handling.
- `CLI`: Handles CLI requests without a specific error handler.

***

### Using Callbacks

This example demonstrates using anonymous functions (callbacks) for error handling in the `WEB` and `API` contexts.

```php
Boot::http()->router->context(
    new Context(Context::WEB, fn(Application $app): int => ViewErrors::onWebError($app)),
    new Context(Context::API, function(){
        echo "Error";
    }),
    new Context(Context::CLI)
)->run();
```

***

### Using Arrays

In this example, contexts are defined using arrays. Each array specifies a `prefix` and an `error` handler.

```php
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

***

### Using Attributes

This example shows the simplest usage, where the router context is defined using PHP attributes, and no additional parameters are passed to the `context` method.

```php
Boot::http()->router->context()->run();
```

***

### Parameters

The `context` method accepts multiple arguments, which can be instances of `Context`, arrays, or `null` when using PHP attributes.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$contexts` | **Luminova\Routing\Context\|array<string,mixed>\|null** | Arguments containing routing contexts or an array of arguments. Pass `null` or leave blank only when using route attributes. |

**Return Value:**

`Router` - Returns the router instance.

**Throws:**

- [\Luminova\Exceptions\RouterException](/exceptions/classes.md#routerexception) - Thrown if no context arguments are passed and route attributes are not enabled.

### Notes

- Ensure the context name is unique based on your `URLs` to discover and load only the required files.
- Mixing context arguments with arrays and `Context` instances may produce unintended errors.

***
### See Also

- [Routing Context](/routing/view-context.md) - Learn more about how routing prefixes work and their usage.
- [Routing Class](/routing/view-uri.md) -  Learn more about how routing and their usage.
- [Routing Attribute](/routing/route-attribute.md) -  Learn more about how PHP attribute routing and its usage.

This documentation should provide a clear understanding of how to configure and use the `index.php` file as the entry point for a Luminova application.