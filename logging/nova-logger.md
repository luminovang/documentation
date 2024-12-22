# PSR Nova Logger Class

***

## Overview

Installation guides for the PHP Luminova framework highlight the benefits of using Composer for easy maintenance and ensuring you're always up to date with the latest framework and dependency.

***

## Introduction

The `NovaLogger` class serves as the default logging system for the Luminova framework, offering robust features for managing application logs. It supports logging messages, creating log backups, and dispatching log entries to emails or remote servers.

If preferred other logger, `NovaLogger` can be replaced with any PSR-compliant logger (e.g., `Monolog`) by configuring the `App\Config\Logger` class's `getLogger` method. This allows developers to easily channel all logging operations to a custom logger.

---

### Key Features

1. **Default Logger**: Preconfigured as the default logger for Luminova applications.
2. **Versatile Logging Options**: Supports logging to files, sending logs via email, or dispatching logs to remote servers.
3. **Asynchronous Logging**: Leverages asynchronous operations for non-blocking logging (enabled in the logger configuration).
4. **Configurable**: Allows customization, such as setting log file extensions or dispatching methods.

---

### Usage

The `NovaLogger` is preconfigured as the default logger in `\Luminova\Logger\Logger`. Custom initialization is typically unnecessary unless specific customization, such as changing log file extensions, is required.

#### Example: Using NovaLogger

Initialize NovaLogger with custom log file extension
```php
<?php
use \Luminova\Logger\NovaLogger;

$logger = new NovaLogger('.txt');
```

Log messages asynchronously (enabled by default in the logger configuration)

```php
$logger->log('error', 'This is an error message');
```

Log messages synchronously

```php
$logger->write('error', 'This is a synchronous error message');
```
Send logs to email

```php
$logger->setLevel('error')->mail('peter@example.com', 'This is a log sent via email');
```

Dispatch logs to a remote server

```php
$logger->setLevel('debug')->remote('https://example.com/api/log', 'This is a log sent to a remote server');
```

> **Note**: When using `NovaLogger` with `\Luminova\Logger\Logger`, you can redirect logs to a file, email, or remote server by using the `dispatch` method. This ensures logs are sent to the appropriate destination based on their type.

---

### Customization

To replace `NovaLogger` with another PSR-compliant logger:
1. Implement the `getLogger` method in `App\Config\Logger` to return your preferred logger.
2. Ensure your custom logger adheres to the PSR-3 `LoggerInterface`. 

Example:

```php
// app/Config/Logger.php
public function getLogger(): ?LoggerInterface
{
    return new MonologLogger('custom-channel');
}
```

***

## Class Definition

* Class namespace: `\Luminova\Logger\NovaLogger`
* Parent class: [AbstractLogger](/Psr/Log/AbstractLogger.md)

## Properties

### path

Default log path.

```php
protected static ?string $path = null;
```

***

### maxSize

The maximum log size in bytes.

```php
protected static ?int $maxSize = null;
```

***

### createBackup

Flag indicating if backup should be created.

```php
protected static ?bool $createBackup = null;
```

***

### extension

The log file extension type (e.g, `.txt`, `.log`)

```php
protected string $extension = '.log';
```

***

## Methods

### constructor

Initialize NovaLogger

```php
public __construct(string $extension ='.log'): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$extension` | **string** | The log file extension type (e.g, `.txt`, `.log`). |

***

### setLevel

Sets the log level for the remote and email logging.

```php
public setLevel(string $level): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to set (e.g., 'error', 'info', 'debug'). |

**Return Value:**

`self` -  Returns the current NovaLogger instance.

> **Note:** Setting log level with this method doesn't affect `log` and `write` methods.
> This is only for `remote` and `mail` logging, to identify the type of log level that triggered the logging.

***

### getHtmlMessage

Generates the HTML message body for log.

```php
public getHtmlMessage(string $message, array $context = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level (e.g., 'error', 'info', 'debug'). |
| `$message` | **string** | The log message. |
| `$context` | **array** | Optional associative array providing context data. |

**Return Value:**

`self` - Return formatted HTML email message body.

***

### log

Log a message at the given level.
This method will log message asynchronously using PHP `Fiber` if explicitly enabled in `App\Config\Logger`, 
otherwise logs as normal.

```php
public log(string $level, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level (e.g., &quot;emergency,&quot; &quot;error,&quot; &quot;info&quot;). |
| `$message` | **string** | The log message. |
| `$context` | **array** | Optional associative array providing context data. |

**Throws:**

- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - If unable to write log to file.

***

### write

Writes a log message to the specified file. This doesn't support no use asynchronous logging.

```php
public write(string $level, string $message, array $context = [], bool $auth_backup = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level (e.g., 'info', 'error', 'debug'). |
| `$message` | **string** | The primary log message. |
| `$context` | **array** | Optional associative array providing context data. |
| `$auth_backup` | **array** | Weather to automatically create backup if max size is reached (default: false). |

**Return Value:**

`bool` -  Returns true if the log written to file, false otherwise.

**Throws:**

- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - If unable to write log to file.

***

### mail

Logs message to an email asynchronously.

This method prepares and sends an error log via email using the configured Mailer.
If sending fails, it logs an error message.

```php
public mail(string $email, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$email` | **string** | The recipient email address. |
| `$message` | **string** | The message to log. |
| `$context` | **array** | Additional context data (optional). |

**Return Value:**

> Note: If error occurs during mailing log, file logger will be used instead.
> If exception occurs during mailing log, file logger with level `exception` be used.

***

### remote

Send log message to a remote URL asynchronously.

This method sends an error log to a specified URL endpoint with details about the error.
If sending fails, it logs an error message.

```php
public remote(string $url_endpoint, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url_endpoint` | **string** | The URL to which the log should be sent. |
| `$message` | **string** | The message to log. |
| `$context` | **array** | Additional context data (optional). |

> Note: If error occurs during network log, file logger will be used instead.
> If exception occurs during network log, file logger with level `exception` be used.

***

### clear

Clears the contents of the specified log file for a given log level.

```php
public clear(string $level): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level whose log file should be cleared (e.g., 'info', 'error'). |

**Return Value:**

`bool` -  Returns true on success, or false on failure if the log file cannot be cleared.

***

### message

Formats a log message with the given level, message, and optional context.

```php
public static message(string $level, string $message, array $context = [], bool $html_context = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level (e.g., 'INFO', 'ERROR'). |
| `$message` | **string** | The primary log message. |
| `$context` | **array** | Optional associative array providing context data. |
| `$html_context` | **bool** | If true the context will be formatted as HTML `&lt;pre&gt;&lt;code&gt;` (default: false). |

**Return Value:**

`string` -  Return the formatted log message.

***

### backup

Creates a backup of the log file if it exceeds a specified maximum size.

```php
protected backup(string $filepath, string $level): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filepath` | **string** | The path to the current log file. |
| `$level` | **string** | The log level, used in the backup file's naming convention. |