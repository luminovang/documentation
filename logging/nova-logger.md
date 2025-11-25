# Default PSR-Compliant Logger for Luminova Application Logging System

***

## Overview

The NovaLogger class is the default logging system for Luminova applications. It supports logging to files, sending logs via email, and dispatching logs to a remote server URL.

***

## Introduction

The **NovaLogger** class serves as the default logging system for the Luminova framework, offering robust features for managing application logs. It supports logging messages, creating log backups, and dispatching log entries to emails or remote servers.

If preferred other logger, `NovaLogger` can be replaced with any PSR-compliant logger (e.g., `Monolog`) by configuring the `App\Config\Logger` class's `getLogger` method. This allows developers to easily channel all logging operations to a custom logger.

---

### Key Features

1. **Default Logger**: Preconfigured as the default logger for Luminova applications.
2. **Versatile Logging Options**: Supports logging to files, sending logs via email, or dispatching logs to remote servers.
3. **Asynchronous Logging**: Leverages asynchronous operations for non-blocking logging (enabled in the logger configuration).
4. **Configurable**: Allows customization, such as setting log file extensions or dispatching methods.

---

### Usage

The `NovaLogger` is preconfigured as the default logger in `Luminova\Logger\Logger`. Custom initialization is typically unnecessary unless specific customization, such as changing log file extensions, is required.

#### Example: Using NovaLogger

Initialize NovaLogger with custom log file extension:

```php
use Luminova\Logger\NovaLogger;

$logger = new NovaLogger('app', '.txt');
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
$logger->setLevel('error')
	->mail('peter@example.com', 'This is a log sent via email');
```

Dispatch logs to a remote server

```php
$logger->setLevel('debug')
	->remote('https://example.com/api/log', 'This is a log sent to a remote server');
```

> **Note**: When using `NovaLogger` with `Luminova\Logger\Logger`, you can redirect logs to a file, email, or remote server by using the `dispatch` method. This ensures logs are sent to the appropriate destination based on their type.

---

### Customization

To replace `NovaLogger` with another PSR-compliant logger:
1. Implement the `getLogger` method in `App\Config\Logger` to return your preferred logger. Ensure your custom logger adheres to the PSR-3 `LoggerInterface`. 
2. Implement the `getEmailLogTemplate` to return a custom html template for mail logging, return null to use default template. 

Example:

```php
// /app/Config/Logger.php
namespace App\Config;

use Luminova\Base\Configuration;
use Luminova\Interface\HttpRequestInterface;
use Monolog\Logger as MonologLogger;
use Monolog\Handler\StreamHandler;
use Psr\Log\AbstractLogger;

class Logger extends Configuration 
{
    public function getLogger(): ?LoggerInterface
    {
        return (new MonologLogger('custom-channel'))
            ->pushHandler(new StreamHandler('path/to/your.log'));
    }

    public static function getEmailLogTemplate(
        HttpRequestInterface $request, 
        AbstractLogger $logger,
        string $message, 
        string $level, 
        array $context
    ): ?string 
    {
        return "<template>{$message}</template>";
    }
}
```

***

## Class Definition

* Class namespace: `Luminova\Logger\NovaLogger`
* Parent class: [\Psr\Log\AbstractLogger](https://www.php-fig.org/psr/psr-3/#3-psrabstractlogger)

---

## Properties

### maxSize

The maximum log size in bytes.

```php
protected int $maxSize = 0;
```

***

### createBackup

Flag indicating if backup should be created.

```php
protected bool $createBackup = false;
```

***

### extension

The log file extension type (e.g, `.txt`, `.log`).

```php
protected string $extension = '.log';
```

***

### name

The logging system name (e.g, `app`, `foo`).

```php
protected string $name = 'default';
```

***

## Methods

### constructor

Initialize NovaLogger class instance.

```php
public __construct(string $name = 'default', string $extension ='.log'): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The logging system name (default: `default`). |
| `$extension` | **string** | The log file name extension (default: `.log`). |

***

### setName

Sets the log name for application identifier.

```php
public setName(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The Logging system name. |

**Return Value:**

`self` -  Returns the instance of NovaLogger class.

***

### setMaxLogSize

Sets the maximum size for log files.

This method allows setting a custom maximum size for log files. When a log file reaches this size, it may trigger backup or clearing operations depending on other configuration settings.

```php
public setMaxLogSize(int $size): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$size` | **int** | The maximum size of the log file in bytes. |

**Return Value:**

`self` -  Returns the instance of NovaLogger class.

***

### setAutoBackup

Enables or disables automatic log file backup based on maximum log size configuration.

When enabled, old log files will be automatically archived to prevent excessive log size and improve performance.

```php
public setAutoBackup(bool $backup = true): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$backup` | **bool** | Set to `true` to enable auto-backup, `false` to disable it (default: `true`). |

**Return Value:**

`self` -  Returns the instance of NovaLogger class.

***

### setLevel

Sets the default log level for the `remote` or `email` logging.

When sending logs via email or to a remote server, the default log level will be use in case of failure. 
It also serves as an indicator of log severity.

```php
public setLevel(string $level): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to set (e.g., 'error', 'info', 'debug'). |

**Return Value:**

`self` -  Returns the instance of NovaLogger class.

> **Note:** Setting log level with this method doesn't affect `log` and `write` methods.
> This is only for `remote` and `mail` logging, to identify the type of log level that triggered the logging.

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
| `$level` | **string** | The log level (e.g., `emergency,` `error,` `info`). |
| `$message` | **string** | The log message. |
| `$context` | **array** | Optional associative array providing context data. |

**Throws:**

- [Luminova\Exceptions\FileException](/error-handlers/exceptions.md#fileexception) - If unable to write log to file.

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

> **Note:** If error occurs during mailing log, file logger will be used instead.
> If exception occurs during mailing log, file logger with level `exception` be used.

***

### remote

Send log message to a remote URL asynchronously.

This method sends an error log to a specified URL endpoint with details about the error.
If sending fails, it logs an error message.

```php
public remote(string $url, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The URL to which the log should be sent. |
| `$message` | **string** | The message to log. |
| `$context` | **array** | Additional context data (optional). |

> Note: If error occurs during network log, file logger will be used instead.
> If exception occurs during network log, file logger with level `exception` be used.

***

### telegram

Sends a log message to a Telegram chat using the Telegram Bot API.

This method sends an error log to a telegram chat bot, allowing to get instant notification on telegram when error occurs in your application.

```php
public telegram(string|int $chatId, string $token, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$chatId` | **string\|int** | The telegram chat ID to send the message to. |
| `$string` | **string** | The telegram bot token. |
| `$message` | **string** | The log message to send. |
| `$context` | **array** | Additional contextual data related to the log message. |

**Setup Steps for Telegram Integration**  

**1. Create a Telegram Bot**  
- Open [BotFather](https://t.me/botfather).  
- Create a new bot and copy the generated **bot token**.  

**2. Manually Configure Environment Variables**  
- Send a message (e.g., `"Hi!"`) to your bot.  
- Retrieve your **chat ID** using the following API:  
  ```bash
  https://api.telegram.org/bot<TOKEN>/getUpdates
  ```
- Set the environment variables in your configuration for global use:  
  ```ini
  telegram.bot.token = <TOKEN>
  telegram.bot.chat.id = <CHAT_ID>
  ```

**3. Automatically Configure via CLI**  
Use the `novakit` command to set up the Telegram bot:  
```bash
php novakit env:setup -t=telegram
Enter your Telegram bot token: <TOKEN>
```

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

Constructs a formatted log message with an ISO 8601 timestamp (including microseconds).

```php
public message(string $level, string $message, array $context = [], bool $htmlFormat = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level (e.g., `LogLevel::DEBUG`, `info`, `error`). |
| `$message` | **string** | The primary log message. |
| `$context` | **array<string,mixed>** | Optional associative array providing context data. |
| `$htmlFormat` | **bool** | Whether to format the message and context as HTML (default: false).|

**Return Value:**

`string` -  Return the formatted log message.

***

### write

Writes a log message to the specified file. This doesn't support no use asynchronous logging.

```php
protected write(string $level, string $message, array $context = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level (e.g., 'info', 'error', 'debug'). |
| `$message` | **string** | The primary log message. |
| `$context` | **array** | Optional associative array providing context data. |

**Return Value:**

`bool` -  Returns true if the log written to file, false otherwise.

**Throws:**

- [Luminova\Exceptions\FileException](/error-handlers/exceptions.md#fileexception) - If unable to write log to file.

***

### backup

Creates a backup of the log file if it exceeds a specified maximum size.

```php
public backup(string $level): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to create backup for. |

**Return Value:**

`bool` -  Return true if the backup was created, otherwise false.