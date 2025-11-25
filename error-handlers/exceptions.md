# Abstract Base Exception for Application

***

## Overview

The AppException class is Luminova’s base for handling errors, providing a consistent way to catch, log, and respond to unexpected issues using built-in exceptions and the Tripwire error system.

***

## Introduction

The **AppException** class is the foundation of Luminova's exception classes. Exception handling is essential for maintaining application stability and ensuring a smooth user experience when unexpected issues occur.

Luminova provides a rich collection of exception classes along with custom helper methods, all working seamlessly with the [Core Error Monitoring System](/error-handlers/error-monitoring-system.md). Together, they give you the tools to detect problems, record them, and present meaningful feedback without leaving your users confused.

This guide explains how Luminova organizes its exceptions, how you can use them in your projects, and how they help keep your application resilient. By understanding and using these classes, you can ensure that errors are caught, logged, and handled cleanly, keeping disruptions to a minimum. 

> For a complete list of built-in exception codes, see [Exception and Error Codes](/error-handlers/error-codes.md) documentation.

---

## Examples

Luminova ships with both general-purpose exceptions such as `ErrorException` and specialized ones like `DatabaseException` and `Http\ClientException`. This guide shows you how to throw and catch these exceptions in real projects, and how to customize them when your application has unique needs.

### Extending the AppException Class

When you create your own exception by extending `AppException`, you gain access to all of its core features automatically. This makes it simple to build exceptions that fit your project while keeping consistent behavior with the rest of the framework.

```php
// /app/Exceptions/CustomException.php

/**
 * Custom exception class that extends Luminova AppException.
 *
 * @template T
 * @extends Luminova\Exceptions\AppException<T>
 */
namespace App\Exceptions;

use Luminova\Exceptions\AppException;
use Throwable;

class CustomException extends AppException
{
    public function __construct(
        string $message,
        string|int $code = 0,
        ?Throwable $previous = null
    ) {
        parent::__construct($message, $code, $previous);
    }
}
```

---

### Creating a Custom Exception Without AppException

You can define your own exception class without extending Luminova’s `AppException`. In this case, you should implement the `Luminova\Interface\ExceptionInterface` to ensure compatibility with Luminova’s error system.

```php
// /app/Exceptions/CustomException.php

/**
 * Custom class that implements the Luminova ExceptionInterface.
 *
 * @template \T
 * @implements \T<Luminova\Interface\ExceptionInterface>
 */
namespace App\Exceptions;

use Luminova\Interface\ExceptionInterface;
use \Exception;
use \Throwable;

class CustomException extends Exception implements ExceptionInterface
{
    public function __construct(
        string $message,
        string|int $code = 0,
        ?Throwable $previous = null
    ) {
        parent::__construct($message, $code, $previous);
    }

    // You can add your own methods or properties here as needed
}
```

> **Note** 
> When doing this, remember that exception codes may be either strings or integers, so you need to account for both.

---

### Throwing Exceptions

In luminova, you can throw an exception in traditional way using `throw` statement and `new Object` or using the `throwException` method to throw and exception gracefully.

**The usual way**

You can throw an exception directly whenever something goes wrong:

```php
use Luminova\Exceptions\RuntimeException;

throw new RuntimeException('Invalid configuration file.');
```

**Graceful throwing based on environment**

Luminova provides helper methods such as `throwException` to handle errors differently depending on whether the app is running in production or development. In production, non-fatal errors are logged quietly, while fatal errors show only a basic “something went wrong” message without exposing sensitive details.

```php
use Luminova\Exceptions\RuntimeException;

RuntimeException::throwException('Invalid configuration file.');
```

**Throwing an error when input is missing**

```php
use Luminova\Exceptions\ErrorCode;
use Luminova\Exceptions\ErrorException;

function loginAccount(string $username, string $password) 
{
    if (!$username || !$password) {
        throw new ErrorException(
            'Username and password are required.',
            ErrorCode::INVALID_ARGUMENTS
        );
    }
}
```

---

### Throwing as Another Exception Type

You can also rethrow errors as a different exception type. This is helpful when you need to unify how different errors are handled in your application.

```php
use Luminova\Exceptions\ErrorException;
use Luminova\Exceptions\AppException;
use Luminova\Exceptions\RuntimeException;

// Convert a generic Error into a RuntimeException
AppException::throwAs(
    new Error('An error occurred'),
    RuntimeException::class
);

// Convert a generic Error into an ErrorException
ErrorException::throwAs(new Error('An error occurred'));
```

---

### Catching Exceptions

Exceptions are usually handle using `try`, `catch` and `finally` blocks. This lets you intercept problems and respond appropriately rather than letting the application crash.

For example, you might show a user-friendly message in production while logging the full details for developers to review later.

#### Handling an Exception Code

You can catch exceptions thrown by the (e.g, `loginAccount`) function using a `try-catch` block. Below is an example of how to handle different types of exceptions based on their error codes.

```php
use Luminova\Exceptions\ErrorCode;

try {
    $user = loginAccount($username, $password);
} catch (Throwable $e) {
    // Handle errors based on the error code.

    if ($e->isCode(ErrorCode::INVALID_ARGUMENTS)) {
        echo "Username and password are required.";
    } elseif ($e->isCode(ErrorCode::VALUE_FORBIDDEN)) {
        echo "Access to the requested value is forbidden.";
    } else {
        echo "Error: " . $e->getMessage();
    }
}
```

#### Handling a Specific Exception Type

You can catch a specific exception type, such as `ErrorException`, for more granular error handling.

```php
try {
    $user = loginAccount($username, $password);
} catch (Luminova\Exceptions\ErrorException $e) {
    // Handle specific errors here.
    echo $e->getMessage();
} catch (Luminova\Exceptions\RuntimeException $e) {
    // Handle specific errors here.
    echo $e->getMessage();
} catch (\Throwable $e) {
    // Handle specific errors here.
    echo $e->getMessage();
}
```

***

#### Catch and Logging Exceptions

In cases where you want to log the exception rather than handle it directly, you can do so as follows:

```php
try {
    $pdo = loginAccount($username, $password);
} catch (\Throwable $e) {
    // Log if it's not a core PHP exception type.
    // To ensure that log method exists
    if ($e instanceof Luminova\Exceptions\AppException) {
        // Log AppException details.
        $e->log('debug');
        return;
    } 

    // Log PHP exceptions using a logger.
    Logger::debug($e->getMessage());
}
```

***

#### Catch and Handle Exceptions

Based on Environment, you can manage exceptions differently based on your environment, throwing exceptions during development and logging them in production:

```php
use Luminova\Exceptions\AppException;
use Exception;

try {
    $pdo = loginAccount($username, $password);
} catch (\Throwable $e) {
    if ($e instanceof Luminova\Exceptions\AppException) {
        // Handle AppException accordingly.
        $e->handle(); 
        return;
    }

    // Handle generic PHP exceptions gracefully.
    if(PRODUCTION){
        Logger::error($e->getMessage());
        return;
    }

    throw $e;
}
```

***

## Class Definition

* Class namespace: `Luminova\Exceptions\AppException`
* Parent class: [\Exception](https://www.php.net/manual/en/language.exceptions.php)
* This class implements: [Luminova\Interface\ExceptionInterface](/interface/classes.md#exceptioninterface), [Stringable](https://www.php.net/manual/en/class.stringable.php)
* This class is an **Abstract class**

***

## Methods

### constructor

Create a new exception instance.

When an exception object is created with a message, an optional code, and a previous exception, it can be thrown using the `throw` keyword or pass the object to methods or return types.

```php
public __construct(string $message, string|int $code = 0, ?Throwable $previous = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The error message for the exception. |
| `$code` | **string&#124;int** | The exception code as a string or integer (default: `0`). |
| `$previous` | **\Throwable&#124;null** | The previous exception object, if available (default: `null`). |

**Usages Example**

Initialize and throws an exception:

```php
use Luminova\Exceptions\AppException;
use Luminova\Exceptions\RuntimeException;

try{
	throw new RuntimeException('An error occurred');
}catch(AppException $e){
	// handle exception
}
```

***

### isCode

Checks if the current exception's code matches a given code or any in an array.

Compares the provided code(s) against the exception's string code (if available) or its numeric code as a fallback.

```php
public isCode(string|array<int,string|int>|int $code): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$code` | **string\|array\|int** | The code or list of codes to compare against. |

**Return Value:**

`bool` - Returns `true` if a match is found, otherwise `false`.

***

### setCode

Sets the exception code.

```php
public setCode(string|int $code): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$code` | **string&#124;int** | The string or integer representation of the exception code. |

**Return Value:**

`self` - Returns the current instance for method chaining.

***

### setFile

Sets the file where the error occurred.

```php
public setFile(string $file): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **string** | The file where the error occurred. |

**Return Value:**

`self` - Returns the current instance for method chaining.

***

### setLine

Sets the line number where the error occurred.

```php
public setLine(int $line): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$line` | **int** | The line number of the error. |

**Return Value:**

`self` - Returns the current instance for method chaining.

***

### getDescription

Get a formatted message.

This method returns a filtered exception message, ensuring messages doesn't contain any sensitive information like private server paths.

```php
public getDescription(): string
```

**Return Value:**

`string` - Return the filtered exception message.

**Example:**

You can retrieve a filtered error message that omits file details:

```php
use Luminova\Exceptions\ErrorException;

try {
    $pdo = loginAccount($username, $password);
} catch (ErrorException $e) {
    echo $e->getDescription(); // Get a user-friendly error message.
}
```

***

### getName

Get an exception error name.

This method returns a humanized exception name based on the exception code.

```php
public getName(): string
```

**Return Value:**

`string` - Return the name of the thrown exception.

**Example:**

To retrieve a user-friendly name for the exception, use the following method:

```php
use Luminova\Exceptions\ErrorException;

try {
   $pdo = loginAccount($username, $password);
} catch (ErrorException $e) {
   echo $e->getName(); // Get a human-readable name for the error.
}
```

***

### getErrorCode

Get a **string** or an **integer** error code for thrown exception.

Unlike `getCode` method, this method returns an integer or string error code of the exception. It first checks if a string error code is set (`$strCode`), if not, falls back to the numeric error code.

```php
public getErrorCode(): string|int
```

**Return Value:**

`string|int` - Return the error code as either a string or an integer. Returns the string error code if set, otherwise returns the numeric error code.

***

### getBacktrace

Retrieve the las debug tracer.

This attempt to get last debug backtrace from the exception or shared error context.

```php
public getBacktrace(): array
```

**Return Value:**

`array` - Return the debug backtrace, or an empty array if not available.

> This method checks for the exception backtrace, if not available it checks for the stored debug backtrace in the shared memory `__ERROR_DEBUG_BACKTRACE__` if still unavailable, it returns an empty array.

**Example:**

You can obtain backtrace information from an exception:

```php
use Luminova\Exceptions\ErrorException;

try {
   $pdo = loginAccount($username, $password);
} catch (ErrorException $e) {
   print_r($e->getBacktrace()); // Print backtrace for debugging.
}
```

***

### toString

Returns a formatted error message with code, file, and line details.

> **Format:** 
> The message returns in this format: `Exception: (code) message in file/path/foo.php on line N`.

```php
public toString(): string
```

**Return Value:**

`string` - Return the formatted error message with code, file, and line number.

**Example:**

```php
use Luminova\Exceptions\RuntimeException;

try{
   throw new RuntimeException('An error occurred');
}catch(RuntimeException $e){
   echo $e->toString();
   // Output: Exception: (0) An error occurred in /path/to/file.php on line 12
}
```

***

### log

Logs the exception message to the configured logger.

Uses `App\Config\Logger`. If asynchronous logging is enabled, logs use Fiber for async processing. In production, if `logger.mail.logs` or `logger.remote.logs` is set, logs are sent via email or to a remote server.

**Log Levels:**

- `emergency` - Log emergency error that need attention.
- `alert` - Log alert message.
- `error` - Log minor error.
- `warning` - Log a warning message.

For more log level see [Log Levels](/logging/levels.md) documentation.

```php
public log(string $dispatch = 'exception'): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$dispatch` | **string** | A log level, email address, or remote URL to send the error to (default: `exception`). |

**Example:**

This example demonstrates catching an exception and logging an exception message with a custom log level.

```php
use Luminova\Exceptions\RuntimeException;

try{
	throw new RuntimeException('An error occurred');
}catch(RuntimeException $e){
	$e->log('debug');
}
```

***

### handle

Handles exceptions safely depending on the application environment and error type.

This method ensures that exceptions are processed appropriately:
- In CLI mode: either re-throws the exception (if enabled) or shows a CLI-friendly error detail.
- In production: logs the error, shows a user-friendly page for fatal errors, and prevents leaks of sensitive information.
- In development: re-throws the exception so it can be displayed directly.

The handler also prevents recursive exception handling and ensures consistent shutdown behavior.

```php
public handle(): void
```

**Throws:**

- `Luminova\Interface\AppException<\T, Throwable>` - Re-throws the exception in non-production environments or when explicitly configured for CLI.

**Example:**

This below example shows how you can catch an exception and handle it with `handle` method.

```php
use Luminova\Exceptions\RuntimeException;

try{
	throw new RuntimeException('An error occurred');
}catch(RuntimeException $e){
	$e->handle();
}
```

***

### throwException

Creates and handles an exception gracefully.

```php
public static throwException(string $message, string|int $code = 0, ?\Throwable $previous = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The exception message. |
| `$code` | **string&#124;int** | The exception code (default: `0`). |
| `$previous` | **\Throwable&#124;null** | The previous exception, if available (default: `null`). |

**Throws:**

- `Luminova\Exceptions\AppException<\T>` - Throws in development environment or if the exception code is fatal, otherwise log the information.

**Example:**

The following example demonstrates how to handle an exception  gracefully in a production environment. 
For more error codes, refer to [Exceptions and Error Handling](/error-handlers/error-codes.md).

```php
use Luminova\Exceptions\ErrorCode;
use Luminova\Exceptions\AppException;
use Luminova\Exceptions\RuntimeException;

try {
    throw new RuntimeException('An error occurred');
} catch (\Throwable $e) {
    if($e instanceof AppException) {
        $e->handle();
        return;
    }

    // If not instance of AppException, create safe exception handler from the exception object.
    RuntimeException::throwException(
        $e->getMessage(), 
        ErrorCode::COMPILE_ERROR,
        $e
    );
}
```

***

### throwAs

Rethrow or handle an exception as a specified exception class.

If the given Throwable is already an instance of `Luminova\Exceptions\AppException`, it will be handled directly. Otherwise, a new exception of the specified class (or the current class if not provided) will be created with the same message, code, and previous exception, then handled.

```php
public static throwAs(\Throwable $e, ?string $abstract = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$e` | **\Throwable** | The original exception object to be thrown or handled. |
| `$abstract` | **class-string<AppException>&#124;null** | The new exception class to throw as (e.g., `Luminova\Exceptions\RuntimeException`). Defaults to the current class if null. |

**Throws:**

- `\T<Throwable>` - Throws the exception from the called class.

**Example:**

Throw `Error` exception as `RuntimeException` and handle it gracefully:

```php
use Luminova\Exceptions\AppException;
use Luminova\Exceptions\RuntimeException;

try {
    throw new \Error('An error occurred');
} catch (\Throwable $e) {
    if($e instanceof RuntimeException) {
        $e->handle();
        return;
    }

    AppException::throwAs($e, RuntimeException::class);
}
```

***

## Available Exception Classes

Luminova provides a robust set of predefined exception classes that extend the `Luminova\Exceptions\AppException` class. These exceptions cover a wide range of error scenarios, ensuring that your application can handle various issues gracefully.

The global namespace group for these classes is: `Luminova\Exceptions\`.

| Class                        | Description                                                                 |
|------------------------------|-----------------------------------------------------------------------------|
| `AppException`               | The base exception class for all Luminova-specific exceptions.              |
| `BadMethodCallException`     | Thrown when a callback refers to an undefined method or an invalid context. |
| `CacheException`             | Represents errors related to caching operations.                            |
| `ClassException`             | Indicates issues related to class handling, such as instantiation failures. |
| `CookieException`            | Thrown when there are problems with cookie operations.                      |
| `DatabaseException`          | General exception for database-related errors.                              |
| `DatabaseLimitException`     | Raised when a query exceeds predefined database limits.                     |
| `DateTimeException`          | Handles errors related to date and time operations.                         |
| `EncryptionException`        | Indicates failures in encryption or decryption processes.                   |
| `ErrorException`             | Represents generic errors that don't fall under a specific category.        |
| `FileException`              | Thrown when file-related operations fail, such as reading or writing files. |
| `InvalidArgumentException`   | Raised when an argument provided to a method is invalid or out of range.    |
| `InvalidException`           | General exception for invalid operations or inputs.                         |
| `InvalidObjectException`     | Indicates that an object is invalid or not in the expected state.           |
| `JsonException`              | Handles errors related to JSON encoding or decoding.                        |
| `LogicException`             | Handles errors related to implementation and logic issues.                  |
| `MailerException`            | Thrown when email sending operations fail.                                  |
| `NotFoundException`          | Raised when a required resource or entity cannot be found.                  |
| `RouterException`            | Represents issues related to routing operations within the application.     |
| `RuntimeException`           | General exception for runtime errors that occur during application execution.|
| `SecurityException`          | Indicates security-related issues, such as unauthorized access attempts.    |
| `StorageException`           | Thrown when there are issues with storage operations, such as saving or retrieving data. |
| `ValidationException`        | Raised when data fails to pass validation checks.                           |
| `ViewNotFoundException`      | Thrown when a requested view file cannot be located.                        |

***

### Network-Related Exception Classes

Luminova also provides custom exception classes specifically for handling network-related errors. These exceptions may be thrown when using the [Luminova\Http\Network](/http/request.md) class.

The global namespace group for `Http` exception classes is: `Luminova\Exceptions\Http\`.

| Class                    | Description                                                                   |
|--------------------------|-------------------------------------------------------------------------------|
| `ClientException`        | Represents client-side errors during HTTP requests, such as 4xx status codes. |
| `ConnectException`       | Thrown when a connection to a remote server fails.                            |
| `RequestException`       | Handles errors that occur during the creation or processing of HTTP requests. |
| `ServerException`        | Represents server-side errors encountered during HTTP requests, such as 5xx status codes. |