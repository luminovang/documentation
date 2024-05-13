## Exception Classes

***

## Overview

Luminova&#039;s Exception classes provide a structured way to handle errors and exceptions gracefully in your Luminova applications. 

***

Luminova Exceptions is a set of custom exception classes designed to log or handle errors gracefully in production. These exceptions provide clear and easy methods to help developers identify and troubleshoot issues effectively.

***

* Base Exception namespace: `\Luminova\Exceptions\AppException`
* Parent class: `\Exception`
* This class implements:
[\Luminova\Interface\ExceptionInterface](/interface/classes#ExceptionInterface)
* This class is an **Abstract class**

***

## Methods

### constructor

To initialize your exception class.

```php
public __construct(string $message, int $code, \Throwable $previous = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The exception message. |
| `$code` | **int** | The exception code (default: 0). |
| `$previous` | **\Throwable** | The previous exception if applicable (default: null). |

***

### handle

Handle exception gracefully based on environment and exception error code.

```php
public handle(): void
```

**Throws:**

- `Exception` - Throws an exception if on development or fatal, otherwise log the exception.

**Example**

This below example shows how you can catch an exception and handle it with `handle` method.

```php
<?php
use Luminova\Exceptions\RuntimeException;
try{
	throw new RuntimeException("An error occurred");
}catch(RuntimeException $e){
	$e->handle();
}
```

***

### log

Logs an exception message.

```php
public log(string $level = 'exception'): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | Log level. |

**Example**

This example demonstrates catching an exception and logging it with a custom log level.

```php
<?php
use Luminova\Exceptions\RuntimeException;
try{
	throw new RuntimeException("An error occurred");
}catch(RuntimeException $e){
	$e->log('debug');
}
```

***

### throwException

Create and handle a exception gracefully.

```php
public static throwException(string $message, int $code, \Throwable $previous = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The exception message. |
| `$code` | **int** | The exception code (default: 500). |
| `$previous` | **\Throwable** | The previous exception if applicable (default: null). |

**Throws:**

- `static` -Throws an exception if on development or fatal, otherwise log the exception.

**Example**

This below example will throws the exception both in production, because we used `PHP` fatal error code `E_ERROR`.

```php
<?php
use Luminova\Exceptions\RuntimeException;
RuntimeException::throwException("An error occurred", E_ERROR);
```

> These are considered as PHP fatal error codes: `E_ERROR`, `E_PARSE`, `E_CORE_ERROR`, `E_COMPILE_ERROR`.
> You can use them in conjunction with exception classes to indicate fatal error codes.

***

### Usages

Catching exceptions

```php
<?php 
use \Luminova\Exceptions\AppException;
use \Luminova\Exceptions\RuntimeException;
use \Exception;

try {
    // Code that may throw exceptions
} catch (RuntimeException $e) {
    // Handle Luminova-specific exceptions
} catch (AppException $e) {
    // Handle any other Luminova exceptions
} catch (Exception $e) {
    // Handle other exceptions
}
```

***

### Throwing Exceptions

```php
<?php
use \Luminova\Exceptions\ErrorException;
throw new ErrorException("Invalid configuration file.");
```
Throw exception gracefully.

```php
<?php
use \Luminova\Exceptions\ErrorException;
ErrorException::throwException("Invalid configuration file.");
```

### Custom Exception Class

To define your own exception handler class, it must implement `\Luminova\Interface\ExceptionInterface`.

```php
<?php
use \Luminova\Interface\ExceptionInterface;
use \Exception;
use \Throwable;

class CustomException extends Exception implements ExceptionInterface
{
	public function __construct(string $message, int $code = 0, Throwable $previous = null)
  	{
		parent::__construct($message, $code, $previous);
    }
} 
```

Optionally you can just extend `\Luminova\Exceptions\AppException` class.

```php
<?php
use \Luminova\Exceptions\AppException;
use \Exception;
use \Throwable;

class CustomException extends AppException
{
	public function __construct(string $message, int $code = 0, Throwable $previous = null)
	{
		parent::__construct($message, $code, $previous);
	}
} 
```

***

## Exception Classes

* Parent class: `\Luminova\Exceptions\AppException`
* Class namespace: `\Luminova\Exceptions\`

### AppException

`\Luminova\Exceptions\AppException`

Base exception class for Luminova framework.

---

### BadMethodCallException

`\Luminova\Exceptions\BadMethodCallException`

Exception thrown if a callback refers to an undefined method or if some arguments are missing.

---

### ClassException

`\Luminova\Exceptions\ClassException`

Represents exceptions related to class issues.

---

### CookieException

`\Luminova\Exceptions\CookieException`

Represents exceptions related to cookie class.

---

### DatabaseException

`\Luminova\Exceptions\DatabaseException`

Represents exceptions related to database operations.

---

### DatabaseLimitException

`\Luminova\Exceptions\DatabaseLimitException`

Represents exceptions related to database limits.

---

### DateTimeException

`\Luminova\Exceptions\DateTimeException`

Represents exceptions related to date and time operations.

---

### EncryptionException

`\Luminova\Exceptions\EncryptionException`

Represents exceptions related to encryption.

---

### ErrorException

`\Luminova\Exceptions\ErrorException`

Represents errors thrown by PHP.

---

### FileException

`\Luminova\Exceptions\FileException`

Represents exceptions related to file operations.

---

### InvalidArgumentException

`\Luminova\Exceptions\InvalidArgumentException`

Exception thrown if an argument is not of the expected type.

---

### InvalidException

`\Luminova\Exceptions\InvalidException`

Represents exceptions related to invalid data.

---

### InvalidObjectException

`\Luminova\Exceptions\InvalidObjectException`

Represents exceptions related to invalid objects.

---

### MailerException

`\Luminova\Exceptions\MailerException`

Represents exceptions related to mailer.

---

### NotFoundException

`\Luminova\Exceptions\NotFoundException`

Represents exceptions for not found resources.

---

### RuntimeException

`\Luminova\Exceptions\RuntimeException`

Represents exceptions that can be thrown during normal execution.

---

### SecurityException

`\Luminova\Exceptions\SecurityException`

Represents exceptions related to security issues.

---

### StorageException

`\Luminova\Exceptions\StorageException`

Represents exceptions related to storage operations.

---

### ValidationException

`\Luminova\Exceptions\ValidationException`

Represents exceptions related to validation failures.

---

### ViewNotFoundException

`\Luminova\Exceptions\ViewNotFoundException`

Represents exceptions for view not found.

---

### JsonException

`\Luminova\Exceptions\JsonException`

Represents exceptions for json encode and decode errors.

---

### ClientException

`\Luminova\Exceptions\Http\ClientException`

Represents exceptions for for HTTP client request errors.

---

### ConnectException

`\Luminova\Exceptions\Http\ConnectException`

Represents exceptions for for HTTP request connection errors.

---

### RequestException

`\Luminova\Exceptions\Http\RequestException`

Represents exceptions for for HTTP request errors.

---

### ServerException

`\Luminova\Exceptions\Http\ServerException`

Represents exceptions for for HTTP request response errors.
