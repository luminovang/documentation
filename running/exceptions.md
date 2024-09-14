# Exception Handling and Custom Exception Classes

***

## Overview

Comprehensive Guide to Luminova Exception Classes: Detailed Documentation for Extending, Catching, Graceful Error, and Exception Handling in Luminova Applications.

***

## Introduction

In any robust application, proper error and exception handling are crucial to maintaining stability and ensuring a smooth user experience. The Luminova framework provides a comprehensive set of exception classes that enable developers to manage errors effectively and gracefully. This documentation offers an in-depth look at the various exception classes available within Luminova, guiding you through their usage to handle different types of errors with precision.

Whether you are dealing with runtime errors, validation issues, or network-related exceptions, Luminova’s exception classes are designed to simplify the process of error management. By understanding and utilizing these classes, you can enhance the resilience of your application, ensuring that errors are caught, logged, and handled in a manner that minimizes disruption.

This guide covers everything from basic exceptions like `ErrorException` to more specific ones like `DatabaseException` and `Http\ClientException`. It also provides practical examples of how to implement and throw exceptions based on different error conditions. By the end of this guide, you'll have a clear understanding of how to leverage Luminova's exception handling capabilities to build more reliable and user-friendly applications.

***

* Base Exception namespace: `\Luminova\Exceptions\AppException`
* Parent class: `\Exception`
* This class implements:
[\Luminova\Interface\ExceptionInterface](/interface/classes.md#exceptioninterface)
* This class is an **Abstract class**

***

## Usage Guide

### Extending the Exception Class

When you extend the Luminova `AppException` class, you gain access to all its built-in features.

```php
// /app/Exceptions/CustomException.php

<?php
/**
 * Custom exception class that extends the Luminova `AppException`.
 *
 * @template T
 * @extends \Luminova\Exceptions\AppException<T>
 */
namespace App\Exceptions;
use \Luminova\Exceptions\AppException;
use \Throwable;

class CustomException extends AppException
{
    public function __construct(string $message, string|int $code = 0, ?Throwable $previous = null)
    {
        parent::__construct($message, $code, $previous);
    }
}
```

***

### Custom Exception Without Extending AppException

You can also define your own exception class without extending the Luminova `AppException` class. In this case, implement the `\Luminova\Interface\ExceptionInterface` to maintain compatibility.

Additionally, you need to handle cases where the exception code might be a `string` instead of an `integer`.

```php
// /app/Exceptions/CustomException.php

<?php
/**
 * Custom class that implements the Luminova `ExceptionInterface`.
 *
 * @template T
 * @implements \Luminova\Interface\ExceptionInterface<T>
 */
namespace App\Exceptions;

use \Luminova\Interface\ExceptionInterface;
use \Exception;
use \Throwable;

class CustomException extends Exception implements ExceptionInterface
{
    public function __construct(string $message, string|int $code = 0, ?Throwable $previous = null)
    {
        parent::__construct($message, $code, $previous);
    }
}
```

***

### Exception Handling

Error handling in Luminova is primarily accomplished using `try-catch` blocks. This approach allows you to catch exceptions and respond appropriately based on their error codes. Below are examples demonstrating how to handle various types of errors. For a complete list of available exception and error codes, please refer to the [Documentation here](/running/error-codes.md).

#### Throwing an Error

```php
use Luminova\Exceptions\ErrorException;

function loginAccount(string $username, string $password) 
{
    if (!$username || !$password) {
        throw new ErrorException(
            'Username and password are required.',
            ErrorException::INVALID_ARGUMENTS
        );
    }
}
```

***

#### Throwing Custom Exceptions

To throw a custom exception:

```php
<?php
use \App\Exceptions\CustomException;

throw new CustomException('Invalid configuration file.');
```

To throw the exception gracefully:

```php
<?php
use \App\Exceptions\CustomException;

CustomException::throwException('Invalid configuration file.');
```

***

#### Catch an Exception that Was Thrown

You can catch exceptions thrown by the `loginAccount` function using a `try-catch` block. Below is an example of how to handle different types of exceptions based on their error codes.

```php
<?php
use Luminova\Exceptions\AppException;

try {
    $pdo = loginAccount($username, $password);
} catch (AppException $e) {
    // Handle errors based on the error code.
    if ($e->getCode() === AppException::INVALID_ARGUMENTS) {
        echo "Username and password are required.";
    } elseif ($e->getCode() === AppException::VALUE_FORBIDDEN) {
        echo "Access to the requested value is forbidden.";
    } else {
        echo "Error: " . $e->getMessage();
    }
}
```

***

#### Catch a Specific Exception

You can catch a specific exception type, such as `ErrorException`, for more granular error handling.

```php
<?php
use Luminova\Exceptions\ErrorException;

try {
    $pdo = loginAccount($username, $password);
} catch (ErrorException $e) {
    // Handle specific errors here.
    echo "An error occurred: " . $e->getMessage();
}
```

***

#### Catch an Exception and Log Instead

In cases where you want to log the exception rather than handle it directly, you can do so as follows:

```php
<?php
use Luminova\Exceptions\AppException;
use Luminova\Exceptions\ErrorException;
use Exception;

try {
    $pdo = loginAccount($username, $password);
} catch (ErrorException|Exception $e) {
    if ($e instanceof AppException) {
        $e->log('debug'); // Log AppException details.
    } else {
        // Log PHP exceptions using a logger.
        logger('debug', $e->getMessage());
    }
}
```

***

#### Catch an Exception and Handle Gracefully Based on Environment

You can manage exceptions differently based on your environment, throwing exceptions during development and logging them in production:

```php
<?php
use Luminova\Exceptions\AppException;
use Luminova\Exceptions\ErrorException;
use Exception;

try {
    $pdo = loginAccount($username, $password);
} catch (ErrorException|Exception $e) {
    if ($e instanceof AppException) {
        $e->handle(); // Handle AppException accordingly.
    } else {
        // Handle generic PHP exceptions gracefully.
        logger('error', 'An error occurred: ' . $e->getMessage());
    }
}
```
***

#### Throw an Exception Gracefully Based on Environment

You can throw exceptions in a way that varies depending on the environment:

```php
<?php
use Luminova\Exceptions\ErrorException;

ErrorException::throwException('An error occurred', ErrorException::ROUTING_ERROR);
```

***

#### Get an Exception Message Without File Information

You can retrieve a filtered error message that omits file details:

```php
<?php
use Luminova\Exceptions\ErrorException;

try {
    $pdo = loginAccount($username, $password);
} catch (ErrorException $e) {
    echo $e->getFilteredMessage(); // Get a user-friendly error message.
}
```

***

#### Get Exception Backtrace Information

You can obtain backtrace information from an exception:

```php
<?php
use Luminova\Exceptions\ErrorException;

try {
    $pdo = loginAccount($username, $password);
} catch (ErrorException $e) {
    print_r($e->getBacktrace()); // Print backtrace for debugging.
}
```

***

#### Get the Humanized Exception Error Name

To retrieve a user-friendly name for the exception, use the following method:

```php
<?php
use Luminova\Exceptions\ErrorException;

try {
    $pdo = loginAccount($username, $password);
} catch (ErrorException $e) {
    echo $e->getName(); // Get a human-readable name for the error.
}
```

***

### Debug Tracing

In PHP, some errors may not throw exceptions due to the default behavior of the language. In Luminova, we capture these errors and log a backtrace to help you understand what went wrong. Below are some useful methods you can call to retrieve error information.

### Retrieve Error Backtrace Information

To get the backtrace information of an error, you can use the following method:

```php
<?php
use Luminova\Errors\ErrorHandler;

print_r(ErrorHandler::getBacktrace());
```

***

#### Manually Set Error Backtrace Information

You can manually set the backtrace information when an error is encountered. This allows you to call `ErrorHandler::getBacktrace()` from anywhere in your application:

```php
<?php
use Luminova\Errors\ErrorHandler;

// Set backtrace information when an error occurs.
ErrorHandler::setBacktrace(debug_backtrace());
```

***

#### Get the Error Name Based on Supported Error Codes

To retrieve the human-readable name of an error based on any supported error code, use the following method:

```php
<?php
use Luminova\Errors\ErrorHandler;

echo ErrorHandler::getErrorName(ErrorException::INPUT_VALIDATION_ERROR); // Outputs: INPUT VALIDATION ERROR
```

***

## Methods

### constructor

Constructor for the exception.

```php
public __construct(string $message, string|int $code, \Throwable|null $previous = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The error message for the exception. |
| `$code` | **string&#124;int** | The exception code (default: 0). |
| `$previous` | **\Throwable&#124;null** | The previous exception, if available (default: null). |

**Usages Example**

Initialize and throws an exception:

```php
<?php
use Luminova\Exceptions\RuntimeException;

try{
	throw new RuntimeException('An error occurred');
}catch(RuntimeException $e){
	// handle exception
}
```

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

### getFilteredMessage

Retrieves the filtered exception message without the file path.

```php
public getFilteredMessage(): string
```

**Return Value:**

`string` - Return the filtered exception message.

***

### getName

Gets the name of the exception.

```php
public getName(): string
```

**Return Value:**

`string` - Return the name of the thrown exception.

***

### getBacktrace

Retrieves the debug backtrace from the exception or shared error context.

```php
public getBacktrace(): array
```

**Return Value:**

`array` - Return the debug backtrace or an empty array if not available.

> This method checks for the exception backtrace, if not available it checks for the stored debug backtrace in the shared memory `__ERROR_DEBUG_BACKTRACE__` if still unavailable, it returns an empty array.

***

### __toString

Gets a string representation of the exception.

```php
public __toString(): string
```

**Return Value:**

`string` - Return a formatted error message representing the exception.

***

### log

Logs the exception message to a specified log file.

```php
public log(string $level = 'exception'): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to use (default: 'exception'). |

**Log Levels:**

- `emergency` - Log emergency error that need attention.
- `alert` - Log alert message.
- `critical` - Log critical issue that may cause app not to work properly.
- `error` - Log minor error.
- `warning` - Log a warning message.
- `notice` - Log a notice to attend later.
- `info` - Log an information.
- `debug` - Log for debugging purpose.
- `exception` - Log an exception message.
- `php_errors` - Log any php related error.
- `metrics` - Log performance metrics, specifically for api in production level.

**Usages Example**

This example demonstrates catching an exception and logging an exception message with a custom log level.

```php
<?php
use Luminova\Exceptions\RuntimeException;

try{
	throw new RuntimeException('An error occurred');
}catch(RuntimeException $e){
	$e->log('debug');
}
```

***

### handle

Handles the exception gracefully based on the environment and error code.

```php
public handle(): void
```

**Throws:**

- `self<\T>` - Throws in development environment or if the exception code is fatal, otherwise log the information.

**Usages Example**

This below example shows how you can catch an exception and handle it with `handle` method.

```php
<?php
use Luminova\Exceptions\RuntimeException;

try{
	throw new RuntimeException('An error occurred');
}catch(RuntimeException $e){
	$e->handle();
}
```

***

### throwException

Throw an exception gracefully based on environment.

```php
public static throwException(string $message, string|int $code, \Throwable|null $previous = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The exception message. |
| `$code` | **string&#124;int** | The exception code (default: 0). |
| `$previous` | **\Throwable&#124;null** | The previous exception, if available (default: null). |

**Throws:**

- `static<\T>` - Throws in development environment or if the exception code is fatal, otherwise log the information.

**Usage Example**

The following example demonstrates how to throw an exception in a production environment using the fatal error code `COMPILE_ERROR`. For more error codes, refer to [Exceptions and Error Handling](/running/error-codes.md).

```php
<?php
use Luminova\Exceptions\RuntimeException;

RuntimeException::throwException('An error occurred', RuntimeException::COMPILE_ERROR);
```

> **Note:** The error codes `ERROR`, `PARSE_ERROR`, `CORE_ERROR`, and `COMPILE_ERROR` are considered fatal. These can be used with exception classes to signify critical issues.

***

## Available Exception Classes

Luminova provides a robust set of predefined exception classes that extend the `\Luminova\Exceptions\AppException` class. These exceptions cover a wide range of error scenarios, ensuring that your application can handle various issues gracefully.

The global namespace for these classes is: `\Luminova\Exceptions\`.

| Class                        | Description                                                                 |
|------------------------------|-----------------------------------------------------------------------------|
| `AppException`               | The base exception class for all Luminova-specific exceptions.              |
| `BadMethodCallException`      | Thrown when a callback refers to an undefined method or an invalid context. |
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

The global namespace for these classes is: `\Luminova\Exceptions\Http\`.

| Class                                              | Description                                                                                   |
|----------------------------------------------------|-----------------------------------------------------------------------------------------------|
| `ClientException`        | Represents client-side errors during HTTP requests, such as 4xx status codes.                 |
| `ConnectException`       | Thrown when a connection to a remote server fails.                                            |
| `RequestException`       | Handles errors that occur during the creation or processing of HTTP requests.                 |
| `ServerException`        | Represents server-side errors encountered during HTTP requests, such as 5xx status codes.     |