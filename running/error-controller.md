# Implementation of Custom Error View Controllers for Pages

***

## Overview

Customize or add new error handlers for different routing contexts. By default, it provides handlers for the web or API contexts, which you can modify or customize as needed.

***

## Introduction

The `ViewErrors` class allows you define and manage error-handling methods to customize how view errors are displayed. Error-handling methods for `404` error type that may occur during the execution of your application routing or view rendering.
This provide a way to display meaningful error messages to users.

***

**NOTE**

- Error handler methods can be either static or non-static.
- Methods must be declared as `public`, not `protected` or `private`.
- Dependency injection is enabled by default for error handlers, allowing you to pass any class you need as a parameter in the method signature.

***

* Class namespace: `\App\Controllers\Errors\ViewErrors`
* This class implements:
[\Luminova\Interface\ErrorHandlerInterface](/interface/classes.md#errorhandlerinterface)
* Parent class: [\Luminova\Base\BaseViewController](/base/view.md)

***

## Methods

### onWebError

The default error handler for the `web` routing context.

```php
public static onWebError(Application $app): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app` | **\App\Application** | Application instance available |

***

### onApiError

The default error handler for the `API` routing context.

```php
public static onApiError(Application $app): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app` | **\App\Application** | Application instance available |

***

### Usage Example

To register your error handler, it must be callable or pass an array where the first element of the array is your error handler class name and the second element is the method name.

```php
// public/index.php
<?php
use \Luminova\Routing\Prefix;
use App\Controllers\Errors\ViewErrors;

$app->router->context( 
	new Prefix('foo', [ViewErrors::class, 'onFooError']),
	//...
);
``` 

> This example demonstrates how to register an error handler in your application's routing context. The `onFooError` method from the `ViewErrors` class will handle errors for the `FOO` context.