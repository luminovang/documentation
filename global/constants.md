# Globally Defined Constants

***

## Overview

Constants defines fixed values for application path, database return &amp; result modes, and other application flags. Constants are immutable values set once and remain unchanged at runtime.

***

## Introduction

Global constants are fixed values defined once and used throughout the application. They provide consistent, unchangeable references that improve reliability and make your code easier to understand and maintain.

## What Are Constants?

* **Immutable**: Once defined, they cannot be changed during execution.
* **Global**: Accessible from anywhere in your application.
* **Purposeful**: Ideal for storing configuration flags, environment states, directory paths, status codes, or commonly reused values.

Using well-named constants reduces magic numbers and strings, making code more readable and self-explanatory.

---

### Scope and Usage

In **Luminova**, constants are typically declared in:

```php
// /bootstrap/constants.php
```

This file is loaded early in the application lifecycle, ensuring your constants are available globally — before controllers, models, or routes are executed.

---

### Custom Constants

If you want to define your own constants:

1. Add them to:

```php
// /app/Utils/Global.php
```

2. Enable procedural loading by setting the following in your `.env` file:

```ini
feature.app.dev.functions = true
```

This allows Luminova to load procedural functions and constants during bootstrapping, so your custom constants are globally accessible.

**Example:**

```php
// /app/Utils/Global.php

defined('IS_ACTIVE') || define('IS_ACTIVE', false);
defined('UPLOAD_PATH') || define('UPLOAD_PATH', __DIR__ . '/../public/uploads/');
```

> These values are now globally accessible anywhere in your app.

---

## Default Constants

These are predefined constants provided by Luminova. They are automatically derived from your application's configuration and environment settings. These constants give global access to important values such as file paths, URLs, environment modes, and application metadata, making it easier to reference them across your project without repeating logic.

---

### Status Codes

| Constant         | Type    | Description                          | Recommended When              |
| ---------------- | ------- | ------------------------------------ | ----------------------------- |
| `STATUS_SUCCESS` | **int** | Indicates the operation was successful. | When you want to continue the normal application flow after a successful process. |
| `STATUS_ERROR`   | **int** | Indicates the operation failed. | When a critical error occurs and the current process should stop or return an error response. |
| `STATUS_SILENCE` | **int** | Indicates the operation may have failed but was handled gracefully. | When you want to suppress the error, return custom error details, or continue without showing a failure message. |

---

### I/O Streams

| Constant | Type       | Description                                                                             |
| -------- | ---------- | --------------------------------------------------------------------------------------- |
| `STDOUT` | **string** | Standard output stream. Used for printing output (e.g., with `fwrite`).                 |
| `STDIN`  | **string** | Standard input stream. Used to read input from CLI (e.g., with `fgets`).                |
| `STDERR` | **string** | Standard error stream. Used for writing error messages separately from standard output. |

---

### Application

| Constant           | Type       | Description                                                        |
| ------------------ | ---------- | ------------------------------------------------------------------ |
| `APP_NAME`         | **string** | Application name or identifier.                                    |
| `APP_VERSION`      | **string** | Application current version (e.g., `1.0.0`).                       |
| `APP_FILE_VERSION` | **string** | Static file version used for cache busting (e.g., `v=1689123091`). |

---

### Environment

| Constant          | Type       | Description                  |                                      |
| ----------------- | ---------- | ---------------------------- | ------------------------------------ |
| `ENVIRONMENT`     | **string** | Current application environment type (`development`, `production`, `testing`, etc.). |
| `PRODUCTION`      | **bool**   | Indicates if the application is running in production mode.  |
| `MAINTENANCE`     | **bool**   | Indicates if the application is in maintenance mode. |
| `CLI_ENVIRONMENT` | **string** | CLI-specific environment state for command-line operations. |
| `NOVAKIT_ENV`     | **string\|null** | Path or name of the NovaKit CLI script, or `null` if not set. |

---

### File Paths

| Constant                 | Type       | Description                                                    |
| ------------------------ | ---------- | -------------------------------------------------------------- |
| `APP_ROOT`               | **string** | Project root directory (`private` folder containing core application code). |
| `DOCUMENT_ROOT`          | **string** | Public document root (`public` folder accessible via web server). |
| `CONTROLLER_SCRIPT_PATH` | **string** | Full path to the front controller script (e.g., `public/index.php`). Useful for routing. |

---

### URLs

| Constant             | Type       | Description                                                                |
| -------------------- | ---------- | -------------------------------------------------------------------------- |
| `APP_HOSTNAME`       | **string** | Application hostname (e.g., `example.com`).                                |
| `APP_HOSTNAME_ALIAS` | **string** | Alternate hostname, typically the `www` version (e.g., `www.example.com`). |
| `APP_URL`            | **string** | Full application base URL (e.g., `https://example.com`).                   |
| `APP_URL_ALIAS`      | **string** | Full alternate URL with alias domain (e.g., `https://www.example.com`).    |
| `URL_SCHEME`         | **string** | URL scheme (`http` or `https`) used by the application.                    |

---

### Database Fetch Modes

The `FETCH_*` constants define how individual rows are fetched from a query result.
They are consistently supported across both **PDO** and **MySQLi** drivers in methods that allow specifying a fetch mode.

| Constant         | Description                                          |
| ---------------- | ---------------------------------------------------- |
| `FETCH_ASSOC`    | Fetch row as an **associative array**, using column names as keys. |
| `FETCH_NUM`      | Fetch row as a **numerically indexed array**, using column positions. |
| `FETCH_BOTH`     | Fetch row as **both associative and numeric array** (default for many drivers). |
| `FETCH_OBJ`      | Fetch row as a **standard object**, where columns become object properties. |
| `FETCH_COLUMN`   | Fetch a **single column value** from each row (indexed by row number). |
| `FETCH_NUM_OBJ`  | Fetch row as an **object**, but column names are converted to string-based numeric property names (e.g., `$row->{'0'}`). |
| `FETCH_KEY_PAIR` | Fetch a **2-column result** as a key-value array. The first column becomes the key, the second the value. |
| `FETCH_CLASS`    | Fetch row as an **instance of a user-defined class**, mapping columns to properties. |

> **Tip:**
>
> * Use `FETCH_ASSOC` for clean array access like `$row['email']`.
> * Use `FETCH_OBJ` or `FETCH_CLASS` when working with object-oriented access.
> * `FETCH_KEY_PAIR` is ideal for dropdown lists or ID-to-name mappings.

---

### Database Result Return Modes

The following `RETURN_*` constants define how query results are returned.
They are supported consistently across both **PDO** and **MySQLi** drivers and are mainly used with the `fetch()` and `getResult()` methods.

| Constant        | Description                                              |
| --------------- | -------------------------------------------------------- |
| `RETURN_ALL`    | Returns **all rows** as an array or object (supports both `fetch()` and `getResult()`). |
| `RETURN_NEXT`   | Returns the **next row** or a **single record** as an array or object (supports both). |
| `RETURN_STREAM` | Enables **row-by-row iteration** (streaming mode). Use inside a `while` loop (supports both). |
| `RETURN_2D_NUM` | Returns all rows as a **2D array of numeric indexes** (supports `getResult()` only). |
| `RETURN_ID`     | Returns the **last inserted ID** (supports `getResult()` only). |
| `RETURN_INT`    | Returns the **record count** of the current result set (supports `getResult()` only). |
| `RETURN_COUNT`  | Returns the **number of affected rows** (e.g., after `UPDATE`, `DELETE`) (supports `getResult()` only). |
| `RETURN_COLUMN` | Returns **all values of a single column** (supports `getResult()` only). |
| `RETURN_STMT`   | Returns the **prepared statement object** (useful for manual execution or advanced operations) (supports `getResult()` only). |
| `RETURN_RESULT` | Returns the **raw result object** (`mysqli_result` or `PDOStatement`) (supports `getResult()` only). |

> **Tip:**
> Use `RETURN_ALL` for full result sets, `RETURN_NEXT` for single records, and `RETURN_STREAM` for efficient memory usage in large datasets.

---

### Database Param Types

The following `PARAM_*` constants are shared between **PDO** and **MySQLi** and standardize the way values are bound to prepared statements:

| Constant      | Type | Description                                       |
| ------------- | ---- | ------------------------------------------------- |
| `PARAM_NULL`  | int  | Null value type (e.g., for SQL `NULL` values).    |
| `PARAM_INT`   | int  | Integer value type (e.g., for IDs, counters).     |
| `PARAM_STR`   | int  | String value type (e.g., for text, UUIDs, dates). |
| `PARAM_LOB`   | int  | Large object type (e.g., BLOBs or binary data).   |
| `PARAM_BOOL`  | int  | Boolean value type (`true` or `false`).           |
| `PARAM_FLOAT` | int  | Floating-point number (e.g., decimals).           |

**Type Mapping**

This table shows how each `PARAM_*` constant maps to native types in **PDO** and **MySQLi** drivers:

| Constant      | Meaning                       | PDO Equivalent    | MySQLi Equivalent |
| ------------- | ----------------------------- | ----------------- | ----------------- |
| `PARAM_NULL`  | Null value                    | `PDO::PARAM_NULL` | `'s'`             |
| `PARAM_INT`   | Integer                       | `PDO::PARAM_INT`  | `'i'`             |
| `PARAM_STR`   | String                        | `PDO::PARAM_STR`  | `'s'`             |
| `PARAM_LOB`   | Large object/BLOB             | `PDO::PARAM_LOB`  | `'s'`             |
| `PARAM_BOOL`  | Boolean                       | `PDO::PARAM_BOOL` | `'i'`             |
| `PARAM_FLOAT` | Float (cast as string in PDO) | `PDO::PARAM_STR`  | `'d'`             |

> **Note:** PDO does not natively support `float` types, so `PARAM_FLOAT` is internally cast to `string` (`PDO::PARAM_STR`).