# Exceptions and Error Codes

***

## Overview

Explore Comprehensive Documentation on Luminova Exception and Error Handling: Detailed Error Types and Codes for Enhanced Code Quality.

***

## Introduction

This guide explains Luminova’s exception codes, their severity levels, and how to use them effectively.
All error codes are now defined as constants in the `Luminova\Exceptions\ErrorCode` class. These codes represent different categories of errors, including:

* **PDO SQLSTATE errors**
* **MySQL errors**
* **PostgreSQL errors**
* **Custom application errors**

---

### Using Error Codes

You can pass an error code when throwing exceptions.

```php
use Luminova\Exceptions\ErrorCode;
use Luminova\Exceptions\RuntimeException;

throw new RuntimeException('Error message', ErrorCode::BAD_METHOD_CALL);
```

---

### getName

**Get a Human-Friendly Error Name**

Translate internal error codes into descriptive names that are easier to understand.

```php
use Luminova\Exceptions\ErrorCode;

echo ErrorCode::getName(ErrorCode::INPUT_VALIDATION_ERROR);

// Output: Validation Error
```

You can also ask for a **longer description**:

```php
use Luminova\Exceptions\ErrorCode;

echo ErrorCode::getName(ErrorCode::INPUT_VALIDATION_ERROR, true);
// Output: Input validation failed
```

This is especially helpful when displaying error messages to developers or when logging meaningful details.

---

### has

Check if a given error code is defined in the `Code` class.

```php
use Luminova\Exceptions\ErrorCode;

ErrorCode::has(ErrorCode::ERROR);
```

---

## Constants

All error codes are centralized in the `Code` class.
Reference them directly whenever you need to identify or classify an error:

```php
Luminova\Exceptions\ErrorErrorCode::ERROR
```

***

### PHP Error Codes

These constants map directly to standard PHP error types. You can use them for handling, logging, or categorizing errors in your application.

| Constant                     | Value                 | Description                                             |
|------------------------------|-----------------------|---------------------------------------------------------|
| `ERROR`                      | `E_ERROR`             | Fatal run-time errors.                                  |
| `PARSE_ERROR`                | `E_PARSE`             | Compile-time parse errors.                              |
| `CORE_ERROR`                 | `E_CORE_ERROR`        | Fatal errors that occur during PHP's initial startup.   |
| `COMPILE_ERROR`              | `E_COMPILE_ERROR`     | Fatal compile-time errors.                              |
| `WARNING`                    | `E_WARNING`           | Run-time warnings (non-fatal errors).                   |
| `CORE_WARNING`               | `E_CORE_WARNING`      | Warnings that occur during PHP's initial startup.       |
| `COMPILE_WARNING`            | `E_COMPILE_WARNING`   | Compile-time warnings.                                  |
| `USER_WARNING`               | `E_USER_WARNING`      | User-generated warnings.                                |
| `NOTICE`                     | `E_NOTICE`            | Run-time notices (non-critical errors).                 |
| `USER_NOTICE`                | `E_USER_NOTICE`       | User-generated notices.                                 |
| `STRICT_NOTICE`              | `E_STRICT`            | Suggestions for code improvements.                      |
| `USER_ERROR`                 | `E_USER_ERROR`        | User-generated fatal errors.                            |
| `RECOVERABLE_ERROR`          | `E_RECOVERABLE_ERROR` | Errors that can be caught by a `catch` block.           |
| `DEPRECATED`                 | `E_DEPRECATED`        | Notices of deprecated features.                         |
| `USER_DEPRECATED`            | `E_USER_DEPRECATED`   | User-generated notices of deprecated features.          |

***

## Database Error Codes

### PDO SQLSTATE Codes

These constants map to **SQLSTATE** error codes used by PDO. They help you detect and handle database connection problems or SQL execution errors more precisely.

| Constant                              | Value     | Description                                                 |
|---------------------------------------|-----------|-------------------------------------------------------------|
| `UNABLE_TO_CONNECT`                   | `08001`   | Unable to connect to the database.                          |
| `CONNECTION_DENIED`                   | `08004`   | Connection denied due to invalid credentials.               |
| `INTEGRITY_CONSTRAINT_VIOLATION`      | `23000`   | Integrity constraint violation, such as foreign key issues. |
| `SQL_SYNTAX_ERROR_OR_ACCESS_VIOLATION`| `42000`   | SQL syntax error or access violation.                       |

***

### MySQL Error Codes

These constants correspond to common MySQL error codes. They allow you to handle database issues specific to MySQL, such as duplicate entries, missing tables, or connection problems.

| Constant                              | Value   | Description                                           |
|---------------------------------------|---------|-------------------------------------------------------|
| `ACCESS_DENIED_FOR_USER`              | `1044`  | Access denied for the user.                           |
| `ACCESS_DENIED_INVALID_PASSWORD`      | `1045`  | Access denied due to invalid password.                |
| `UNKNOWN_DATABASE`                    | `1049`  | Unknown database specified in the connection.         |
| `SYNTAX_ERROR_IN_SQL_STATEMENT`       | `1064`  | Syntax error in SQL statement.                        |
| `TABLE_DOES_NOT_EXIST`                | `1146`  | The specified table does not exist.                   |

***

### PostgreSQL Error Codes

These constants represent PostgreSQL-specific error codes. They are useful when handling authentication errors, missing databases, or other PostgreSQL issues.

| Constant                              | Value   | Description                                 |
| ------------------------------------- | ------- | ------------------------------------------- |
| `INVALID_AUTHORIZATION_SPECIFICATION` | `28000` | The authorization specification is invalid. |
| `INVALID_CATALOG_NAME`                | `3D000` | The specified database name is invalid.     |

---

### SQLite Error Codes

These constants represent SQLite-specific error codes. They are useful when handling storage, locking, or database file issues.

| Constant           | Value | Description                                |
| ------------------ | ----- | ------------------------------------------ |
| `DATABASE_IS_FULL` | `5`   | The database has run out of storage space. |
| `DATABASE_LOCKED`  | `6`   | The database file is locked.               |

***

### Custom Luminova Error Codes

These constants are unique to the Luminova framework. They provide a standardized way to represent application-specific errors that go beyond PHP or database engine codes. You can use them to handle issues such as database failures, validation problems, or other framework-level exceptions in a consistent way.

| Constant                              | Value   | Description                                            |
|---------------------------------------|---------|--------------------------------------------------------|
| `DATABASE_ERROR`                      | `1500`  | General database error.                                |
| `FAILED_ALL_CONNECTION_ATTEMPTS`      | `1503`  | Failed all attempts to connect to the database.        |
| `CONNECTION_LIMIT_EXCEEDED`           | `1509`  | Connection limit exceeded.                             |
| `INVALID_DATABASE_DRIVER`             | `1406`  | Invalid database driver specified.                     |
| `DATABASE_DRIVER_NOT_AVAILABLE`       | `1501`  | Database driver not available.                         |
| `DATABASE_TRANSACTION_READONLY_FAILED`| `1417`  | Failed to execute a read-only transaction.             |
| `DATABASE_TRANSACTION_FAILED`         | `1420`  | Database transaction failed.                           |
| `TRANSACTION_SAVEPOINT_FAILED`        | `1418`  | Failed to create a transaction savepoint.              |
| `FAILED_TO_ROLLBACK_TRANSACTION`      | `1419`  | Failed to rollback the transaction.                    |
| `NO_STATEMENT_TO_EXECUTE`             | `1499`  | No SQL statement to execute.                           |
| `VALUE_FORBIDDEN`                     | `1403`  | Forbidden value encountered.                           |
| `INVALID_ARGUMENTS`                   | `1001`  | Invalid arguments provided to a function.              |
| `INVALID`                             | `1002`  | General invalid error.                                 |
| `RUNTIME_ERROR`                       | `5001`  | General runtime error.                                 |
| `CLASS_NOT_FOUND`                     | `5011`  | Class not found during execution.                      |
| `STORAGE_ERROR`                       | `5079`  | General storage error.                                 |
| `VIEW_NOT_FOUND`                      | `404`   | The requested view could not be found.                 |
| `INPUT_VALIDATION_ERROR`              | `4070`  | Input validation error occurred.                       |
| `ROUTING_ERROR`                       | `4161`  | Error in routing the request.                          |
| `NOT_FOUND`                           | `4040`  | Requested resource not found.                          |
| `BAD_METHOD_CALL`                     | `4051`  | Method call failed due to invalid parameters.          |
| `CACHE_ERROR`                         | `5071`  | Error related to caching.                              |
| `FILESYSTEM_ERROR`                    | `6204`  | Filesystem error encountered.                          |
| `COOKIE_ERROR`                        | `4961`  | Error related to cookies.                              |
| `DATETIME_ERROR`                      | `2306`  | Error in date/time operations.                         |
| `CRYPTOGRAPHY_ERROR`                  | `3423`  | Error related to cryptographic operations.             |
| `WRITE_PERMISSION_DENIED`             | `6205`  | Denied write permission.                               |
| `READ_PERMISSION_DENIED`              | `6206`  | Denied read permission.                                |
| `READ_WRITE_PERMISSION_DENIED`        | `6209`  | Denied read/write permission.                          |
| `CREATE_DIR_FAILED`                   | `6207`  | Failed to create a directory.                          |
| `SET_PERMISSION_FAILED`               | `6208`  | Failed to set permissions on a file/directory.         |
| `JSON_ERROR`                          | `4180`  | JSON encoding/decoding error.                          |
| `SECURITY_ISSUE`                      | `4973`  | Security issue detected.                               |
| `MAILER_ERROR`                        | `449`   | Error related to mailer functionality.                 |
| `INVALID_CONTROLLER`                  | `1003`  | Invalid controller specified.                          |
| `INVALID_METHOD`                      | `4052`  | Invalid method specified.                              |
| `INVALID_REQUEST_METHOD`              | `4053`  | Invalid request method used.                           |
| `NOT_ALLOWED`                         | `4061`  | Operation not allowed.                                 |
| `NOT_SUPPORTED`                       | `4062`  | Operation not supported.                               |
| `LOGIC_ERROR`                         | `4229`  | Logical error in the application.                      |
| `UNDEFINED`                           | `8790`  | Undefined error occurred.                              |
| `HTTP_CLIENT_ERROR`                   | `4974`  | Client error occurred during an HTTP request.          |
| `HTTP_CONNECTION_ERROR`               | `4975`  | Connection error during HTTP request.                  |
| `HTTP_REQUEST_ERROR`                  | `4976`  | Error during HTTP request processing.                  |
| `HTTP_SERVER_ERROR`                   | `4977`  | Server error occurred while processing HTTP request.   |
| `HTTP_RESPONSE_ERROR`                 | `4978`  | HTTP response error occurred while processing request. |