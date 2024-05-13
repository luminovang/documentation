# File Manager

***

## Overview

File manager class is a simple PHP class shipped with Luminova Framework to handle file uploads as an alternative to the Storage class.

***

## Introduction

***

# FileSystem

* Class namespace: `\Luminova\Storages\FileManager`

***

### Helper Function

Using helper functions to access class object and properties.

```php
<?php
echo path('system');
echo factory('files', true)->system;
```
Outputs
```bash
Windows: /system/
Unix:  \\system\\
```

***

## Properties

***
### system

Path to the system framework codes.

```php
protected string $system
```

***

### plugins

Path to the system third-party plugins.

```php
protected string $plugins
```

***

### library

Path to the libraries and third-party modules.

```php
protected string $library
```

***

### services

Path to the serialized shared services.

```php
protected string $services
```

***

### controllers

Path to the application controllers.

```php
protected string $controllers
```

***

### writeable

Path to the writeable files.

```php
protected string $writeable
```

***

### logs

Path to the error logs.

```php
protected string $logs
```

***

### caches

Path to the application caches.

```php
protected string $caches
```

***

### public

Path to the public controller document root.

```php
protected string $public
```

***

### assets

Path to the public assets directory.

```php
protected string $assets
```

***

### views

Path to the template views files.

```php
protected string $views
```

***

### routes

Path to the application routes.

```php
protected string $routes
```

***

### languages

Path to the languages modules.

```php
protected string $languages
```

***

## Methods

### permission

Check if file has read, write or read+write permission is granted.

```php
public static permission(string $permission = 'rw', string|null $file = null, bool $quiet = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$permission` | **string** | File access permission (e.g: `rw`, `r` or `w`). |
| `$file` | **string&#124;null** | File name or file path to check permissions (default: writeable dir). |
| `$quiet` | **bool** | Indicate whether to throws an exception if permission is not granted. |

**Return Value:**

`bool` - Returns true if permission is granted otherwise false.

**Throws:**

- [\Luminova\Exceptions\FileException](/exceptions/classes#FileException) - If permission is not granted and quiet is not passed true.

***

### setPermission

Set permissions for a file or directory.

```php
public static setPermission(string $location, int $permission): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$location` | **string** | The path to the file or directory. |
| `$permission` | **int** | The permission to set (Unix file permissions). |

**Return Value:**

`bool` - True on success, false on failure.

***

### permissions

Converts file permission code to its string representation

```php
public static permissions(int $permission): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$permission` | **int** | Permission code |

**Return Value:**

`string` - Permission string representation.

***

### toUnixPermission

Convert permission string to Unix integer permission.

```php
public static toUnixPermission(string $permission): int|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$permission` | **string** | The permission string (e.g., 'rw-r--r--'). |

**Return Value:**

`int|null` - The Unix integer permission, or null if the conversion failed.

***

### write

Write or append contents to a file.

```php
public static write(string $filename, string|resource $content, int $flags, resource $context = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | — Path to the file where to write the data. |
| `$content` | **string&#124;resource** | The contents to write to the file, either as a string or a stream resource. |
| `$flags` | **int** | File flags, it can be combination flags joined with the binary OR (|) operator. |
| `$context` | **resource** | [optional] A valid context resource created with stream_context_create. |

**Return Value:**

`bool` - Return true if content was write successfully, otherwise false.

**Throws:**

- [\Luminova\Exceptions\FileException](/exceptions/classes#FileException) - If unable to write file.

***

### stream

Write or append contents to a file.

```php
public static stream(string $filename, resource $resource, int $flags, resource $context = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | — Path to the file where to write the data. |
| `$resource` | **resource** | The contents to write to the file as a stream resource. |
| `$flags` | **int** | [optional] The value of flags can be any combination of the following flags (with some restrictions), joined with the binary OR (|) operator. |
| `$context` | **resource** | [optional] A valid context resource created with stream_context_create. |

**Return Value:**

`bool` - Return true if the operation was successful, false otherwise.

**Throws:**

- [\Luminova\Exceptions\FileException](/exceptions/classes#FileException) - If unable to write file or invalid resource.

***

### mkdir

Attempts to create the directory, if it doesn't exist.

```php
public static mkdir(string $path, int $permissions = 0777, bool $recursive = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | Directory path to create. |
| `$permissions` | **int** | Unix file permissions |
| `$recursive` | **bool** | Allows the creation of nested directories specified in the pathname (default: true). |

**Return Value:**

`bool` - Return true if directory already existed or was created successfully, otherwise false.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes#RuntimeException) - If path is not readable.
- [\Luminova\Exceptions\FileException](/exceptions/classes#FileException) - If unable to create directory

***

### copy

Copy files and folders from the source directory to the destination directory.

```php
public static copy(string $origin, string $dest, int& $copied = 0): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$origin` | **string** | The source directory. |
| `$dest` | **string** | The destination directory. |
| `$copied` | **int** | Reference to a variable to store the number of copied items. |

**Return Value:**

`bool` - Return true if the copy operation is successful, false otherwise.

***

### move

Move files and folders from one location to another recursively.

```php
public static move(string $origin, string $dest, int& $moved = 0): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$origin` | **string** | The source directory or file path. |
| `$dest` | **string** | The destination directory path. |
| `$moved` | **int** | Reference to a variable to store the number of moved items. |

**Return Value:**

`bool` - Return true if the operation is successful, false otherwise.

***

### isResource

Checks if a variable is a valid resource of the specified type.

```php
public static isResource(mixed $resource, ?string $type = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$resource` | **mixed** | The resource to check. |
| `$type` | **string** | The expected resource type. Possible values are: 'stream-context', 'stream', etc. |

**Return Value:**

`bool` - Returns true if the variable is a resource and matches the expected resource type, otherwise returns false.

***

### download

Download a file to the user's browser.

```php
public static download(string $file, string|null $name = null, array $headers = [], bool $delete = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **string** | The full file path or content to to download. |
| `$name` | **string&#124;null** | The filename as it will be shown in the download. |
| `$headers` | **array** | Optional passed headers for download. |
| `$delete` | **bool** | Whether to delete the file after download (default: false). |

**Return Value:**

`bool` - Return true on success, false on failure.

***

### remove

Deletes files and folders recursively.

```php
public static remove(string $location, bool $delete_base = false, int& $deleted = 0): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$location` | **string** | Directory or file to delete. |
| `$delete_base` | **bool** | Remove the base directory once done (default is false). |
| `$deleted` | **int** | Reference to a variable to store the number of deleted items. |

**Return Value:**

`int` - Returns count of deleted files.

***

### symbolic

Create a symlink to a target file or directory.

```php
public static symbolic(string $target, string $link): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$target` | **string** | The targe file or directory to link from. |
| `$link` | **string** | The location of the link. |

**Return Value:**

`bool` - Return true if the link was successfully created false otherwise.