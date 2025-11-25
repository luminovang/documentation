# Error Handling and Monitoring System

***

## Overview

Luminova’s centralized error handling system. It automatically catches every error in your application and routes it to Luminova’s final controller for logging or presents a friendly UI.

***

## Introduction

Error **Guard** is Luminova's built-in error handling system. It automatically catches all errors, sends them to the right handler, and makes sure they’re either logged or displayed with helpful details.

All methods in Monitor are **static**, meaning you can call them directly without creating an object. This makes it very convenient to use its core features anywhere in your project.

At runtime, Monitor `register()` method is automatically registered inside the `Luminova\Boot` class. This ensures **no error slips through unnoticed**. In most cases, you don’t need to set anything up manually, it just works out of the box.

> **Note:**
> When an error occurs and needs to be displayed, Monitor uses the `Luminova\Foundation\Error\Message` class to ship and present the details.
> This class works like PHP’s built-in `ErrorException`, it carries all the important error information, but it isn’t throwable. Instead, it’s designed purely as a container for error data that can be safely shown to users or logged by the framework.

---

## When Do You Need to Call Monitor Yourself?

Monitor runs mostly on its own. While many of its methods are public, **you don’t need to manually call methods like `register()`, `handle()`, or `shutdown()`**, these are already wired up internally by the framework.

However, some utility methods are useful in application code, especially when you need to debug, customize, or log additional details about errors.

---

## Examples

### Get the Last Error Backtrace

Want to see where the last error came from? Monitor keeps a record of the most recent backtrace.

```php
use Luminova\Foundation\Error\Guard;

print_r(Guard::getBacktrace());
```

This is great for debugging when you need insight into the call stack that led to an error.

---

### Manually Set the Backtrace

Sometimes you may want to set your own backtrace. For example, when handling a custom error manually but still want Monitor to keep it for you.

```php
use Luminova\Foundation\Error\Guard;

// Record a custom backtrace for later debugging
Guard::setBacktrace(debug_backtrace());
```

Now, if you call `Monitor::getBacktrace()`, it will return your manually set information.

---

### Message Prettifier

With message prettifier `Luminova\Foundation\Error\Message::prettify`, you can converts an exception message with special placeholders like (`<link>` and `<highlight>`) into styled output that adapts to both web and CLI environments.

#### How it works

**In web contexts:**

* `<link>URL</link>` → rendered as a clickable link:

  ```html
  <a href="URL" target="_blank" rel="noopener">URL</a>
  ```
* `<highlight color="red">Text</highlight>` → rendered as a span (supports colors):

  ```html
  <span style="color:red">Text</span>
  ```

**In CLI contexts:**

* `<link>URL</link>` → underlined or clickable URL (ANSI if supported).
* `<highlight>Text</highlight>` → highlighted using an ANSI color (defaults to yellow; any color attributes are ignored).

Only valid `http(s)` and `mailto` URLs are converted inside `<link>`.
Highlight colors are ignored in CLI mode and replaced with a single default color.

##### Examples

```php
use Luminova\Foundation\Error\Message;

// Convert placeholders into styled links and highlights
echo Message::prettify(
    'See <link>https://luminova.ng/</link> and <highlight color="red">important</highlight>'
);
```

**Output in a browser:**

```html
See <a href="https://luminova.ng/" target="_blank" rel="noopener">https://luminova.ng/</a>
and <span style="color:red">important</span>
```

**Output in CLI:**

```
See https://luminova.ng/ and (highlighted) important
```

***

## Class Definition

* Class namespace: `Luminova\Foundation\Error\Guard`
* This class is marked as **final** and can't be subclassed

***

## Methods

### guard

Enables Luminova's global error and shutdown handling.

Hooks PHP's error and shutdown events so that:
- All errors are routed through {@see Luminova\Foundation\Error\Guard::handle()}.
- Fatal errors on shutdown are processed by {@see Luminova\Foundation\Error\Guard::shutdown()}.

```php
public static guard(): void
```

***

### handle

Global error handler for recoverable PHP errors.

Invoked automatically for warnings, notices, deprecations, and other recoverable errors.
Skips suppressed errors based on the current `error_reporting()` level.

Behavior:
- In non-production with `display_errors` enabled:
  Throws critical errors or prints formatted error messages.
- In production or with `display_errors` disabled:
  Logs the error in a structured format.

```php
public static handle(int $severity, string $message, string $file, int $line): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$severity` | **int** | The error severity level. |
| `$message` | **string** | The error message. |
| `$file` | **string** | The full path to the file where the error occurred. |
| `$line` | **int** | The line number of the error. |

**Return Value:**

`bool` - Return true if handled, false if suppressed.

***

### shutdown

Shutdown handler for fatal or last-minute errors.

Executes when the script terminates, checking for a final error from `error_get_last()`.

Behavior:
- Passes error data to `Application::onShutdown()`, stopping if handled.
- Displays a fatal error page if needed (production or fatal).
- Logs the error if hidden from display or in production.

```php
public static shutdown(): void
```

***

### trigger

Throws an ErrorException with the given message, code, and optional file/line.

```php
public static trigger(string $message, string|int $code = ErrorCode::USER_NOTICE, ?string $file = null, ?int $line = null): never
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | Error message. |
| `$code` | **string&#124;int** | Error code (default: `Luminova\Exceptions\ErrorErrorCode::USER_NOTICE`). |
| `$file` | **string&#124;null** | Optional file where the error occurred. |
| `$line` | **int&#124;null** | Optional line where the error occurred. |

**Throws:**

- [Luminova\Exceptions\ErrorException](/error-handlers/exceptions.md#errorexception) - Always throw error when called.

***

### deprecate

Issues a deprecation warning or logs it in production.

- In development: triggers a PHP `E_USER_DEPRECATED` warning.
- In production: logs the warning at `notice` level if `$force` is `false`.

```php
public static deprecate(string $message, string $since = '', ?array $arguments = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | Deprecation message (supports placeholders). |
| `$since` | **string** | Version when the deprecation started (e.g., `1.5.0`). |
| `$arguments` | **array&#124;null** | Optional values to replace placeholders in the message. |
| `$force` | **bool** | Force triggering warning even in production (default: false). |

**Return Value:**

`bool` - Returns true if the message was logged or triggered.

**Example:**

Simple deprecation warning:

```php
Guard::deprecate('Method foo() is deprecated. Use getFoo() instead.');
```

Formatted deprecation warning:

```php
Guard::deprecate('Method %s() is deprecated. Use %s() instead.', '1.5.0', ['foo', 'getFoo']);
```

***

### setBacktrace

Stores the last debug backtrace in the shared error context.

Accessible via `Guard::getBacktrace()`.
Can either replace the current backtrace or prepend to it.

```php
public static setBacktrace(array $backtrace, bool $push = true): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$backtrace` | **array** | Array of backtrace information. |
| `$push` | **bool** | If true (default), prepends to the existing backtrace. |

***

### setLastErrorCode

Stores the last error code in the shared error context.

Accessible via `Monitor::getCode()`.

```php
public static setLastErrorCode(string|int $code): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$code` | **string&#124;int** | The last error code value. |

***

### getCode

Retrieves the last stored error code or a default value.

```php
public static getCode(string|int $default = E_ERROR): string|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$default` | **string&#124;int** | Default error code if none was stored (default: `E_ERROR`). |

**Return Value:**

`string|int` - Return the last stored error code, or the provided default.

***

### getBacktrace

Retrieves the last debug backtrace from the shared error context.

This method accesses a shared memory `$trace` to retrieve
the stored debug backtrace. If the backtrace is not set, it returns an empty array.

```php
public static getBacktrace(): array
```

**Return Value:**

`array` - Return the debug backtrace or an empty array if not available.

***

### sanitizeMessage

Sanitize error message.

This method sanitizes an error message by removing the application root path and trimming extraneous details.

Strips out the `APP_ROOT` path to avoid exposing sensitive directory structures,
and removes trailing phrases like "thrown in" for cleaner output.

```php
public static sanitizeMessage(string $message): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The original error message. |

**Return Value:**

`string` - Returns the sanitized error message.

***

### findCode

Determines the most relevant exception code from an error message or severity.

- Extracts a numeric code from messages like: **"Uncaught Exception: (123)"**.
- Maps "Call to undefined" errors to `AppException::UNDEFINED`.
- Falls back to the current stored code or provided severity if it has a known name.

```php
public static findCode(string $message, string|int $severity = E_ERROR): string|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The error message to inspect. |
| `$severity` | **string&#124;int** | Fallback severity/type code (default: `E_ERROR`). |

**Return Value:**

`string|int` - Return the extracted or derived error code.

***

### notify

Display a basic error message when no error handler is available.

```php
public static notify(int $retryAfter = 60): never
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$retryAfter` | **int** | Number of seconds before the client should retry (default: 60). |

***

### isFatal

Determine if the error is fatal.

Fatal errors are unrecoverable and halt script execution immediately.

This includes:
- Core runtime errors (`E_ERROR`)
- Parse errors (`E_PARSE`)
- Core startup errors (`E_CORE_ERROR`)
- Compile-time errors (`E_COMPILE_ERROR`)

```php
public static isFatal(string|int $severity): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$severity` | **string&#124;int** | PHP error severity level (e.g, `E_*`). |

**Return Value:**

`bool` - Return true if the error is fatal, false otherwise.

***

### isCritical

Determine if the error is critical.

Critical errors are severe problems that require immediate attention, but may not always halt execution. This includes:
- All fatal errors (see isFatal())
- User-generated fatal errors (E_USER_ERROR)
- Recoverable fatal errors (E_RECOVERABLE_ERROR)

```php
public static isCritical(string|int $severity): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$severity` | **string&#124;int** | PHP error severity level (e.g, `E_*`). |

**Return Value:**

`bool` - Return true if the error is critical, false otherwise.

***

### getLevel

Get error logging level.

```php
public static getLevel(string|int $severity): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$severity` | **string&#124;int** | The error code or type. |

**Return Value:**

`string` - Return error log level by error code.