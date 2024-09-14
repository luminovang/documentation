# Management of Incoming HTTP Request Headers

***

## Overview

The header class specifically focuses on managing request headers. It provides methods to interact with and manage request headers easily.

***

## Introduction

The `Header` class in Luminova is designed to manage HTTP request headers in a structured and efficient manner, similar to the way the `Server` class handles server-related data. It provides methods to interact with, retrieve, and manipulate request headers, simplifying the process of managing HTTP requests.

***

* Class namespace: `\Luminova\Http\Header`

***

### Example Usage:

The `Header` object is available in the `Request` class object as a public property `$request->header`.

**Get the value for a specific key:**

```php
<?php
$value = $header->get('User-Agent');
```

**Set a new value for a key:**

```php
<?php
$header->set('User-Agent', 'New User Agent');
```

**Remove a key:**

```php
<?php
$header->remove('Accept');
```

**Search for a value:**

```php
<?php
$key = $header->search('Host');
```
**Check if a key exists:**

```php
<?php
if ($header->has('Accept-Encoding')) {
    // Key exists
}
```

**Count the number of elements:**

```php
<?php
$count = $header->count();
```

***

## Properties

### httpMethods

List of all allowed HTTP request methods, which must be in uppercase.

```php
public static string[] $httpMethods = [];
```

***

### variables

Array containing header variables.

```php
protected array<string,mixed> $variables = [];
```

***

## Methods

### constructor

Initializes the `Header` object with optional headers. If no headers are passed, it will use the request headers.

```php
public __construct(?array $variables = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$variables` | **array<string,mixed>|null** | Optional key-value pairs of header variables. |

***

### get

Retrieves the value of a specific header or all headers if no name is provided.

```php
public get(string|null $name = null, mixed $default = null): mixed|array|string|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | Optional name of the server variable. |
| `$default` | **mixed** | Default value for the server key. |

**Return Value:**

`mixed|array|string|null` - Return the value of the specified header, or all headers if `$name` is null.

***

### set

Sets the value for a specific header.

```php
public set(string $key, mixed $value): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The header key to set. |
| `$value` | **string** | The value to set for the header key. |

***

### remove

Removes a specific header by its key.

```php
public remove(string $key): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the header to remove. |

***

### has

Checks if a specific header key exists.

```php
public has(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The header key to check. |

**Return Value:**

`bool` - Return true if the key exists, false otherwise.

***

### count

Gets the total number of header variables.

```php
public count(): int
```

**Return Value:**

`int` - Return the number of header variables.

***

### getHeaders

Extracts all request headers using `apache_request_headers` or the `$_SERVER` variables.

```php
public static getHeaders(): array<string,string>
```

**Return Value:**

`array<string,string>` - Return the request headers.

***

### getContentType

Determines the content type based on the file extension and charset.

```php
public static getContentType(string $extension = 'html', string $charset = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$extension` | **string** | The file extension. |
| `$charset` | **string** | The character set. |

**Return Value:**

`string` - Return the content type and optional charset.

***

### getContentTypes

Retrieves content types by name.

```php
public static getContentTypes(string $type): array<string,array>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The type of content types to retrieve. |

**Return Value:**

`array<string,array>` - Return array, string of content types or null if not found.