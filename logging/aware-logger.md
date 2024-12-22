# PSR Aware Logger Interface

***

## Overview

The Luminova LoggerAware is an extension of PSR LoggerAwareInterface is part of the PSR-3 logging standard, designed to provide a way for classes to accept a PSR-compliant logger instance.

***

## Introduction

The Luminova `LoggerAware` class extends the PSR `LoggerAwareInterface` from the PSR-3 logging standard. It provides a mechanism for classes to accept and utilize a PSR-compliant logger instance, enabling seamless logging. 

This design allows easy swapping of logging implementations and ensures compatibility with any PSR-compliant logger, enhancing flexibility and standardization.

The primary method in `LoggerAware` is `setLogger`, which injects a logger instance into the implementing class.

---

### Usage Examples

#### Using a Class Implementing `\Psr\Log\LoggerInterface`

```php
use Luminova\Logger\LoggerAware;

$logger = new LoggerAware();
$logger->setLogger(new MyPsrLoggerClass());
$logger->alert('This is a alert message');
$logger->getLogger()->setLevel('debug')->mail('peter@example.com', 'This is a debug message');
```

**Primary Benefits:**
- **Flexibility:** Easily switch between different logging implementations.  
- **Standards Compliance:** Fully compatible with any PSR-compliant logger.  

***

## Class Definition

* Full namespace: `\Luminova\Logger\LoggerAware`
* This class implements:
[\Psr\Log\LoggerInterface](https://www.php-fig.org/psr/psr-3/#3-psrlogloggerinterface), [\Psr\Log\LoggerAwareInterface](https://www.php-fig.org/psr/psr-3/#4-psrlogloggerawareinterface)

## Properties

### logger

The PSR logger interface class instance.

```php
protected \Psr\Log\LoggerInterface $logger = null;
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
