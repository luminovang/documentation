# Globally Defined Constants

***

## Overview

Constants defines fixed values for application path, database return &amp; result modes, and other application flags. Constants are immutable values set once and remain unchanged at runtime.

***

## Introduction

Global constants are immutable values that remain the same throughout the entire application lifecycle. Unlike regular variables, constants cannot be changed at runtime, providing reliability and consistency in the application’s behavior.

They are commonly used to define reusable values such as configuration settings, environment flags, directory paths, and other predefined values. Giving meaningful names to constants improves code readability, maintainability, and clarity by clearly conveying the purpose of each value.

---

### Scope

Constants are accessible from anywhere in the application, making them ideal for values shared across multiple modules. In Luminova, constants are typically defined in `/bootstrap/constants.php` and are loaded before any other module, ensuring they are available globally from the start of execution.

---

### Status Codes

- `STATUS_SUCCESS` **(int)**: - Status code indicating the operation was successful.  
  > **Recommended when:** You want to continue the normal application flow after a successful process.

- `STATUS_ERROR` **(int)**: - Status code indicating the operation failed.  
  > **Recommended when:** A critical error occurs that requires stopping the current process or returning an error response.

- `STATUS_SILENCE` **(int)**: - Status code indicating the operation may have failed but was handled gracefully without interrupting the application.  
  > **Recommended when:** You want to suppress the error visibly, respond with a custom error details or continue without showing a failure message.

***

### Application

- `APP_NAME` **(string)**: - Application development state.
- `APP_VERSION` **(string)**: - Home directory path.
- `APP_FILE_VERSION` **(string)**: - Home directory path.
- `APP_FILE_VERSION` **(string)**: - Home directory path.

***

### Environment

- `ENVIRONMENT` **(string)**: - Application environment state type.
- `PRODUCTION` **(bool)**: - Application production state.
- `MAINTENANCE` **(bool)**: - Application on maintenance mode.
- `CLI_ENVIRONMENT` **(string)**: - Application CLI development state.
- `NOVAKIT_ENV` **(string|null)**: - NovaKit CLI executable script.

***

### I/O Streams

- `STDOUT` **(string)**: - Standard output stream.
- `STDIN` **(string)**: - Standard input stream.
- `STDERR` **(string)**: - Standard error stream.

***

### File Paths

- `APP_ROOT` **(string)**: - Application project root directory (`private`).
- `DOCUMENT_ROOT` **(string)**: - Application front controller document root directory (`public`).
- `CONTROLLER_SCRIPT_PATH` **(string)** - Project controller script name directory (e.g, `your-project-dir/public`).

***

### URLs

- `APP_HOSTNAME` **(string)**: - Application hostname (e.g, `example.com`).
- `APP_HOSTNAME_ALIAS` **(string)**: - Application hostname alias (e.g, `www.example.com`) www version.
- `APP_URL` **(string)**: - Application URL (e.g, `http://example.com`).
- `APP_URL_ALIAS` **(string)**: - Application URL alias (e.g, `http://www.example.com`) www version.
- `URL_SCHEME` **(string)**: - URL protocol scheme string `http` or `https`.

***

### Database Fetch Modes

- `FETCH_ASSOC` **(int)**: - Fetch as an associative array.
- `FETCH_NUM` **(int)**: - Fetch as an array integer index.
- `FETCH_BOTH` **(int)**: - Fetch as an array integer index and associative.
- `FETCH_OBJ` **(int)**: - Fetch as an object.
- `FETCH_COLUMN` **(int)**: - Fetch as an array columns integer index.
- `FETCH_NUM_OBJ` **(int)**: - Fetch as an object with string integer property names.
- `FETCH_ALL` **(int)**: - Fetch all as an associative array.
- `FETCH_COLUMN_ASSOC` **(int)**: - Fetch all as an associative array.

***

### Database Results Modes

- `RETURN_NEXT` **(int)**: - Return next or single record.
- `RETURN_2D_NUM` **(int)**: - Return all result as 2D array integers.
- `RETURN_ID` **(int)**: - Return last inserted ID.
- `RETURN_INT` **(int)**: - Return count of records.
- `RETURN_COUNT` **(int)**: - Return number of affected rows.
- `RETURN_COLUMN` **(int)**: - Return all result columns.
- `RETURN_ALL` **(int)**: - Return all result.
- `RETURN_STMT` **(int)**: -  Return prepared statement object.
- `RETURN_RESULT` **(int)**: - Return `MYSQLI` result object.