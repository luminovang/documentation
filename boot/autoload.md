# Autoloading Modules and Composer Dependency

***

## Overview

Autoloading framework modules and composer third-party libraries based on application environment using the Boot helper class.

***

## Introduction

Autoloading in Luminova simplifies the process of pre-loading the framework setup and registering dependencies, including Composer libraries, to make them readily available. This ensures that global functions, constants, and third-party libraries are accessible throughout your application.

Instead of manually loading the Composer autoloader (`/system/plugins/autoload.php`), you can load Luminova's autoloader (`/system/Boot.php`). This will automatically handle basic initialization and error handling setup.

---
## Usage Examples

### HTTP Example

To initialize the HTTP environment and get an instance of the application class:

```php
use Luminova\Boot;

require_once __DIR__ . '/system/Boot.php';
$app = Boot::http();
```

To initialize the HTTP environment without needing the application class instance:

```php
use Luminova\Boot;

require_once __DIR__ . '/system/Boot.php';
Boot::init();
```

### CLI Example

For command-line interface (CLI) initialization:

```php
use Luminova\Boot;

require_once __DIR__ . '/system/Boot.php';
Boot::cli();
```

---

## Class Definition

* **Namespace**: `Luminova\Boot`
* **Type**: Final class, cannot be extended.

---

## Methods

### init

Initializes the HTTP environment for web and API applications.

Warms up the application, registers error handlers, and loads core modules.

```php
public static init(): void
```

> Similar to the `http()` method but does not return the application instance.

**Usage:**

```php
use Luminova\Boot;

require_once __DIR__ . '/system/Boot.php';

Boot::init();
```

---

### autoload

Initializes all required autoload modules.

Wrapper for `warmup()` and `finish()`. Loads core modules and prepares the application context without registering error handlers or configuring the CLI environment.

```php
public static autoload(): void
```

> Use this to ensure Composer and framework modules are available in the current context.

**Usage:**

```php
use Luminova\Boot;

require_once __DIR__ . '/system/Boot.php';

Boot::autoload();
```

---

### http

Prepares the HTTP environment for web and API applications.

Typically used in `public/index.php`. Ensures the application is warmed up, registers error handlers, and loads required core modules before returning the application instance.

```php
public static http(): App\Application<CoreApplication, LazyObjectInterface>
```

**Returns**

`\App\Application<Luminova\Foundation\Core\Application,Luminova\Interface\LazyObjectInterface>` - Return the application instance.

**Usage:**

```php
// /public/index.php
use Luminova\Boot;
 
require_once __DIR__ . '/../system/Boot.php';
 
Boot::http()->router->context(...)->run();
```
 
---

### cli

Prepares the CLI environment.

Intended for use in custom CLI scripts. Autoloads required files, enables error reporting, validates the SAPI type, defines CLI constants, and completes the bootstrapping process.

```php
public static cli(): void
```

**Usage:**
```php
// /bin/script.php

#!/usr/bin/env php
<?php
use Luminova\Boot;

require __DIR__ . '/system/Boot.php';

Boot::cli();

// Your cli implementation
```

---

### warmup

Loads core modules and prepares the application environment.

Ensures constants, functions, error handlers, setting time zones, adjusting execution limits, configuring script behavior and loads core framework modules in the correct order. Also applies HTTP method spoofing early to ensure routing uses the correct request method.

```php
public static warmup(): void
```

> **Note:** The `warmup` method is automatically called when the `Boot` class is loaded. You may not need to call this method again.

---

### shouldDefineCommandStreams

Ensures CLI standard streams (`STDIN`, `STDOUT`, `STDERR`) are defined.

This method uses `tryFopen()` to safely define the CLI standard streams if not already set.

```php
public static shouldDefineCommandStreams(): void
```

---

### tryFopen

Opens a file using `fopen()` with exception handling.

If the file can't be opened or an error occurs, a RuntimeException is thrown.

```php
public static tryFopen(string $filename, string $mode): ?resource
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The path to file. |
| `$mode` | **string** | File access mode (e.g., `r`, `w`). |

**Return Value:**

`resource|null` - Return a valid stream resource.

**Throws:**

[Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the file can't be opened.