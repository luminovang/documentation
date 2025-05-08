# PSR Aware Logger for Custom Application Logging

***

## Overview

The Luminova LoggerAware is an extension of PSR LoggerAwareInterface is part of the PSR-3 logging standard, designed to provide a way for classes to accept a PSR-compliant logger instance.

***

## Introduction

The Luminova `LoggerAware` class extends the PSR `LoggerAwareInterface` from the PSR-3 logging standard. It provides a mechanism for classes to accept and utilize a PSR-compliant logger instance, enabling seamless logging. 

This design allows easy swapping of logging implementations and ensures compatibility with any PSR-compliant logger, enhancing flexibility and standardization.

The primary method in `LoggerAware` is `setLogger`, which injects a logger instance into the implementing class.

**Primary Benefits:**
- **Flexibility:** Easily switch between different logging implementations.  
- **Standards Compliance:** Fully compatible with any PSR-compliant logger. 

---

### Usage Examples

Using a custom PSR logger class.

**Implement your custom class:**

```php
namespace App\Utils;

use Psr\Log\LoggerInterface;

class MyLoggerClass implements LoggerInterface
{
    // Your logger methods
}
```

Initialize aware logger with your custom logger instance:

```php
use Luminova\Logger\LoggerAware;
use \App\Utils\MyLoggerClass;

$logger = new LoggerAware(new MyLoggerClass());
```

---

Initialize aware logger with Luminova `NovaLogger` instance:

```php
use Luminova\Logger\LoggerAware;
use Luminova\Logger\NovaLogger;

$logger = new LoggerAware(new NovaLogger('app'));
```

---

Initialize aware logger with Monolog logger instance:

```php
use Luminova\Logger\LoggerAware;
use Monolog\Logger;
use Monolog\Handler\StreamHandler;

$monolog = new Logger('app');
$monolog->pushHandler(new StreamHandler('php://stdout', Logger::DEBUG));

$logger = new LoggerAware($monolog);
```

---

Log messages using your logger class: 

```php
$logger->alert('This is a alert message');
$logger->info('This is an info message');
$logger->debug('This is a debug message');
```

---

### NovaLogger-Specific Features

Send log to emil or a remote URL:

```php
$logger->mail('peter@example.com', 'This is an email log message');
$logger->remote('https://example.com/api/log', 'This is a remote log message');
```

When sending logs via email or to a remote server, you can define a default log level to use in case of failure. The log level also serves as an indicator of log severity.  

```php
$logger->setLevel('debug')
    ->mail('peter@example.com', 'This is a debug message');
``` 

***

## Class Definition

* Full namespace: `\Luminova\Logger\LoggerAware`
* Class parent:
[\Psr\Log\AbstractLogger](https://www.php-fig.org/psr/psr-3/#3-psrabstractlogger)
* This class implements:
[\Psr\Log\LoggerInterface](https://www.php-fig.org/psr/psr-3/#3-psrlogloggerinterface), [\Psr\Log\LoggerAwareInterface](https://www.php-fig.org/psr/psr-3/#4-psrlogloggerawareinterface)

---

## Properties

### logger

The PSR logger interface class instance.

```php
protected ?Psr\Log\LoggerInterface $logger = null;
```

***

## Methods

### constructor

Initialize Aware Logger Interface.

```php
public __construct(?\Psr\Log\LoggerInterface $logger = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$logger` | **\Psr\Log\LoggerInterface** | The logger instance. |

***

### setLogger

Set a logger instance on the object.

```php
public setLogger(\Psr\Log\LoggerInterface $logger): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$logger` | **\Psr\Log\LoggerInterface** | The logger instance. |

***

### getLogger

Get the instance of PSR logger class.

```php
public getLogger(): ?LoggerInterface
```

**Return Value:**

`\Psr\Log\LoggerInterface|null` -  Return instance of logger class in-use, otherwise null.

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
public exception(\Throwable|string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **\Throwable\|string** | The exception message to log. |
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
public metrics(string $data, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The php message to log. |
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
| `$level` | **string** | The log level (e.g., `emergency,` `error,` `info`). |
| `$message` | **string** | The log message. |
| `$context` | **array** | Additional context data (optional). |

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If logger does not implement LoggerInterface
