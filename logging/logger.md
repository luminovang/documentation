# Application Logger Class

***

## Overview

Logger class is a PSR-3 compliant logging system that extends the LoggerInterface. It provides a unified interface for handling application logs and serves as a dispatcher to ensure compatibility.

***

## Introduction

The Luminova ** Logger** class is a PSR-3 compliant logging system that extends the `LoggerInterface`. It provides a unified and flexible interface for managing application logs and acts as a dispatcher, ensuring seamless compatibility with other PSR-compliant loggers.

---

### Key Features

- **PSR-3 Compliance**: Adheres to `LoggerInterface`, enabling integration with third-party tools.
- **Default Logger**: Automatically configured as the default logger in Luminova applications.
- **Versatile Dispatching**: Supports logging to various destinations such as files, emails, or remote servers.
- **Custom Logger Support**: Can be replaced with a custom PSR-compliant logger via the application configuration.
- **Convenient Helpers**: Includes global functions and factory methods for easy access to the logger.

---

### Usage

The Luminova Logger can be accessed and used in multiple ways, ensuring flexibility and convenience.

#### **Using Global Helper Function**

```php
// Log an informational message
logger('info', 'This is an informational message');

// Log a message to a remote server
logger('https://example.com/api/log', 'This is a remote log message');

// Log a message to an email
logger('peter@example.com', 'This is an email log message');
```

#### **Accessing the Logger via Factory**

```php
$logger = factory('logger');
```

or using the application factory:

```php
use Luminova\Application\Factory;

$logger = Factory::logger();
```

#### **Creating a New Logger Instance**

```php
use Luminova\Logger\Logger;

$logger = new Logger();
```

#### **Basic Logging Examples**

```php
// Log an informational message
$logger->info('This is an informational message.');

// Log an error message
$logger->dispatch('error', 'This is an error message.');

// Dispatch log to an email
$logger->dispatch('peter@example.com', 'Critical error detected.');

// Dispatch log to a remote server
$logger->dispatch('https://example.com/api/log', 'Critical error detected.');
```

> **Note**: The `dispatch` method automatically determines the appropriate destination (file, email, or remote server) based on the input type, making log management more intuitive.

***

### Customization

To use a custom logger instead of the default:
1. Implement the `App\Config\Logger` class's `getLogger` method to return your preferred PSR-compliant logger.
2. The custom logger will automatically handle all logging operations in your application. 

#### Example: Custom Logger Integration

```php
public function getLogger(): ?LoggerInterface
{
    return new MonologLogger('custom-channel');
}
``` 

This makes it easy to switch logging implementations while maintaining PSR compliance.

***

## Class Definition

* Class namespace: `\Luminova\Logger\Logger`
* This class implements:
[\Psr\Log\LoggerInterface](https://www.php-fig.org/psr/psr-3/#3-psrlogloggerinterface)

## Properties

### logger

PSR logger interface.

```php
protected static ?\Psr\Log\LoggerInterface $logger = null;
```

***

## Methods

### constructor

Initialize logger instance

```php
public __construct(): mixed
```

***

### getLogger

Get shared instance of PSR logger class.

```php
public static getLogger(): \Psr\Log\LoggerInterface
```

**Return Value:**

`\Psr\Log\LoggerInterface` -  Return instance of logger class in-use.

***

### emergency

Log an emergency message.

```php
public emergency(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The emergency message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### alert

Log an alert message.

```php
public alert(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The alert message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### critical

Log a critical message.

```php
public critical(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The critical message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### error

Log an error message.

```php
public error(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The error message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### warning

Log a warning message.

```php
public warning(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The warning message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### notice

Log a notice message.

```php
public notice(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The notice message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### info

Log an info message.

```php
public info(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The info message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### debug

Log a debug message.

```php
public debug(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The debug message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### exception

Log an exception message.

```php
public exception(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The EXCEPTION message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### php

Log an php message.

```php
public php(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The php message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### metrics

Log an performance metric.

```php
public metrics(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The php message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### log

Log a message at a specified log level.

```php
public log(string $level, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level (e.g., &quot;emergency,&quot; &quot;error,&quot; &quot;info&quot;). |
| `$message` | **string** | The log message. |
| `$context` | **array** | Additional context data (optional). |

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If logger does not implement LoggerInterface

***

### dispatch

Sends a log message to a specified destination, either asynchronously or synchronously.

The method validates the logging destination and routes the log based on its type
(log level, email address, or URL). Email and network logging are performed asynchronously by default.

```php
public dispatch(string $to, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$to` | **string** | The destination for the log (log level, email address, or URL). |
| `$message` | **string** | The message to log. |
| `$context` | **array** | Additional context data (optional). |

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If an invalid logging destination is provided.
- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If called logging for network or mail destination for `non-novalogger` class.