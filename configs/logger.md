# Configurations for Application Logger Preference and Auto Log Context

***

## Overview

Application logger configuration provides flexibility and customization methods to override the default nova-logger module with your preferred PSR-compliant logger interface.

***

## Introduction

The `Logger` configuration class allows you to specify a preferred PSR-compliant logger interface for application logging, replacing the default `NovaLogger`. 

It also enables asynchronous logging using PHP `Fiber`, allowing log messages to be processed in the background without blocking the UI thread.

***

* Class namespace: `\App\Config\Logger`
* File path: `/app/Config/Logger.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

### contextHeaderName

Specifies the name of a request header to look for when adding additional context to logs.

This is useful for identifying users or API clients by extracting a unique identifier (like `X-User-ID` or `Authorization`) from the request headers. If the specified header is present, its value will be automatically included in the log context.

```php
public static ?string $contextHeaderName = null;
```

---

### contextFieldName

Specifies the name of a field in the request body to include in the log context.

This allows you to capture identifying information (e.g., `user_id`, `client_id`) from the request payload and include it in your log entries automatically.

```php
public static ?string $contextFieldName = null;
```

***

### asyncLogging

Enables asynchronous logging using Fibers to log messages in a background thread without blocking the UI.

```php
public static bool $asyncLogging = false;
```

> **Note:** This feature is only supported for `\Luminova\Logger\NovaLogger`.

***

### maxSize

Specify the maximum size (in bytes) for each log level (e.g., 10485760 for 10 MB). 
When this limit is reached, a backup of the log file is created if `logger.create.backup` or `$autoBackup` is set to true; otherwise, the logs are cleared.

```php
public static int $maxSize = 0;
```

***

### autoBackup

Indicate whether a backup of log file should be created when the `logger.max.size` or `$maxSize` limit is reached. 
Set to 'true' to automatically create a backup and empty the current log file, 'false' to empty the log file only.

```php
public static bool $autoBackup = false;
```

***

## Methods

### getLogger

Returns an instance of the preferred logger for application logging.

```php
public getLogger(): ?\Psr\Log\LoggerInterface
```

**Return Value:**

`\Psr\Log\LoggerInterface|null` - Return the preferred logger instance, or null to use the default logger.

> **Note:** The logger class must implement `\Psr\Log\LoggerInterface`.

***

### getEmailLogTemplate

Customize and Generate an HTML Email Template for Logging Notifications.

This method allows you to define a custom email template for logging system notifications. If the method returns `null` or an empty string, the default email template will be used.

```php
public static getEmailLogTemplate(
    Luminova\Interface\HttpRequestInterface $request,
    Psr\Log\AbstractLogger $logger,
    string $message,
    string $level,
    array $context
): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **HttpRequestInterface** | The HTTP request object containing information about the request. |
| `$logger` | **AbstractLogger** | The logger instance handling the log entry. |
| `$message` | **string** |  The log message. |
| `$level` | **string** | The log level (e.g., 'info', 'warning', 'error'). |
| `$context` | **array** | Additional context information for the log message. |

**Return Value:**

`string|null` - Return a custom HTML email template or null/empty string to use the default template.