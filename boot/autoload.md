# Autoloading Dependency and Composer

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
<?php
use Luminova\Boot;

require_once __DIR__ . '/system/Boot.php';
$app = Boot::http();
```

To initialize the HTTP environment without needing the application class instance:

```php
<?php
use Luminova\Boot;

require_once __DIR__ . '/system/Boot.php';
Boot::init();
```

### CLI Example

For command-line interface (CLI) initialization:

```php
<?php
use Luminova\Boot;

require_once __DIR__ . '/system/Boot.php';
Boot::cli();
```

---

## Class Definition

* **Namespace**: `\Luminova\Boot`
* **Type**: Final class, cannot be extended.

---

## Methods

### http

Initializes the HTTP environment for a web application, sets up error handling, completes bootstrapping, and returns the application instance.

```php
public static http(): App\Application
```

**Returns**

`\App\Application` - Return the application instance.

---

### init

Initializes the HTTP environment, sets up error handling, and completes the bootstrapping process.

```php
public static init(): void
```

---

### cli

Initializes the CLI environment, defines CLI-related constants, and completes bootstrapping.

```php
public static cli(): void
```

---

### warmup

Performs the initial setup for the application autoload, by loading the necessary files, setting time zones, adjusting execution limits, and configuring script behavior.

```php
public static warmup(): void
```

> **Note:** The `warmup` method is automatically called when the `Boot` class is loaded. You do not need to call this method manually, and doing so again will result in an error.