# Exceptions and Custom Error Codes

***

## Overview

Explore Comprehensive Documentation on Luminova Exception and Error Handling: Detailed Error Types and Codes for Enhanced Code Quality.

***

## Introduction

This documentation provides a complete reference for error codes with it's usage descriptions. The `ErrorCodes` trait in the Luminova framework defines a set of constants that represent various error codes. These codes encompass a wide range of PHP error types, including:

- **PDO SQLSTATE Codes**
- **MySQL Error Codes**
- **PostgreSQL Error Codes**
- **Custom Error Codes**

The `AppException` class automatically inherits the `ErrorCodes` trait, granting any exception class that extends `AppException` access to these error constants defined within the `ErrorCodes` trait.

***

- **Namespace**: `\Luminova\Config\Enums\ErrorCodes`

***

## 1. PHP Error Types

The following constants represent standard PHP error types. They can be used for error handling and logging purposes within the application.

| Constant                     | Value       | Description                                                 |
|------------------------------|-------------|-------------------------------------------------------------|
| `ERROR`                      | `E_ERROR`   | Fatal run-time errors.                                      |
| `PARSE_ERROR`                | `E_PARSE`   | Compile-time parse errors.                                  |
| `CORE_ERROR`                 | `E_CORE_ERROR` | Fatal errors that occur during PHP's initial startup.     |
| `COMPILE_ERROR`              | `E_COMPILE_ERROR` | Fatal compile-time errors.                                |
| `WARNING`                    | `E_WARNING` | Run-time warnings (non-fatal errors).                      |
| `CORE_WARNING`               | `E_CORE_WARNING` | Warnings that occur during PHP's initial startup.        |
| `COMPILE_WARNING`            | `E_COMPILE_WARNING` | Compile-time warnings.                                   |
| `USER_WARNING`               | `E_USER_WARNING` | User-generated warnings.                                  |
| `NOTICE`                     | `E_NOTICE`  | Run-time notices (non-critical errors).                    |
| `USER_NOTICE`                | `E_USER_NOTICE` | User-generated notices.                                   |
| `STRICT_NOTICE`              | `E_STRICT`  | Suggestions for code improvements.                          |
| `USER_ERROR`                 | `E_USER_ERROR` | User-generated fatal errors.                               |
| `RECOVERABLE_ERROR`          | `E_RECOVERABLE_ERROR` | Errors that can be caught by a `catch` block.          |
| `DEPRECATED`                 | `E_DEPRECATED` | Notices of deprecated features.                             |
| `USER_DEPRECATED`            | `E_USER_DEPRECATED` | User-generated notices of deprecated features.          |

***

## 2. PDO SQLSTATE Codes

These constants represent SQLSTATE error codes for PDO. They are useful for handling database connection issues and SQL errors.

| Constant                              | Value     | Description                                           |
|---------------------------------------|-----------|-------------------------------------------------------|
| `UNABLE_TO_CONNECT`                   | `08001`   | Unable to connect to the database.                   |
| `CONNECTION_DENIED`                   | `08004`   | Connection denied due to invalid credentials.        |
| `INTEGRITY_CONSTRAINT_VIOLATION`      | `23000`   | Integrity constraint violation, such as foreign key issues. |
| `SQL_SYNTAX_ERROR_OR_ACCESS_VIOLATION`| `42000`   | SQL syntax error or access violation.                |

***

## 3. MySQL Error Codes

The following constants represent common MySQL error codes. These can be used for handling database-specific errors in MySQL.

| Constant                              | Value   | Description                                           |
|---------------------------------------|---------|-------------------------------------------------------|
| `ACCESS_DENIED_FOR_USER`              | `1044`  | Access denied for the user.                           |
| `ACCESS_DENIED_INVALID_PASSWORD`       | `1045`  | Access denied due to invalid password.                |
| `UNKNOWN_DATABASE`                     | `1049`  | Unknown database specified in the connection.        |
| `SYNTAX_ERROR_IN_SQL_STATEMENT`       | `1064`  | Syntax error in SQL statement.                        |
| `TABLE_DOES_NOT_EXIST`                 | `1146`  | The specified table does not exist.                   |

***

## 4. PostgreSQL Error Codes

These constants represent PostgreSQL-specific error codes.

| Constant                              | Value     | Description                                           |
|---------------------------------------|-----------|-------------------------------------------------------|
| `INVALID_AUTHORIZATION_SPECIFICATION` | `28000`   | Invalid authorization specification.                  |
| `INVALID_CATALOG_NAME`                | `3D000`   | Invalid catalog name (database name).                |

***

## 5. Custom Luminova Error Codes

These constants represent custom error codes defined specifically for the Luminova framework. They cover various aspects of application errors, including database errors, validation errors, and more.

| Constant                              | Value   | Description                                           |
|---------------------------------------|---------|-------------------------------------------------------|
| `DATABASE_ERROR`                      | `1500`  | General database error.                              |
| `FAILED_ALL_CONNECTION_ATTEMPTS`      | `1503`  | Failed all attempts to connect to the database.     |
| `CONNECTION_LIMIT_EXCEEDED`           | `1509`  | Connection limit exceeded.                           |
| `INVALID_DATABASE_DRIVER`              | `1406`  | Invalid database driver specified.                   |
| `DATABASE_DRIVER_NOT_AVAILABLE`       | `1501`  | Database driver not available.                       |
| `DATABASE_TRANSACTION_READONLY_FAILED`| `1417`  | Failed to execute a read-only transaction.          |
| `DATABASE_TRANSACTION_FAILED`         | `1420`  | Database transaction failed.                         |
| `TRANSACTION_SAVEPOINT_FAILED`        | `1418`  | Failed to create a transaction savepoint.           |
| `FAILED_TO_ROLLBACK_TRANSACTION`      | `1419`  | Failed to rollback the transaction.                  |
| `NO_STATEMENT_TO_EXECUTE`             | `1499`  | No SQL statement to execute.                         |
| `VALUE_FORBIDDEN`                     | `1403`  | Forbidden value encountered.                         |
| `INVALID_ARGUMENTS`                   | `1001`  | Invalid arguments provided to a function.           |
| `INVALID`                              | `1002`  | General invalid error.                              |
| `RUNTIME_ERROR`                       | `5001`  | General runtime error.                              |
| `CLASS_NOT_FOUND`                     | `5011`  | Class not found during execution.                   |
| `STORAGE_ERROR`                       | `5079`  | General storage error.                              |
| `VIEW_NOT_FOUND`                      | `404`   | The requested view could not be found.             |
| `INPUT_VALIDATION_ERROR`              | `4070`  | Input validation error occurred.                    |
| `ROUTING_ERROR`                       | `4161`  | Error in routing the request.                       |
| `NOT_FOUND`                           | `4040`  | Requested resource not found.                        |
| `BAD_METHOD_CALL`                    | `4051`  | Method call failed due to invalid parameters.       |
| `CACHE_ERROR`                        | `5071`  | Error related to caching.                           |
| `FILESYSTEM_ERROR`                   | `6204`  | Filesystem error encountered.                       |
| `COOKIE_ERROR`                       | `4961`  | Error related to cookies.                           |
| `DATETIME_ERROR`                     | `2306`  | Error in date/time operations.                      |
| `CRYPTOGRAPHY_ERROR`                 | `3423`  | Error related to cryptographic operations.          |
| `WRITE_PERMISSION_DENIED`            | `6205`  | Denied write permission.                            |
| `READ_PERMISSION_DENIED`             | `6206`  | Denied read permission.                             |
| `READ_WRITE_PERMISSION_DENIED`       | `6209`  | Denied read/write permission.                       |
| `CREATE_DIR_FAILED`                  | `6207`  | Failed to create a directory.                       |
| `SET_PERMISSION_FAILED`              | `6208`  | Failed to set permissions on a file/directory.     |
| `JSON_ERROR`                         | `4180`  | JSON encoding/decoding error.                       |
| `SECURITY_ISSUE`                     | `4973`  | Security issue detected.                            |
| `MAILER_ERROR`                       | `449`   | Error related to mailer functionality.              |
| `INVALID_CONTROLLER`                 | `1003`  | Invalid controller specified.                        |
| `INVALID_METHOD`                     | `4052`  | Invalid method specified.                           |
| `INVALID_REQUEST_METHOD`             | `4053`  | Invalid request method used.                        |
| `NOT_ALLOWED`                        | `4061`  | Operation not allowed.                              |
| `NOT_SUPPORTED`                      | `4062`  | Operation not supported.                            |
| `LOGIC_ERROR`                        | `4229`  | Logical error in the application.                   |
| `UNDEFINED`                          | `8790`  | Undefined error occurred.                           |
| `HTTP_CLIENT_ERROR`                  | `4974`  | Client error occurred during an HTTP request.      |
| `HTTP_CONNECTION_ERROR`              | `4975`  | Connection error during HTTP request.               |
| `HTTP_REQUEST_ERROR`                 | `4976`  | Error during HTTP request processing.               |
| `HTTP_SERVER_ERROR`                  | `4977`  | Server error occurred while processing HTTP request. |

***

### 6. SQLite Error Codes

These constants represent SQLite-specific error codes for database operations.

| Constant                              | Value   | Description                                           |
|---------------------------------------|---------|-------------------------------------------------------|
| `DATABASE_IS_FULL`                   | `5`     | The database is full.                                 |
| `DATABASE_LOCKED`                    | `6`     | The