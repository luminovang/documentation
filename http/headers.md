## Request Header

***

## Overview

The header class functions similarly to the Server class, but it specifically focuses on managing request headers. It provides methods to interact with and manage request headers in a structured way.

***

The `Header` class functions similarly to the `Server` class, but it specifically focuses on managing request headers. It provides methods to interact with and manage request headers in a structured manner.

***

* Class namespace: `\Luminova\Http\Header`

## Properties

### httpMethods

All allowed HTTP request methods, must be in upper case.

```php
public static array&lt;int,string&gt; $httpMethods
```

* This property is **static**.

***

### variables

Header variables.

```php
protected array $variables
```

***

## Methods

### constructor

Initializes the header constructor.

```php
public __construct(?array $variables = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$variables` | **?array** |  |

***

### get

Get header variables.

```php
public get(string|null $name = null, mixed $default = null): mixed|array|string|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | Optional name of the server variable. |
| `$default` | **mixed** | Default value for the server key. |

**Return Value:**

`mixed|array|string|null` - The value of the specified server variable, or all server variables if $name is null.

***

### set

Set server variable.

```php
public set(string $key, string $value): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The server variable key to set. |
| `$value` | **string** | The server variable value. |

***

### remove

Removes a server variable by key

```php
public remove(string $key): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to remove. |

***

### has

Check if request header key exist.

```php
public has(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | Header key to check. |

**Return Value:**

`bool` - Return true if key exists, false otherwise.

***

### count

Get the total number of server variables.

```php
public count(): int
```

**Return Value:**

`int` - Number of server variables

***

### getHeaders

Get all request headers.

```php
public static getHeaders(): array&lt;string,string&gt;
```

**Return Value:**

`array&lt;string,string&gt;` - The request headers.

***

### getContentType

Get the content type based on file extension and charset.

```php
public static getContentType(string $extension = 'html', string $charset = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$extension` | **string** | The file extension. |
| `$charset` | **string** | The character set. |

**Return Value:**

`string` - The content type.

***

### getContentTypes

Get content types by name

```php
public static getContentTypes(string $type): array&lt;int,array&gt;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | Type of content types to retrieve. |

**Return Value:**

`array&lt;int,array&gt;` - Array of content types or null if not found.

***

### Example Usage:

The header object is available in `request` class object as public property `request()->header`

Get value for a specific key.

```php
$value = $header->get('User-Agent');
```
Set a new value for a key.

```php
$header->set('User-Agent', 'New User Agent');
```
Remove a key.

```php
$header->remove('Accept');
```
Search for a value
```php
$key = $header->search('Host');
```
Check if a key exists
```php
if ($header->has('Accept-Encoding')) {
    // Key exists
}
```

Count the number of elements.

```php
$count = $header->count();
```