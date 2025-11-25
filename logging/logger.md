# Static Logger for Application Logging

***

## Overview

Logger class is a PSR-3 compliant logging system that extends the LoggerInterface. It provides a unified interface for handling application logs and serves as a dispatcher to ensure compatibility.

***

## Introduction

The **Luminova Logger** is the default logging system in Luminova, fully compliant with the PSR `LoggerInterface`. It provides a unified and flexible set of static methods for managing logs across various channels. This class acts as a dispatcher, allowing you to work with any PSR-compliant logger configured in your application.

When `NovaLogger` is used as the default logger, the `dispatch()` method automatically routes critical logs to a remote server, Telegram, or an email address depending on the environment configuration in (`.env`), when running in production.

---

### Key Features

- **PSR-Compliant**: Fully implements `LoggerInterface`, ensuring compatibility with third-party tools and libraries.
- **Preconfigured Default**: Comes ready-to-use as the default logger in Luminova applications.
- **Multi-Channel Support**: Supports logging to local files, remote servers, email, and Telegram.
- **Auto Context Injection**: Automatically includes useful debug information from the current HTTP request body or headers, for easy issue tracking and debugging.

---

### Usage Examples

The Luminova Logger offers multiple access points, making it easy to integrate logging into your application.

#### Global Helper Function

A simple and convenient way to log messages using the global `logger()` helper.

```php
// Log an informational message
logger('info', 'This is an informational message.');

// Log a message to a remote URL
logger('https://example.com/api/log', 'This is a remote log message.');

// Log a message via email
logger('peter@example.com', 'This is an email log message.');

// Log a message via telegram
logger('123456789', 'This is a Telegram log message.');
```

---

#### Accessing the Logger via Factory

Retrieve the logger instance using the application factory:

```php
$logger = factory('logger');
$logger->info('This is an informational message.');
```

Or directly using the `Factory` class:

```php
use Luminova\Application\Factory;

$logger = Factory::logger();
$logger->info('This is an informational message.');
```

---

#### Using Static Methods

Log messages directly through static methods for quick and simple logging:

```php
use Luminova\Logger\Logger;

Logger::info('This is an informational message.');
Logger::error('This is an error message.');
Logger::log('critical', 'This is a critical issue.');
```

---

### Dispatch Logs

**Automatic Log Dispatch in Production**

The `Logger::dispatch()` method allows you to send logs to different destinations based on your environment configuration. It prioritizes the output in the following order:

1. Email (`logger.mail.logs`)
2. Remote URL (`logger.remote.logs`)
3. Telegram Bot (`telegram.bot.token` and `telegram.bot.chat.id`)
4. Fallback to local error log if none are set

```php
Logger::dispatch('error', 'This is an error message.');
```

> **Note:** The dispatch method automatically selects the appropriate destination and is designed to send only critical logs to external channels.

---

#### Send a Log to Email

To send logs via email, configure the `logger.mail.logs` environment variable with a valid email address.

**Manual Dispatching to an Email Address:**

```php
Logger::dispatch('peter@example.com', 'Critical error detected.');
```

Alternatively, you can call the method directly:

```php
Logger::mail('peter@example.com', 'Critical error detected.');
```

---

#### Send a Log to a Remote URL

To send logs to a remote endpoint, set the `logger.remote.logs` variable with the target URL:

**Manual Dispatching to a Remoke Server:**

```php
Logger::dispatch('https://example.com/api/log', 'Critical error detected.');
```

Or use the direct method:

```php
Logger::remote('https://example.com/api/log', 'Critical error detected.');
```

---

#### Send a Log to Telegram

To send logs to a Telegram chat, configure `telegram.bot.chat.id` with the chat ID:

**Manual Dispatching to Telegram Chat:**

```php
Logger::dispatch('123456789', 'Critical error detected.');
```

Or call the method directly:

```php
Logger::telegram('123456789', 'Critical error detected.');
```

> **Note:** Both examples above use the same bot token, configured via the `telegram.bot.token` environment variable.

---

#### Send a Log Using a Different Telegram Bot Token

To send a log using a different bot token (instead of the default configured one), use the `getLogger()->telegram()` method:

```php
Logger::getLogger()->telegram('123456789', 'Bot:Token', 'Log message to Telegram');
```

In this format:
- `'123456789'` is the target chat ID.
- `'Bot:Token'` is the alternative bot token.
- `'Log message to Telegram'` is the message content.

***

### Customization

#### Custom Logger Integration

To use a custom logger instead of the default:
1. Implement the `App\Config\Logger` class's `getLogger` method to return your preferred PSR-compliant logger.
2. The custom logger will automatically handle all logging operations in your application. 

```php
// /app/Config/Logger.php

public function getLogger(): ?LoggerInterface
{
    return new MonologLogger('custom-channel');
}
``` 

> This makes it easy to switch logging implementations while maintaining PSR compliance.

---

#### Auto Log Context

The **Auto Log Context** feature automatically enriches your logs with additional debug information based on the current HTTP request. This is especially useful for tracing issues that occur under specific conditions or with specific users or clients.

**Configuration:**

You can configure the following static properties in your `Logger` configuration file:

```php
// /app/Config/Logger.php
public static ?string $contextFieldName = 'username';          // e.g., from GET/POST data
public static ?string $contextHeaderName = 'X-API-CLIENT-ID';  // e.g., from HTTP headers
```

- **`$contextFieldName`**: Looks for a specific key in `GET`, `POST`, or request payloads and includes its value in the log context.
- **`$contextHeaderName`**: Looks for a specific HTTP header and includes its value in the log context.

> **Example Use Case:**
> Let’s say you’re tracking an intermittent issue reported by a client. By including their unique `X-API-CLIENT-ID` or submitted `username`, logs will automatically contain this identifier, making it much easier to filter and trace.
>
>> When enabled, **core framework logs** (including error and exception logs) will automatically include these context values if they are present in the request.

---

### Multiple Logging

You can combine multiple log messages into a single log entry for better readability and improved performance.

**Using Literal String Logging Entries**

The entries uses the method `Logger::entry()` directly from logger class:

```php
use Luminova\Logger\Logger;
use Luminova\Logger\LogLevel;

$messages = [
    'Debug message one',
    'Debug message two',
    // ...
];

$entry = '';
foreach ($messages as $message) {
    $entry .= Logger::entry(LogLevel::DEBUG, $message);
}

Logger::debug($entry);
```

> This approach uses `Logger::entry()` to build formatted strings before sending them in a single log statement.

---

**Using Log Entry Class**

The entries uses the `Luminova\Logger\Entry` class:

```php
use Luminova\Logger\Logger;
use Luminova\Logger\Entry;
use Luminova\Logger\LogLevel;

$messages = [
    'Debug message one',
    'Debug message two',
    // ...
];

$entry = new Entry();

foreach ($messages as $message) {
    $entry->add(LogLevel::DEBUG, $message);
}

Logger::debug($entry->toString());
```

> The `Entry` class provides a structured way to build multi-line log entries and offers better flexibility for adding context data to each message.

***

## Class Definition

* Class namespace: `Luminova\Logger\Logger`
* This class marked as final

***

## Methods

### constructor

Allows initialization of logger instance.

```php
public __construct(): mixed
```

***

### getLogger

Get the shared instance of the application's PSR-compliant logger.
If no logger is specified in `App\Config\Logger->getLogger()`, the default `NovaLogger` is used.

```php
public static getLogger(): \Psr\Log\LoggerInterface
```

**Return Value:**

`\Psr\Log\LoggerInterface|Luminova\Logger\NovaLogger` -  Return the active logger instance.

***

### emergency

Logs a system emergency (highest severity).

```php
public static emergency(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The emergency message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### alert

Logs an alert that requires immediate action.

```php
public static alert(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The alert message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### critical

Logs a critical condition that requires prompt attention.

```php
public static critical(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The critical message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### error

Logs an error that prevents execution but does not require immediate shutdown.

```php
public static error(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The error message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### warning

Logs a warning about a potential issue.

```php
public static warning(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The warning message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### notice

Logs a normal but significant event.

```php
public static notice(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The notice message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### info

Logs general informational messages.

```php
public static info(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The info message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### debug

Logs debugging information for developers.

```php
public static debug(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The debug message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### exception

Logs an exception with additional context.

```php
public static exception(\Throwable|string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **\Throwable\|string** | The exception message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### php

Logs a PHP runtime error.
Alias for `phpError`, logs PHP-related issues.

```php
public static php(string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The php message to log. |
| `$context` | **array** | Additional context data (optional). |

***

### metrics

Logs performance and metric data.

```php
public static metrics(string $data, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The profiling data to log. |
| `$context` | **array** | Additional context data (optional). |

***

### log

Write log a message at a specified log level.

```php
public static log(string $level, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level (e.g., `LogLevel::INFO`, `emergency`). |
| `$message` | **string** | The log message. |
| `$context` | **array<string,mixed>** | Additional context data (optional). |

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If logger does not implement LoggerInterface

***

### entry

Generates a log entry with an ISO 8601 timestamp (including microseconds).

This method is useful for logging multiple messages that share the same severity level, especially in loops or batch operations. Instead of logging each entry separately, you can construct multiple log entries and log them all at once for better efficiency.

```php
public static entry(string $level, string $message, array $context = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log severity level (e.g., `LogLeve::INFO`, `LogLeve::ERROR`). |
| `$message` | **string** | The main log message. |
| `$context` | **array<string,mixed>** | Optional contextual data for the log entry. |

**Return Value:**

- `string` - Return the formatted log entry in plain text, ending with a newline.

**Example:**

```php
use Luminova\Logger\Logger;
use Luminova\Logger\LogLevel;

$entry = Logger::entry(LogLevel::DEBUG, 'Debug message one');
$entry .= Logger::entry(LogLevel::DEBUG, 'Debug message two');

// Log both entries
Logger::debug($entry);
```

***

### dispatch

Dispatches a log message to a specified destination (file, email, remote server, or telegram bot chat) asynchronously.

The destination is determined by the provided parameter (`$to`) which can be a log level, email address, URL, telegram chat Id or null. Email and remote logging are handled asynchronously. By default, in development, logs are written to a file unless an explicit email address or URL is specified.

In production, if no destination is provided, the method checks for default email, remote URL or telegram chat ID configurations in the environment file (`logger.mail.logs`, `logger.remote.logs` or `telegram.bot.chat.id`).

```php
public static dispatch(string|int|null $to, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$to` | **string\|int\|null** | The destination for the log message (log level, email address, URL, telegram bot chat Id or NULL). |
| `$message` | **string** | The message to log. |
| `$context` | **array** | Additional data to provide context for the log (optional). |

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the provided destination is invalid (not a valid log level, email address, or URL).
- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If network or email logging is attempted in a non-novalogger class, or if the logger does not implement the PSR LoggerInterface.

***

### mail

Sends a log message via email.

This method validates the email address and message, then sends the log message to the specified email address using the configured logger.

```php
public static mail(string $email, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$email` | **string** | The email address to send the log message to. |
| `$message` | **string** | The log message to be sent. |
| `$context` | **array** | Additional context data to be included in the log message (optional). |

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the provided email address is invalid.
- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the logger doesn't support email functionality.

***

### remote

Sends a log message to a remote server via HTTP POST.

This function sends a log message to a specified remote server using HTTP POST. The function validates the URL and ensures that the log message is not empty.
If the URL is invalid or the log message is empty, the function returns without performing any action.

```php
public static remote(string $url, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$email` | **string** | The URL of the remote server to send the log message to. |
| `$message` | **string** | The log message to be sent. |
| `$context` | **array** | Additional context data to be included in the log message (optional). |

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the provided URL is invalid.
- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the logger doesn't support remote logging functionality.

***

### telegram

Sends a log message to a Telegram chat using the Telegram Bot API.

```php
public static telegram(string|int $chatId, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$chatId` | **string\|int** | The chat ID to send the message to. |
| `$message` | **string** | The log message to be sent. |
| `$context` | **array** | Additional context data to be included in the log message (optional). |

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the provided chat Id is invalid.
- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the logger doesn't support telegram logging functionality.
