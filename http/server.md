# Management of Request Server Variables

***

## Overview

With the Server class, managing server-related variables becomes easier and more straightforward, offering convenient methods for retrieving, manipulating, and validating server data.

***

## Introduction

The `Server` class acts as a structured wrapper around PHP's `$_SERVER` superglobal, providing a more organized way to interact with server-related data. By initializing the `Server` class with an array representation of `$_SERVER`, it allows for easier manipulation and access to server variables, enhancing the efficiency and clarity of server-side operations.

---

## Class Definition

* Class namespace: `\Luminova\Http\Server`
* This class implements:  [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface), [\Countable](https://www.php.net/manual/en/class.countable.php)

---

## Example Usage

The `Server` object is accessible via the `request` class as a public property: `$request->server`.

#### Get a Value for a Specific Key:

```php
$value = $server->get('SERVER_NAME');
```

#### Set a New Value for a Key:

```php
$server->set('QUERY_STRING', 'Default Query String');
```

#### Remove a Key:

```php
$server->remove('SERVER_PORT');
```

#### Search for a Value:

```php
$key = $server->search('localhost');
```
***

#### Check if a Key Exists:

```php
if ($server->has('SERVER_SIGNATURE')) {
    // Key exists
}
```

***

#### Count the Number of Elements:

```php
$count = $server->count();
```

---

## Properties

### variables

An array holding server variables.

```php
protected array<string,mixed> $variables = [];
```

---

## Methods

### constructor

Initializes the `Server` class with an array of server variables.

```php
public __construct(array $variables): mixed;
```

**Parameters:**

| Parameter   | Type   | Description |
|-------------|--------|-------------|
| `$variables` | **array** | An Associative array of server variables to initialize with. |

---

### get

Retrieves server variables.

```php
public get(string|null $name = null, mixed $default = null): mixed|array|string|null;
```

**Parameters:**

| Parameter | Type   | Description |
|-----------|--------|-------------|
| `$name`   | **string\|null** | The name of the server variable to retrieve. |
| `$default` | **mixed** | The default value if the variable is not found. |

**Return Value:**

`mixed|array|string|null` - Return the value of the specified server variable, or all variables if `$name` is `null`.

---

### set

Sets a server variable.

```php
public set(string $key, mixed $value): void;
```

**Parameters:**

| Parameter | Type   | Description |
|-----------|--------|-------------|
| `$key`    | **string** | The key of the server variable to set. |
| `$value`  | **mixed** | The value to assign to the server variable. |

---

### remove

Removes a server variable by key.

```php
public remove(string $key): void;
```

**Parameters:**

| Parameter | Type   | Description |
|-----------|--------|-------------|
| `$key`    | **string** | The key of the server variable to remove. |

---

### search

Searches for a value by key in the server variables.

```php
public search(string $key, mixed $default = false): mixed;
```

**Parameters:**

| Parameter | Type   | Description |
|-----------|--------|-------------|
| `$key`    | **string** | The key to search for. |
| `$default` | **mixed** | The default value if the key is not found. |

**Return Value:**

`mixed` - Return the value of the found variable or `false` if not found.

---

### has

Checks if a server variable exists by key.

```php
public has(string $key): bool;
```

**Parameters:**

| Parameter | Type   | Description |
|-----------|--------|-------------|
| `$key`    | **string** | The key to check for existence. |

**Return Value:**

`bool` - Returns `true` if the key exists, `false` otherwise.

---

### count

Counts the total number of server variables.

```php
public count(): int;
```

**Return Value:**

`int` - Return the number of server variables.