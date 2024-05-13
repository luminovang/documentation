# Session Manager

***

## Overview

The Session class provides a set of methods to simplify session management in your Luminova application. It offers convenient methods for storing, retrieving, and manipulating session data.

***

## Introduction

The Luminova's Session Manager is a useful component that provides a set of methods to simplify managing user sessions in your web applications. Its primary function is to enhance the storage and retrieval of session data, allowing applications to maintain stateful interactions with users across multiple requests. With support for two distinct storage drivers `SessionManager` and `CookieManager`, you have the flexibility to choose the appropriate session storage mechanism based on your need. 

This is primarily designed for server-side session storage for enhanced security and scalability.

***

* Class namespace: `\Luminova\Sessions\Session`

***

## Methods

### constructor

Initializes session class with your preferred manager class, such as ` Luminova\Sessions\SessionManager` or ` Luminova\Sessions\CookieManager`.

If the argument is passed `NULL` the `SessionManager` will be used as default.

```php
new Session(\Luminova\Interface\SessionManagerInterface|null $manager = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$manager` | **\Luminova\Interface\SessionManagerInterface&#124;null** | The session manager. |

***

### getInstance

Get a shared instance of the Session class.

```php
public static getInstance(?\Luminova\Interface\SessionManagerInterface $manager = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$manager` | **\Luminova\Interface\SessionManagerInterface&#124;null** | The session manager class. |

**Return Value:**

`static` - Session class instance.

***

### toArray

Retrieve data as an array from the current session storage.

```php
public toArray(string $index = ''): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **string** | Optional key to retrieve. |

**Return Value:**

`array` - Return an array representation of data

***

### toObject

Retrieves data as an object from the current session storage.

```php
public toObject(string $index = ''): object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **string** | Optional key to retrieve. |

**Return Value:**

`object` - Return an object representation of data.

***

### toExport

Retrieves all stored session data as an array or object.

```php
public toExport(string $type = 'array'): array|object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | Return type of object or array (default is 'array'). |

**Return Value:**

`array|object` - All stored session data.

***

### setManager

Sets the session manager.

```php
public setManager(\Luminova\Interface\SessionManagerInterface $manager): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$manager` | **\Luminova\Interface\SessionManagerInterface** | The session manager to set. |

***

### getManager

Retrieves the session storage manager instance (`CookieManager` or `SessionManager`).

```php
public getManager(): \Luminova\Interface\SessionManagerInterface|null
```

**Return Value:**

`SessionManagerInterface` - The storage manager instance otherwise null if not set.

***

### setStorage

Sets the storage name to store and retrieve items from.
Each time you call method for `CRUD` operations it will use your storage, if you want to to change storage you can set new storage name before calling methods. 

```php
public setStorage(string $storage): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | The storage key to set. |

**Return Value:**

`Session` - Return session class instance.

***

### getStorage

Retrieves the current session storage name.

```php
public getStorage(): string
```

**Return Value:**

`string` - Return the current storage name.

***

### get

Retrieves a value from the session storage.

```php
public get(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to retrieve. |
| `$default` | **mixed** | Default value if the key is not found. |

**Return Value:**

`mixed` - Return the retrieved data.

***

### getFrom

Retrieves an item from a specified session storage instance.

```php
public getFrom(string $index, string $storage): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **string** | The key to retrieve. |
| `$storage` | **string** | The storage key name. |

**Return Value:**

`mixed` - Return the retrieved data.

***

### setTo

Sets an item to a specified session storage instance.

```php
public setTo(string $index, mixed $data, string $storage): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **string** | The key to set. |
| `$data` | **mixed** | The data to set. |
| `$storage` | **string** | The storage key name. |

**Return Value:**

`self` - Return session class instance.

***

### online

Checks if user has successfully logged in online.

```php
public online(string $storage = ''): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | optional storage instance key |

**Return Value:**

`bool` - Returns true if the session user is online, false otherwise.

> Optionally, specify a storage name to check; otherwise, it checks the current storage.

***

### ssid

Retrieves the user's login session ID.

```php
public ssid(): string|null
```

**Return Value:**

`string|null` - Returns the session ID or null if not logged in.

> A unique session ID is automatically generated once synchronize() is called.

***

### ssdate

Retrieves the user's login session datetime in ISO 8601 format.

```php
public ssdate(): string|null
```

**Return Value:**

`string|null` - Returns the session login date-time or null if not logged in.

> The session datetime is automatically generated once synchronize() is called.

***

### set

Store data in the session storage.

```php
public set(string $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to set. |
| `$value` | **mixed** | The value to set. |

**Return Value:**

`Session` - Return session class instance.

***

### add

Adds an item to the session storage without overwriting existing values.

```php
public add(string $key, mixed $value): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to set. |
| `$value` | **mixed** | The value to set. |

**Return Value:**

`bool` - Return true if item was added else false.

***

### remove

Remove a key from the session storage by passing the key.

```php
public remove(string $key): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** |  |

**Return Value:**

`Session` - Return session class instance.

***

### clear

Clear all data from session storage by passing the storage key.

```php
public clear(string $storage = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | Optionally pass storage name to clear. |

**Return Value:**

`Session` - Return session class instance.

***

### has

Check if item key exists in session storage.

```php
public has(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | Key to check |

**Return Value:**

`boo` - Return true if key exists in session storage else false.

***

### start

To start a session, simply call the `start()` method. It initializes the session if it's not already started.

```php
public start(): void
```

> This method replaces the default PHP `session_start()`, but with additional configuration.

***

### synchronize

Starts a user's online login session, optionally specifying an IP address.

```php
public synchronize(string $ip = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string** | The IP address. |

**Return Value:**

`Session` - Return session class instance.

> This method should be called to indicate that the user has successfully logged in.

***

### ipChanged

Checks if the user's IP address has changed since the last login session.

```php
public ipChanged(string $storage = ''): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | Optional storage location |

**Return Value:**

`bool` - Returns false if the user's IP address matches the session login IP, otherwise returns true.

***

### Examples

Initializes the session storage driver of your choice.

```php
$session = new Session(new SessionManager());
```

Cookie manager, this manager will store your user information in cookie storage which is client-side storage.

```php
$session = new Session(new CookieManager());
```

> By default access to `JavaScript` is not allowed on and cookie should only be transmitted over secure `HTTPS`  connection.