# Log Levels in the Logging System

***

## Overview

LogLevel class defines various logging levels used for categorizing log messages.

***

## Introduction

The Luminova `LogLevel` class defines a set of constants representing various logging levels, following standard practices for logging severity. These constants provide a consistent way to categorize and manage log messages based on their importance and urgency.

- **Standardized Log Levels:** Includes levels like `EMERGENCY`, `ALERT`, `ERROR`, and `DEBUG` for comprehensive logging.
- **Custom Levels:** Adds additional levels, such as `PHP`, `EXCEPTION` and `METRICS`, are for other application use cases.

### Use Cases

- Categorize log messages by severity for better organization.
- Integrate into logging systems to ensure consistent handling of log levels.
- Extend or filter logs based on specific levels, such as debugging or performance metrics.

---

### Usage Examples

Logging message with a specified level: 

```php
use App\Utils\MyLogger;

(new MyLogger)->log(LogLevel::CRITICAL, 'This is a critical error');
```

Validating log level: 

```php
if (LogLevel::has('critical')) {
    echo "Log level is valid.";
} else {
    echo "Log level not recognized.";
}
```

Check if a given log level is considered critical:

```php
var_dump(LogLevel::isCritical(LogLevel::WARNING));    // false
var_dump(LogLevel::isCritical(LogLevel::CRITICAL));   // true
var_dump(LogLevel::isCritical(LogLevel::ALERT));      // true
var_dump(LogLevel::isCritical(LogLevel::DEBUG));      // false
```

Log level assertion with custom logger class:

```php
namespace App\Utils;

use \Psr\Log\LoggerInterface;

class MyLogger extends LoggerInterface
{
    public function log(string $level, string $message): void
    {
        LogLevel::assert($level, __METHOD__);
        $this->log(sprintf("[%s] %s\n", strtoupper($level), $message));
    }
}
```

***

## Class Definition

* Full namespace: `Luminova\Logger\LogLevel`
* This class is marked as **final** and can't be subclassed

***

## Constants

| Constant       | Type   | Value             | Description                                                                                     |
|:---------------|:-------|:------------------|:-----------------------------------------------------------------------------------------------|
| `EMERGENCY`    | string | `emergency`       | System is unusable. Critical issues that require immediate attention.                          |
| `ALERT`        | string | `alert`           | Action must be taken immediately. Used for critical issues requiring urgent resolution.        |
| `CRITICAL`     | string | `critical`        | Critical conditions, such as an application or service being down. Requires immediate action.  |
| `ERROR`        | string | `error`           | Runtime errors that require attention. Indicates problems that need investigation.             |
| `WARNING`      | string | `warning`         | Warnings about potential issues that may require attention but are not immediately critical.   |
| `NOTICE`       | string | `notice`          | Normal but significant conditions that require attention. Provides useful information.         |
| `INFO`         | string | `info`            | Informational messages that highlight the application's progress at a coarse-grained level.    |
| `DEBUG`        | string | `debug`           | Detailed information used for debugging and diagnosing issues, typically during development.   |
| `EXCEPTION`    | string | `exception`       | Exception messages or errors, useful for handling uncaught exceptions or error scenarios.      |
| `PHP`          | string | `php_error`      | PHP errors, including parse errors, runtime errors, and warnings.                             |
| `METRICS`      | string | `metrics`         | Performance metrics, specifically for APIs or production-level monitoring.                     |

*** 

List of all valid log levels.

```php
public const array<string,string> LEVELS = [
    'emergency'     => self::EMERGENCY,
    'alert'         => self::ALERT,
    'critical'      => self::CRITICAL,
    'error'         => self::ERROR,
    'warning'       => self::WARNING,
    'notice'        => self::NOTICE,
    'info'          => self::INFO,
    'debug'         => self::DEBUG,
    'exception'     => self::EXCEPTION,
    'php_error'     => self::PHP,
	'php'           => self::PHP,
    'metrics'       => self::METRICS,
];
```

***

## Methods

### has

Checks if the specified log level is valid and exists.

```php
public static has(string $level): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to check (e.g., `LogLevel::ERROR`, `LogLevel::INFO`, `LogLevel::DEBUG`). |

**Return Value:**

`bool` -  Returns true if the log level exists, false otherwise.

---

### assert

Asserts that the given log level is valid.

This function checks if the provided log level exists in the predefined set of log levels.
If the level is invalid, it throws an `InvalidArgumentException` with a detailed error message.

```php
public static assert(string $level, ?string $function = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to validate (e.g., `LogLevel::ERROR`, `LogLevel::INFO`, `LogLevel::DEBUG`). |
| `$function` | **string\|null** | Optional. The name of the calling function for context in the error message. |

**Throws:**

[Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the provided log level is not valid.

---

### isCritical

Checks if a given log level is considered critical.

Critical levels represent severe issues that require immediate attention, such as system failures or security breaches.

The following log levels are considered critical:

- `emergency`
- `alert`
- `critical`
- `exception`

```php
public static isCritical(string $level): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to check (e.g., `LogLevel::ERROR`, `LogLevel::INFO`, `LogLevel::DEBUG`). |

**Return Value:**

`bool` -  Returns true if the level is critical, otherwise false.
