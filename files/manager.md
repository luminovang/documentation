# File System Management Utilities

***

## Overview

File Manager is a helper class shipped with Luminova Framework to handle file-related operations like downloading, reading or writing files, all methods are defined as static making it easier to call.

***

## Introduction

The Luminova `FileSystem` is a core component of the Luminova framework designed for managing file-related operations. It handles tasks such as retrieving system paths, downloading, reading, and writing files. With its `static` methods, `FileSystem` offers convenient and efficient file management within both the framework and your application.

***

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

- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - If permission is not granted and quiet is not passed true.

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
| `$flags` | **int** | File flags, it can be combination flags joined with the binary OR (&#124;) operator. |
| `$context` | **resource** | [optional] A valid context resource created with stream_context_create. |

**Return Value:**

`bool` - Return true if content was write successfully, otherwise false.

**Throws:**

- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - If unable to write file.

***

### stream

Write or append contents to a file.

```php
public static stream(string $filename, resource $resource, int $flags, resource $context = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | Path to the file where to write the data. |
| `$resource` | **resource** | The contents to write to the file as a stream resource. |
| `$flags` | **int** | [optional] The value of flags can be any combination of the following flags (with some restrictions), joined with the binary OR (&#124;) operator. |
| `$context` | **resource** | [optional] A valid context resource created with stream_context_create. |

**Return Value:**

`bool` - Return true if the operation was successful, false otherwise.

**Throws:**

- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - If unable to write file or invalid resource.

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

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If path is not readable.
- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - If unable to create directory

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

### size

To calculate the size of a given file or an entire directory.
If a directory is specified, it calculates the size of a given path recursively to determine total size of all files within the directory. 

```php
public static size(string $path): int 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **resource** | The path to the file or directory.. |

**Return Value:**

`int` - Return the size in bytes.

**Throws:**

[\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - Throws If the path does not exist.

***

### read

Reads a file in chunks with type-specific handling and customizable delay, for optimized performance.

This method reads large files efficiently by determining the file type based on its MIME type and applying the appropriate reading strategy (text or binary).

For text-based files, it reads while preserving line endings. For binary files, it reads in raw chunks.

```php
public static read(
	$handler, 
	int $filesize, 
	string $mime, 
	int $length = (1 << 21),
	int $delay = 100000
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **resource** | The file handler. |
| `$filesize` | **int** | The size of the file. |
| `$mime` | **string** | The MIME type of the file.. |
| `$length` | **int** | The size of each chunk to be read (default: 2MB). |
| `$delay` | **int** | The delay in microseconds between chunk reads (default: 100000). |

**Return Value:**

`bool` - Return true if the file was successfully read, false otherwise.

**Example**
```php
<?php 
$filename = 'large-file.pdf';
if ($handler = fopen($filename, 'rb')) {
	$fileSize = filesize($filename);
	$mime = get_mime($filename);

	FileManager::read($handler, $fileSize, $mime);
}

```

***

### readBinary

Reads binary files in chunks with a customizable delay. This method is suitable for reading `PDF`, `AUDIO`, `IMAGES` and other types of files that preserving lines ending isn't necessary.

```php
public static readBinary($handler, int $length = (1 << 21), int $delay = 100000): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **resource** | The file handler. |
| `$length` | **int** | The length of each chunk (default: 2MB in bytes). |
| `$delay` | **int** | The delay in microseconds between each chunk read (default: 100000, 0.1 second). |

**Return Value:**

`bool` - Return true if the file was successfully read, false otherwise.

***

### readText

Reads text-based files in chunks with a customizable delay while preserving line endings. This method is suitable for handling `TEXT`, `MD`, `LOG` and other text-based files.

```php
public static readText($handler, int $filesize, int $length = (1 << 21), int $delay = 100000): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **resource** | The file handler. |
| `$filesize` | **int** | The size of the file. |
| `$length` | **int** | The length of each chunk (default: 2MB in bytes). |
| `$delay` | **int** | The delay in microseconds between each chunk read (default: 100000, 0.1 second). |

**Return Value:**

`bool` - Return true if the file was successfully read, false otherwise.

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

### isAccessible

Validate if the provided file path is safe, exists, and is readable.
This method checks if the file path is valid, whether it is accessible, and ensures it's readable unless it's a `UNC` path.

```php
public static isAccessible(string $path): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The file path to validate (relative or absolute). |

**Return Value:**

`bool` - Returns true if the file is accessible and readable.

***

### isPathPermitted

Verify if the file path follows the allowed format and is not a URL or PHP Archive (Phar).
This method ensures that the file path does not use a URL scheme or a `phar` path, restricting access to local files only.

```php
public static isPathPermitted(string $path): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The file path to check (relative or absolute). |

**Return Value:**

`bool` - Returns true if the path is a valid local file path.

***

### download

Download a file to the user's browser with optional delay between chunks.

```php
public static download(
	string|resource $content, 
	?string $filename = null, 
	array $headers = [], 
	bool $delete = false,
	int $chunk_size = 8192,
	int $delay = 100000
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string\|resource** | The full file path, resource, or string to download. |
| `$filename` | **string&#124;null** | The filename as it will be shown in the download (e.g, `image.png`). |
| `$headers` | **array<string,mixed>** | Optional array headers for download.. |
| `$delete` | **bool** | Whether to delete the file after download (default: false). |
| `$chunk_size` | **int** | The size of each chunk in bytes for large content (default: 8192, 8KB). |
| `$delay` | **int** | The delay between each chunk in microseconds (default: 100000, 0.1 second). |

**Return Value:**

`bool` - Return true on success, false on failure.

**Supported Download Content `$content`**

- **File path** - Download content from path specified.
- **Resource** - Download content from resource specified.
- **String** - Download content from string specified.

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