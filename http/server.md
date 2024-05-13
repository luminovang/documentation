## Server Infomation

***

## Overview

With the Server class, managing server-related data becomes more organized and straightforward, providing convenient methods for retrieval, manipulation, and validation of server variables.

***

The `Server` class serves as a wrapper for the PHP `$_SERVER` superglobal, providing a more structured and manageable way to interact with server-related information. By initializing `Server` with an array representation of `$_SERVER`, it becomes easier to manipulate and access server data.

***

* Class namespace: `\Luminova\Http\Server`

## Properties

### variables

Server variables.

```php
protected array $variables
```

***

## Methods

### constructor

Initializes the server constructor.

```php
public __construct(array $variables): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$variables` | **array** |  |

***

### get

Get server variables.

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

### search

Attempt to find a key in HTTP request headers.

```php
public search(string $key, mixed $default = false): mixed
```

This method searches for a key in the request headers, including normalized and stripped versions.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to search for. |
| `$default` | **mixed** |  |

**Return Value:**

`mixed` - The value of the found header or false if not found.

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

### Example Usage:

The server object is available in `request` class object as public property `request()->server`

Get value for a specific key.

```php
$value = $server->get('SERVER_NAME');
```
Set a new value for a key.

```php
$server->set('QUERY_STRING', 'Default Query String');
```
Remove a key.

```php
$server->remove('SERVER_PORT');
```
Search for a value
```php
$key = $server->search('localhost');
```
Check if a key exists
```php
if ($server->has('SERVER_SIGNATURE')) {
    // Key exists
}
```

Count the number of elements.

```php
$count = $server->count();
```