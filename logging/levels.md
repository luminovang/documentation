# Log Levels

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

### Example

Logging message: 

```php
$logger->log(LogLevel::CRITICAL, 'This is a critical error');
```

Validating log level: 
```php
if (LogLevel::has('critical')) {
    echo "Log level 'critical' is valid.";
} else {
    echo "Log level not recognized.";
}
```

***

## Class Definition

* Full namespace: `\Luminova\Logger\LogLevel`
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
| `INFO`         | string | `info`            | Informational messages that highlight the application’s progress at a coarse-grained level.    |
| `DEBUG`        | string | `debug`           | Detailed information used for debugging and diagnosing issues, typically during development.   |
| `EXCEPTION`    | string | `exception`       | Exception messages or errors, useful for handling uncaught exceptions or error scenarios.      |
| `PHP`          | string | `php_errors`      | PHP errors, including parse errors, runtime errors, and warnings.                             |
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
    'php_errors'    => self::PHP,
    'metrics'       => self::METRICS,
];
```

***

## Methods

### has

Checks if the specified log level exists.

```php
public static has(string $level): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to check (e.g., 'error', 'info', 'debug'). |

**Return Value:**

`bool` -  Returns true if the log level exists, false otherwise.

